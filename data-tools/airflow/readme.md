# Install Airflow 1.9 on Ubuntu 18.04 LTS

## 2. Install Airflow

[Airflow official website](https://airflow.apache.org/installation.html)

```
pip install apache-airflow==1.9.0
```

once installation is completed, type `airflow version` to verify.
![airflow-version](https://github.com/6chaoran/data-story/raw/master/data-tools/airflow/image/airflow-version.png)

## 3.Configure Airflow

### 3.1.initialize the database

initialize the default database using

```
airflow initdb
```

![airflow-initdb](https://github.com/6chaoran/data-story/raw/master/data-tools/airflow/image/airflow-initdb.png)

### 3.2. start your webUI/scheduler


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

If everything is successful, you will be able see the Airflow Web UI, as follow:
![airflow-webserver](https://github.com/6chaoran/data-story/raw/master/data-tools/airflow/image/airflow-webui.png)

### 3.3. test your DAG
There are some sample DAGs pre-defined in airflow. 

* `airflow list_dags`, `airflow list_tasks` are useful commands to check the existing DAGs
* `airflow test`, `airflow run` and `airflow backfill` are useful commands to test your tasks.

Let's do some tests on the `tutorial` DAG:

#### a. list all regiestered DAGs:

```
airflow list_dags
``` 
![airflow-webserver](https://github.com/6chaoran/data-story/raw/master/data-tools/airflow/image/airflow-listdags.png)

#### b. list all tasks under `tutorial` DAG in tree strcture:

```
airflow list_task tutorial --tree
```
```
[2018-07-29 17:06:34,887] {__init__.py:45} INFO - Using executor SequentialExecutor
[2018-07-29 17:06:34,919] {models.py:189} INFO - Filling up the DagBag from /home/chaoran/airflow/image/dags
<Task(BashOperator): sleep>
    <Task(BashOperator): print_date>
<Task(BashOperator): templated>
    <Task(BashOperator): print_date>
```
#### c. you can test run `print_date` task in `tutorial` DAG, using `airflow test tutorial print date 2018-08-01`. The date can be arbitary.

run backfill from `2018-07-11` to `2018-07-12`:
```
airflow backfill tutorial -s 2018-07-11 -e 2018-07-12
```
you will notice the backfill job is registered in webUI as well:
![airflow-webui2](https://github.com/6chaoran/data-story/raw/master/data-tools/airflow/image/airflow-webui2.png)

## 4. Choise of Executor


Airflow offer different execution mode for different scenarios:

|Mode of Executor | Detail |
|-----------------|:-------|
|Sequential|single machine, single-process. Airflow out-of-box option.|
|Local|single machine, multi-process. need to use postgres database instead of sqlite|
|Celery|could be distributed in multi-machine. need to start workers to pick up tasks|

As I only have a single EC2 instance instead of airflow cluster, Local Executor mode will be the most appropreated.

### 4.1. Install Postregres Database

install postgres: `sudo apt-get install postgresql postgresql-contrib`


### 4.2. Setup Postegres Database

sign in database with superuser postgres: `sudo -u postgres psql`

```
CREATE ROLE ubuntu;
CREATE DATABASE airflow;
GRANT ALL PRIVILEGES on database airflow to ubuntu;
ALTER ROLE ubuntu SUPERUSER;
ALTER ROLE ubuntu CREATEDB;
GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA public TO ubuntu;
ALTER ROLE ubuntu LOGIN;
```
tips: don't forget `;`, when key the above command.
![airflow-postgre-db](https://github.com/6chaoran/data-story/raw/master/data-tools/airflow/image/airflow-postgre-db.png)

use `\du` to confirm the role ubuntu is properly set:

```
                                   List of roles
 Role name |                         Attributes                         | Member of 
-----------+------------------------------------------------------------+-----------
 postgres  | Superuser, Create role, Create DB, Replication, Bypass RLS | {}
 ubuntu    | Superuser, Create DB 				                        | {}
```

use `\q` to quit the postgres mode

change the listen port: `/etc/postgresql/9.*/main/pg_hba.conf`

* ipv4 address to `0.0.0.0/0`
* ipv4 connection `md5` to `trust`

In the meantime, we also need to configure the postgresql.conf file to open the listen address to all ip addresses:

listen_addresses = '*'.
And we need to start a postgresql service
`sudo service postgresql start`

### 4.3. Config Airflow.cfg file

use `vim ~/airflow/airflow.cfg` to modify config file:

* change the mode of executor: `executor = LocalExecutor`
* change database connection: `sql_alchemy_conn = postgresql+psycopg2://ubuntu@localhost:5432/airflow`

Finally, re-initialize the airflow database `airflow initdb`

### 4.4 Restart Aiflow scheduler/UI

```
airflow scheduler -D
airflow webserver -D
```
* tips: use `-D` flag to run daemonized process, which will alow program to run at background.
Now my airflow setup is completed, I just need write my own DAG file and drop into `~/airflow/dags`

## 5. Final Notes

## Reference:
----
[Installing Apache Airflow on Ubuntu/AWS – A.R.G.O. – Medium](https://medium.com/a-r-g-o/installing-apache-airflow-on-ubuntu-aws-6ebac15db211)