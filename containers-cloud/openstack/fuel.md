Fuel
====


Development
-----------

  * https://ide.c9.io/gbraad/openstack-fuel


### Source
```
$ git clone https://github.com/openstack/fuel-main
$ git clone https://github.com/openstack/fuel-web
$ git clone https://github.com/openstack/fuel-ui
$ git clone https://github.com/openstack/fuel-agent
$ git clone https://github.com/openstack/fuel-astute
$ git clone https://github.com/openstack/fuel-ostf
$ git clone https://github.com/openstack/fuel-library
$ git clone https://github.com/openstack/fuel-docs
```


### Make ISO
On Ubuntu 14.04

```
$ git clone https://github.com/openstack/fuel-main
$ cd fuel-main
$ ./prepare-build-env.sh
$ make iso
```

[Source](https://docs.fuel-infra.org/fuel-dev/)


### Virtualized environment (for testing)

#### Preparation
Enable KVM nested virtualization

##### Packages
```
$ sudo apt-get install -y git postgresql postgresql-server-dev-all \
    libyaml-dev libffi-dev python-dev qemu-kvm libvirt-bin \
    libvirt-dev ubuntu-vm-builder bridge-utils \
    libpq-dev libgmp-dev \
    python-pip python-virtualenv
```

##### Database
```
$ sudo -u postgres createuser -P fuel_devops  # use "fuel_devops" as the password!
$ sudo -u postgres createdb fuel_devops -O fuel_devops
```

#### Create environment
```
$ git clone https://github.com/openstack/fuel-qa
$ cd fuel-qa
$ virtualenv .venv
$ source .venv/bin/activate
$ pip install -r ./fuelweb_test/requirements.txt
```

Setup the database
```
$ django-admin.py syncdb --settings=devops.settings
$ django-admin.py migrate devops --settings=devops.settings
```

To create the Fuel node use an ISO as described in the previous topic, or download from [here](http://9f2b43d3ab92f886c3f0-e8d43ffad23ec549234584e5c62a6e24.r60.cf1.rackcdn.com/MirantisOpenStack-9.0.iso)
```
$ export NODES_COUNT=5
$ ./utils/jenkins/system_tests.sh -t test -w $(pwd) -j fuel_test -k -K \
    -i MirantisOpenStack-9.0.iso -V .venv -e "Deployment test" -o \
    --group=prepare_release
```

Create slaves
```
$ ./utils/jenkins/system_tests.sh -t test -w $(pwd) -j fuel_test -k -K \
    -i MirantisOpenStack-9.0.iso -V .venv -e "Deployment test" -o \
    --group=prepare_slaves_5
```

You should be able to login to the Fuel UI, at http://10.109.0.2 using admin/admin.
