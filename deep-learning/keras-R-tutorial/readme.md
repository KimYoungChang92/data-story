
## Keras for R
The basic tutorial of Keras for R is recorded [here](https://keras.rstudio.com/index.html).

### Keras Installation

```r
devtools::install_github("rstudio/keras")
library(keras)
install_keras()
```

### MNIST handwriting recognition

```r
library(keras)
# donwnload dataset
mnist <- dataset_mnist()
x_train <- mnist$train$x
y_train <- mnist$train$y
x_test <- mnist$test$x
y_test <- mnist$test$y

# reshape
x_train <- array_reshape(x_train, c(nrow(x_train), 784))
x_test <- array_reshape(x_test, c(nrow(x_test), 784))
# rescale
x_train <- x_train / 255
x_test <- x_test / 255

# one-hot encoding the label
y_train <- to_categorical(y_train, 10)
y_test <- to_categorical(y_test, 10)

# define the computational graph
model <- keras_model_sequential() 
model %>% 
  layer_dense(units = 256, activation = 'relu', input_shape = c(784)) %>% 
  layer_dropout(rate = 0.4) %>% 
  layer_dense(units = 128, activation = 'relu') %>%
  layer_dropout(rate = 0.3) %>%
  layer_dense(units = 10, activation = 'softmax')

# compile model
model %>% compile(
loss = 'categorical_crossentropy',
optimizer = optimizer_rmsprop(),
metrics = c('accuracy'))

# model training
history <- model %>% fit(
  x_train, y_train, 
  epochs = 30, batch_size = 128, 
  validation_split = 0.2
)
```

## Generator
Generators are useful tools to build the training pipelines, such as functions like data sampling and preprocessing before feeding into model training.
A particular advantage of generator is the case that training data is too large to fit into memory. We can prepare the training data files in parts and use generator to read data, preprocess and eventually feed into model for training.
`generator` in R is to define a function within a function, which is slightly different with python and syntax is record in this [website](https://rstudio.github.io/reticulate/articles/introduction.html#generators). 
In this example, we can define a simple sampling generator, which serves as preprocessing pipelines - linking raw data to our expected data format by doing sampleing, re-scaling, re-shaping and one-hot encoding.
```r
sampling_generator <- function(x,y,batch_size) {
  function() {
  	# sample a batch
    inSample <- sample(1:nrow(x), batch_size)
    x.sample <- x[inSample, ,]
    # re-scale
    x.sample <- x.sample / 255
    # re-shape
    x.sample <- array_reshape(x.sample, c(length(inSample), 784))
    # one-hot encoding
    y.sample <- to_categorical(y[inSample],10)
    # return format (feature, label)
    return(list(x.sample, y.sample))
  }
}

```

## Callback
Callbacks are used to control the training process, such as saving the model, early stop, reducing learning rate. 
The full list of callbacks are show [here](https://keras.rstudio.com/articles/training_callbacks.html).
```r
callbacks <- list(
  # record training history in csv file
  callback_csv_logger(format(Sys.time(),'model/log/mnist_%Y%m%d_%H%M.csv')),
  # early stop if eval_loss not improving for 5 epochs
  callback_early_stopping(patience = 5),
  # logdir for tensorboard
  callback_tensorboard(format(Sys.time(),'model/tensorboard/mnist_%Y%m%d_%H%M')))
```

## Tensorboard
Tensorboard is nice UI view to compare different models as well as the model structure visualization.
To launch your tensorboard using:
```bash
tensorboard --logdir=/model/tensorboard/
```
after that, tensorboard will be available at http://localhost:6006.

## Training with generator

```r
valid.set <- my_generator(x_test,y_test,batch_size = nrow(x_test))()

hist <- model %>%
  fit_generator(
    my_generator(x_train,y_train,batch_size = 128),
    callbacks = callbacks,
    steps_per_epoch = 500,
    epochs = 50,
    verbose = 1,
    validation_data = valid.set,
    validation_steps = 1)
```