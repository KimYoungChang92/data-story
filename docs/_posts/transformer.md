# Implement Transformer In Keras 
*attention is all your need*

Transformer is an important NLP area break-through from paper *[Attention Is All You Need](https://arxiv.org/pdf/1706.03762.pdf)* in 2017. It uses attention mechansim to replace recurrent and convolutionary layers in sequence deep learning.
Encoder-Decoder architect is the classic framework for seq2seq learning, such as neural machine translate, which maps a sentence to another sentence.
Before transformer came out, the standard way of encoder-decoder is using 

* Embedding layers to represent discrete words into low dimensional dense vectors
* LSTM/GRU as encoder & decoder, to learn the sequential patterns
* decoder-encoder attention mechansim to give different priority on encoder outputs 

Transformer, instead has:

* self-attention: to extract features from encoder/decoder itself.
* multi-head attention: increase the parallism of the model
* position-encoding: to capture the position of tokens, since no convo or recurrent layers are applied
* residual-connection: to increase model capability of generality, allow deeper model.

## position encoding

## scaled dot product attention

## multi-head attention

## position-wise feed forward

## skip connection

## encoder block

## decoder block
