# apache-superset-installation-on-ubuntu20.04
* official-web-site : https://superset.apache.org/


## Installation

The following command will ensure that the required dependencies are installed:
```sh 
sudo apt-get install build-essential libssl-dev libffi-dev python3-dev python3-pip libsasl2-dev libldap2-dev default-libmysqlclient-dev
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


## Optional > Superset run as a service
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

## Optional > Superset - Oracle DB Connection

1- Oracle client instance installation
web site : https://www.oracle.com/tr/database/technologies/instant-client/linux-x86-64-downloads.html

Firstly, make a directory for client and download client zip file then extract here
```sh 
mkdir -p /opt/oracle
cd /opt/oracle
wget https://download.oracle.com/otn_software/linux/instantclient/216000/instantclient-basic-linux.x64-21.6.0.0.0dbru.zip
unzip instantclient-basic-linux.x64-21.6.0.0.0dbru.zip
```

You should create tnsnames.ora file for connection in admin dir.
```sh
cd instantclient_21_6/network/admin
nano tnsnames.ora
```
Insert these lines into tnsnames.ora, but you should change configuration for your DB info.
```sh
orac_test =
(DESCRIPTION =
(ADDRESS =(PROTOCOL = TCP)
(HOST = 0.0.0.0)
(PORT = 1521))
(CONNECT_DATA =(SERVER = DEDICATED)
(SID = server_name)))
```
Now, should append a few PATH into bashrc
```sh
cd /home/user/
nano .bashrc
```
Insert these lines into bashrc
```sh
export LD_LIBRARY_PATH=/opt/oracle/instantclient_21_6
export PATH=$PATH:$LD_LIBRARY_PATH
export TNS_ADMIN=/opt/oracle/instantclient_21_6/network/admin
export PATH=$PATH:$TNS_ADMIN
```
source command to force Linux to reload the .bashrc file 
```sh
source .bashrc
```
2- Install cx_oracle 
cx_oracle is a python library use for Oracle db connection.
```sh
pip install cx_oracle
```

**Finally**, you should restart superset.
```sh
sudo systemctl restart nifi.service
```