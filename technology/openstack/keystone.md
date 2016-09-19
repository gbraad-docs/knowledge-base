OpenStack Keystone
==================


## Container

  * https://gitlab.com/gbraad/openstack-keystone
  * https://github.com/gbraad/docker-openstack-keystone
  * https://hub.docker.com/r/gbraad/openstack-keystone

Keystone development environment
--------------------------------

### Requirements

#### Distribution packages
```
$ apt-get update
$ apt-get install -y mariadb-server mariadb-client
$ apt-get install -y python-pip python-virtualenv
$ apt-get install -y python-dev python3-dev libxml2-dev libxslt1-dev libsasl2-dev libsqlite3-dev libmysqlclient-dev libssl-dev libldap2-dev libffi-dev
```

#### Python packages
```
$ pip install -U pip
$ pip install -U pyopenssl ndg-httpsclient pyasn1
$ pip install repoze.lru pbr mysql-python
$ pip install python-keystoneclient python-openstackclient
$ pip install uwsgi
```


### Setup database

```
$ service mysql start
```

```
$ mysql  -u root -ppass -e "create database keystone;"
$ mysql  -u root -ppass -e "GRANT ALL PRIVILEGES ON keystone.* TO 'keystone'@'localhost' IDENTIFIED BY 'keystone';"
$ mysql  -u root -ppass -e "GRANT ALL PRIVILEGES ON keystone.* TO 'keystone'@'%' IDENTIFIED BY 'keystone';"
```

### Setup keystone for development


#### Getting the source
```
$ git clone https://github.com/openstack/keystone.git -b stable/mitaka
```


#### Choose: Setup development environment in `virtualenv`
```
$ tox -e venv --notest
$ source .tox/venv/bin/activate
$ pip install MySQL
```

#### or: Setup development outside `virtualenv`
```
$ pip install -r requirements.txt
$ pip install -r test-requirements.txt
$ python setup.py develop
```

### Verify installation
```
$ python -c "import keystone"
```

### Configuration
```
$ export TOKEN=$(openssl rand -hex 10)
$ cp etc/keystone.conf.sample etc/keystone.conf
$ sed -i "s|database]|database]\nconnection = mysql://keystone:keystone@localhost/keystone|g" etc/keystone.conf
$ sed -i 's/#admin_token = ADMIN/admin_token = $TOKEN/g' etc/keystone.conf
```

```
$ keystone-manage db_sync
```


### Run Keystone
```
$ sudo -u keystone /usr/local/bin/keystone-all --config-file=/etc/keystone/keystone.conf  --log-file=/var/log/keystone/keystone.log
```


### Insert records

```
$ keystone --os-token $TOKEN --os-endpoint http://localhost:35357/v2.0/ service-create --name=keystone --type=identity --description="Keystone Identity Service"
$ keystone --os-token $TOKEN --os-endpoint http://localhost:35357/v2.0/ endpoint-create --service keystone --publicurl 'http://localhost:5000/v2.0' --adminurl 'http://localhost:35357/v2.0' --internalurl 'http://localhost:5000/v2.0'
```

```
$ keystone --os-token $TOKEN --os-endpoint http://localhost:35357/v2.0/ user-create --name admin --pass password
$ keystone --os-token $TOKEN --os-endpoint http://localhost:35357/v2.0/ role-create --name admin
$ keystone --os-token $TOKEN --os-endpoint http://localhost:35357/v2.0/ tenant-create --name admin
$ keystone --os-token $TOKEN --os-endpoint http://localhost:35357/v2.0/ user-role-add --user admin --role admin --tenant admin
```


Setup keystone for use
----------------------
Same as above until 'Getting the source'

### Create directories

```
$ useradd --home-dir "/var/lib/keystone" --create-home --system --shell /bin/false keystone
$ mkdir -p /var/log/keystone
$ mkdir -p /etc/keystone
$ chown -R keystone:keystone /var/log/keystone
$ chown -R keystone:keystone /var/lib/keystone
$ chown keystone:keystone /etc/keystone
```

### Configuration
```
$ cd keystone
```

```
$ cp -R etc/* /etc/keystone/
$ mv  /etc/keystone/keystone.conf.sample /etc/keystone/keystone.conf
$ sed -i "s|database]|database]\nconnection = mysql://keystone:keystone@localhost/keystone|g" /etc/keystone/keystone.conf
$ sed -i 's/#admin_token = ADMIN/admin_token = SuperSecreteKeystoneToken/g' /etc/keystone/keystone.conf
```

### Install
```
$ python setup.py install
$ keystone-manage db_sync
```

```
$ cat >> /etc/logrotate.d/keystone << EOF
/var/log/keystone/*.log {
    daily
    missingok
    rotate 7
    compress
    notifempty
    nocreate
}
EOF
```

### Run Keystone

```
$ sudo -u keystone /usr/local/bin/keystone-all --config-file=/etc/keystone/keystone.conf  --log-file=/var/log/keystone/keystone.log
```
