# Install Airflow 1.9 on Ubuntu 18.04 LTS

## Install Airflow

[Airflow official website](https://airflow.apache.org/installation.html)

```
pip install apache-airflow==1.9.0
```

once installation is completed, type `airflow version` to verify.
![](https://github.com/6chaoran/data-story/raw/master/data-tools/airflow/airflow-version.png)

## Configure Airflow

### 1.initialize the database

initialize the default database using

```
airflow initdb
```

