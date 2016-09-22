# OpenStack PackStack


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

### Oneliners

   * OpenStack PackStack all-in-one  
     `curl -sSL https://raw.githubusercontent.com/gbraad/oneliners/master/install_packstack.sh | bash`
   * WeIRDO Packstack scenario   
     `curl -sSL https://raw.githubusercontent.com/gbraad/oneliners/master/run_weirdo_packstack_scenario001.sh | bash`
