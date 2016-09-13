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

```
$ docker exec keystone-server keystone --os-token $ADMIN_TOKEN --os-endpoint http://localhost:35357/v2.0/ service-create --name=keystone --type=identity --description="Keystone Identity Service"
$ docker exec keystone-server keystone --os-token $ADMIN_TOKEN --os-endpoint http://localhost:35357/v2.0/ endpoint-create --service keystone --publicurl 'http://localhost:5000/v2.0' --adminurl 'http://localhost:35357/v2.0' --internalurl 'http://localhost:5000/v2.0'
$ docker exec keystone-server keystone --os-token $ADMIN_TOKEN --os-endpoint http://localhost:35357/v2.0/ user-create --name admin --pass password
$ docker exec keystone-server keystone --os-token $ADMIN_TOKEN --os-endpoint http://localhost:35357/v2.0/ role-create --name admin
$ docker exec keystone-server keystone --os-token $ADMIN_TOKEN --os-endpoint http://localhost:35357/v2.0/ tenant-create --name admin
$ docker exec keystone-server keystone --os-token $ADMIN_TOKEN --os-endpoint http://localhost:35357/v2.0/ user-role-add --user admin --role admin --tenant admin
```