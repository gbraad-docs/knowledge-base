# OpenStack client


[Documentation](http://docs.openstack.org/developer/python-openstackclient/)


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


## Examples
```
wget -q http://cloud.centos.org/centos/7/images/CentOS-7-x86_64-GenericCloud.qcow2 -O /tmp/centos7.qcow2
qemu-img convert /tmp/centos7.qcow2 /tmp/centos7.raw
#openstack image create --disk-format qcow2 --file /tmp/centos7.qcow2 centos7
openstack image create --disk-format raw --container-format bare --file /tmp/centos7.raw centos7
net_id=$(openstack network list -f value |awk '{print $1}')
openstack server create --flavor m1.small --image centos7 --nic net-id=${net_id} --key-name mykey test-instance
```
