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
```

![airflow-webserver](https://github.com/6chaoran/data-story/raw/master/data-tools/airflow/airflow-webui.png)

if you also encountered error:
`OSError: [Errno 2] No such file or directory`, just make sure the python path is added correctly.

use `which python` to check your python installation path.
use `vim ~/.bashrc` to add the python path, e.g. `export PATH=$PATH:xxx`


https://blog.csdn.net/aubdiy/article/details/73930865

you will be able see the Airflow Web UI. Click the toggle on the left to unpause the job. Click the first buttion on the right Links panel to manually trigger a job.
you will notice that, the job becomes running state, but never move to progress.

open another terminal window:
```
airflow sheduler
```

### 3. test your DAG
There are sample DAGs pre-defined after installation.

```
airflow list_dags
``` 

![airflow-webserver](https://github.com/6chaoran/data-story/raw/master/data-tools/airflow/airflow-listdags.png)

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
