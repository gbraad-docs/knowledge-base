# OpenStack PackStack


  * PackStack [Answerfiles](https://github.com/gbraad/openstack-packstack-answerfiles)

## Installation

### Step by step
`/etc/environment`
```
LANG=en_US.utf-8
LC_ALL=en_US.utf-8
```

```
$ sudo yum install -y centos-release-openstack-mitaka
$ sudo yum update -y
$ sudo yum install -y openstack-packstack
$ packstack --allinone
```

Note: consider disabling `NetworkManager`

For more info see: [Quickstart Packstack](https://www.rdoproject.org/install/quickstart/#Step_2:_Install_Packstack_Installer)


### Oneliners

   * OpenStack PackStack all-in-one  
     `curl -sSL https://raw.githubusercontent.com/gbraad/oneliners/master/install_packstack.sh | bash`
   * WeIRDO Packstack scenario   
     `curl -sSL https://raw.githubusercontent.com/gbraad/oneliners/master/run_weirdo_packstack_scenario001.sh | bash`


## Multinode configuration

Generate the default answerfile on the controller node

```
$ packstack --gen-answer-file=~/multinode.cfg
```

Update the following in the answerfile

```
CONFIG_CONTROLLER_HOSTS=192.168.1.205
CONFIG_COMPUTE_HOSTS=192.168.1.206
CONFIG_NETWORK_HOSTS=192.168.1.207
CONFIG_STORAGE_HOST=192.168.1.205
```

Install OpenStack using the answerfile

```
$ packstack --answer-file=~/multinode.cfg
```

In case you do not want to run the installation on already configured servers, add the following to the answerfile:

```
EXCLUDE_SERVERS=<serverIP>,<serverIP>,...
```


## Options to consider

```
CONFIG_HORIZON_SSL=y
CONFIG_PROVISION_DEMO=n
```
