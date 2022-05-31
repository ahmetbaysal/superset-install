# apache-superset-installation-on-ubuntu20.04
* official-web-site : https://superset.apache.org/


## Installation

The following command will ensure that the required dependencies are installed:
```sh 
sudo apt-get install build-essential libssl-dev libffi-dev python3-dev python3-pip libsasl2-dev libldap2-dev default-libmysqlclient-dev```
```

We should do a few system configuration for instllation steps.
Change path to environments.
```sh
cd /etc
nano environment
```
Append this variable in last line
```sh
FLASK_APP=superset
```

start intalling via pip
```sh 
pip install apache-superset
```
need to initialize the database:
```sh 
superset db upgrade
```
create an admin user 
```sh 
export FLASK_APP=superset
superset fab create-admin
```
load some test data
```sh 
superset load_examples
```
Create default roles and permissions
```sh 
superset init
```
Start development web server on host and port 
```sh 
superset run -p 8088 -h 0.0.0.0 --with-threads --reload --debugger
```


## Optional - Superset run as a service
Change directory and create a service file
```sh 
cd /etc/systemd/system
nano superset.service
```
Service configuration should be like this
```sh 
[Unit]
Description = Apache Superset Webserver Daemon
After = network.target

[Service]
Environment= FLASK_APP=superset
ExecStart = superset run -p 8088 -h 0.0.0.0 --with-threads
ExecStop = /bin/kill -s TERM $MAINPID

[Install]
WantedBy=multi-user.target
```
Activate, status, run and stop service
```sh 
sudo systemctl daemon-reload
sudo systemctl enable superset.service
sudo systemctl status superset.service
sudo systemctl start superset.service
sudo systemctl stop superset.service
```


