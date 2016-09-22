# OpenStack Devstack


  * [Devstack configurations](https://github.com/gbraad/openstack-devstack-configurations)


## Installation
On Ubuntu 14.04

```
$ sudo apt-get update
$ sudo apt-get install git
$ git clone https://git.openstack.org/openstack-dev/devstack
```

```
$ tools/create-stack-user.sh; su stack
$ vi local.conf
```
    
    [[local|localrc]]
    ADMIN_PASSWORD=secret
    DATABASE_PASSWORD=$ADMIN_PASSWORD
    RABBIT_PASSWORD=$ADMIN_PASSWORD
    SERVICE_PASSWORD=$ADMIN_PASSWORD
    
```
$ ./stack.sh
```
