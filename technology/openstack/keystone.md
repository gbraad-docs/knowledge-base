Keystone
========

** WIP **

```
$ docker run -d --name keystone-database -e MYSQL_ROOT_PASSWORD=secrete mariadb:10.1
$ docker exec keystone-database mysql -psecrete -e "create database keystone;"
$ docker exec keystone-database mysql -psecrete -e "GRANT ALL ON keystone.* TO 'keystone'@'%' IDENTIFIED BY 'password';"
$ docker exec keystone-database mysql -psecrete -e "GRANT ALL ON keystone.* TO 'keystone'@'localhost' IDENTIFIED BY 'password';"
$ docker exec keystone-database mysql -psecrete -e "flush privileges;"
```

```
$ export ADMIN_TOKEN=$(openssl rand -hex 10)
$ docker run -it --name keystone-server gbraad/openstack-keystone bash
```

```
$ docker run -d --link keystone-database:dbhost -e ADMIN_TOKEN=$ADMIN_TOKEN -p 5000:5000 --name keystone-server gbraad/openstack-keystone
```
