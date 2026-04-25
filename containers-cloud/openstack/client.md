# OpenStack client

  * [Documentation](http://docs.openstack.org/developer/python-openstackclient/)
  * `clouds.yaml`: [usage](http://docs.openstack.org/developer/python-openstackclient/configuration.html), [support](http://specs.openstack.org/openstack/openstack-specs/specs/clouds-yaml-support.html)


## Setup 


### Docker image
The easiest way is to use the Docker container I created: [OpenStack client](https://github.com/gbraad/docker-openstack-client)

```
$ alias stack='docker run -it --rm -v $PWD:/workspace -v ~/.stack:/root/.stack registry.gitlab.com/gbraad/openstack-client:centos stack'
$ alias openstack='docker run -it --rm -v $PWD:/workspace -v ~/.config/openstack:/root/.config/openstack registry.gitlab.com/gbraad/openstack-client:centos openstack'
```

Then create a configuration file at `.stack` for your environment, and prepend commands with:


```
$ stack trystack [command]
```


### Install
Alternatively you can use the following installation methods


### Using PIP
```
$ pip install python-openstackclient
```


#### On Fedora/RHEL/CentOS
```
$ yum install -y python-openstackclient   # dnf
```


#### On Ubuntu/Debian
```
$ apt-get install -y python-openstackclient
```


#### Using Ansible playbook
```
$ curl -sSL https://raw.githubusercontent.com/gbraad/ansible-playbooks/master/playbooks/install-openstack-client.yml > /tmp/install.yml
$ ansible-playbook /tmp/install.yml
```


## Managing users and projects
```
$ openstack project list

$ openstack user list

$ openstack role list
```


### Add new user
```
$ openstack user create <username> --project <project> --password <password>
```

### Grant role to user
```
$ openstack role add --user <username> --project <project> <role>
```

## Managing SSH keys

### Create a new key-pair
```
$ openstack keypair create mykey > mykey.pem
```

### Upload existing key
```
$ openstack keypair create --public-key ~/.ssh/id_rsa.pub mykey
```


## Managing images
```
$ openstack image list
```

### Upload image
```
# openstack image create <name> --disk-format <dformat> --container-format <cformat> --file <file> --property os_distro=<||>
```


## Managing instances
```
$ openstack flavor list

$ openstack server list
```

### Create instance
```
$ openstack server create <name> --flavor <flavor> --image <image> --key-name <key-name>
```

### Lifecycle
```
$ openstack server reboot <name>

$ openstack server delete <name>
```


## Managing volumes
```
$ openstack volume create <volume-name> --size <size>

$ openstack volume create <volume-name> --image <image> --size <size>

$ openstack server add volume <server-name> <volume-name>
```


## Managing networks
```
$ openstack network list

$ openstack network show <network>
```


### Create network
```
$ openstack network create [--prefix prefix] [--enable | --disable] [--share | --no-share] <name>
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


## Dealing with values from the table output

```
for i in $( [command] | grep [filter] | awk ' { print $2 } '); do
  [command] $i
done
```

