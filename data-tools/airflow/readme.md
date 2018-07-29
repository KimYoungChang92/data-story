# Install Airflow 1.9 on Ubuntu 18.04 LTS

## Install Airflow

[Airflow official website](https://airflow.apache.org/installation.html)

```
pip install apache-airflow==1.9.0
```

once installation is completed, type `airflow version` to verify.
![airflow-version](https://github.com/6chaoran/data-story/raw/master/data-tools/airflow/airflow-version.png)

## Configure Airflow

### 1.initialize the database

initialize the default database using

```
airflow initdb
```

![airflow-initdb](https://github.com/6chaoran/data-story/raw/master/data-tools/airflow/airflow-initdb.png)

### 2. start your webUI/scheduler


```
airflow webserver
# open another terminal and type
airflow scheduler
```

if you also encountered error:

`OSError: [Errno 2] No such file or directory`, just make sure the python path is added correctly.

* use `which python` to check your python installation path.
* use `vim ~/.bashrc` to add the python path, e.g. `export PATH=$PATH:xxx`

reference:[https://blog.csdn.net/aubdiy/article/details/73930865](https://blog.csdn.net/aubdiy/article/details/73930865)

you will be able see the Airflow Web UI, as follow:
![airflow-webserver](https://github.com/6chaoran/data-story/raw/master/data-tools/airflow/airflow-webui.png)

### 3. test your DAG
There are some sample DAGs pre-defined in airflow. 

* `airflow list_dags`, `airflow list_tasks` are useful commands to check the existing DAGs
* `airflow test`, `airflow run` and `airflow backfill` are useful commands to test your tasks.

Let's do some tests on the `tutorial` DAG:

list all regiestered DAGs:
```
airflow list_dags
``` 
![airflow-webserver](https://github.com/6chaoran/data-story/raw/master/data-tools/airflow/airflow-listdags.png)

list all tasks under `tutorial` DAG in tree strcture:
```
airflow list_task tutorial --tree
```
```
[2018-07-29 17:06:34,887] {__init__.py:45} INFO - Using executor SequentialExecutor
[2018-07-29 17:06:34,919] {models.py:189} INFO - Filling up the DagBag from /home/chaoran/airflow/dags
<Task(BashOperator): sleep>
    <Task(BashOperator): print_date>
<Task(BashOperator): templated>
    <Task(BashOperator): print_date>
```
you can test run `print_date` task in `tutorial` DAG, using `airflow test tutorial print date 2018-08-01`. The date can be arbitary.

run backfill from `2018-07-11` to `2018-07-12`:
```
airflow backfill tutorial -s 2018-07-11 -e 2018-07-12
```
you will notice the backfill job is registered in webUI as well:
![airflow-webui2](https://github.com/6chaoran/data-story/raw/master/data-tools/airflow/airflow-webui2.png)