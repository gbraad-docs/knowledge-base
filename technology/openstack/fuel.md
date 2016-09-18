Fuel
====


Development
-----------

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