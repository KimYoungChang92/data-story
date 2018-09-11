# set up superset on ubuntu 16.04 LTS

## create a virtualenv

Assume Anaconda is installed for python management.

```
# create a virtualenv with python 2.7
conda create -n superset python=2.7 
```

## install in virtualenv

```
# enter virtualenv
source activate superset

# install superset follow official installation guide
pip install superset

# Create an admin user (you will be prompted to set username, first and last name before setting a password)
fabmanager create-admin --app superset

# Initialize the database
superset db upgrade

# Load some data to play with
superset load_examples

# Create default roles and permissions
superset init

# To start a development web server on port 8088, use -p to bind to another port
superset runserver -d

```

## set up systemd service

```
cd /etc/systemd/system
sudo touch superset.service
sudo vim superset.service
```

write in following code

```
[Unit]
Description=Visualization platform by Airbnb
After=multi-user.target

[Service]
Type=simple
User=User
ExecStart=/some-path/anaconda2/envs/superset/bin/gunicorn -w 2 --timeout 60 -b  0.0.0.0:8088 --limit-request-line 0 --limit-request-field_size 0 superset:app

[Install]
WantedBy=default.target
```

## start systemd service

```
# start superset service
sudo systemctl start superset.service
```

```
# check service status
sudo systemctl status superset.service
```

now superset should be available at http://localhost:8088


