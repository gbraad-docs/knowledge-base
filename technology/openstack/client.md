# OpenStack client



## Setup 


### Docker image
The easiest way is to use the Docker container I created: [OpenStack client](https://github.com/gbraad/docker-openstack-client)

```
alias stack='docker run -it --rm -v ~/.stack:/root/.stack gbraad/openstack-client:centos stack'
```

Then create a configuration file at `.stack` for your environment, and prepend commands with:


```
stack trystack [command]
```


### Install
Alternatively you can use the following installation methods


### Using PIP
```
pip install python-openstackclient
```


#### On Fedora/RHEL/CentOS
```
yum install -y python-openstackclient   # dnf
```


#### On Ubuntu/Debian
```
apt-get install -y python-openstackclient
```


#### Using Ansible playbook
```
curl -sSL https://raw.githubusercontent.com/gbraad/ansible-playbooks/master/playbooks/install-openstack-client.yml > /tmp/install.yml
ansible-playbook /tmp/install.yml
```


## Basic commands

```
openstack keypair create mykey > mykey.pem
```

```
openstack keypair create --public-key ~/.ssh/id_rsa.pub mykey
```

```
openstack flavor list
```

```
openstack image list
```

```
openstack server create [name] --flavor [flavor] --image [image] --key-name [key-name]
```

```
openstack server list
```

```
openstack server reboot [name]
```

```
openstack server delete [name]
```

```
openstack volume create [volume-name] --size [size]
```

```
openstack volume create [volume-name] --image [image] --size [size]
```

```
openstack server add volume [server-name] [volume-name]
```
