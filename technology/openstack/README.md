OpenStack
=========

![OpenStack](http://docs.openstack.org/infra/publications/overview/graphics/openstack-cloud-software-horizontal-small.png)


## Contents

  * [Client](client.md)
  * [Puppet](puppet.md)
  * [RDO](rdo.md)


## Contributions

  * [Devstack configurations](https://github.com/gbraad/openstack-devstack-configurations)
  * [Packstack answerfiles](https://github.com/gbraad/openstack-packstack-answerfiles)
  * [Heat templates](https://github.com/gbraad/openstack-heat-templates)
  * [Hands-on-labs](https://github.com/gbraad/openstack-handsonlabs)


## Projects

  * [Aodh](http://docs.openstack.org/developer/aodh/), provides alarms and notifications based on metrics
  * [Bifrost](http://docs.openstack.org/developer/bifrost/), automates the task of deploying a base image on baremetal using Ironic
  * [Ceilometer](http://docs.openstack.org/developer/ceilometer/), is a component of the Telemetry project. Its data can be used to provide customer billing, resource tracking, and alarming capabilities
  * [Cinder](http://docs.openstack.org/developer/cinder/) provides “block storage as a service”
  * [Designate](http://docs.openstack.org/developer/designate/) provides "DNS as a service"
  * [Glance](http://docs.openstack.org/developer/glance/) provides a service where users can upload and discover data assets that are meant to be used with other services
  * [Gnocchi](http://docs.openstack.org/developer/gnocchi/) is a multi-tenant timeseries, metrics and resources database
  * [Heat](http://docs.openstack.org/developer/heat/) is a service to orchestrate composite cloud applications using a declarative template format 
  * [Horizon](http://docs.openstack.org/developer/horizon/) is the canonical implementation of OpenStack’s Dashboard
  * [Ironic](http://docs.openstack.org/developer/ironic/) provisions bare metal (as opposed to virtual) machines by leveraging common technologies such as PXE boot and IPMI
  * [Keystone](http://docs.openstack.org/developer/keystone/) provides Identity, Token, Catalog and Policy services 
  * [Magnum](http://docs.openstack.org/developer/magnum/) offers container orchestration engines for deploying and managing containers
  * [Manila](http://docs.openstack.org/developer/manila/) provides “Shared Filesystems as a service”.
  * [Mistral](http://docs.openstack.org/developer/mistral/) provides a mechanism to define tasks and workflows without writing code, manage and execute them
  * [Murano](http://docs.openstack.org/developer/murano/) combines an application catalog with versatile tooling to simplify and accelerate packaging and deployment
  * [Nova](http://docs.openstack.org/developer/nova/) provides power massively scalable, on demand, self service access to compute resources
  * [Neutron](http://docs.openstack.org/developer/neutron/) provides “network connectivity as a service” between interface devices (e.g., vNICs)
  * [Swift](http://docs.openstack.org/developer/swift/) is a highly available, distributed, eventually consistent object/blob store
  * [Tempest](http://docs.openstack.org/developer/tempest/) is an integration test suite
  * [Trove](http://docs.openstack.org/developer/trove/) provides "database as a service"
  * [Zaqar](http://docs.openstack.org/developer/zaqar/) is a multi-tenant cloud messaging and notification service for web and mobile developers


## Infrastructure components

  * [Ceph](http://ceph.com/) implementation for Cinder, Glance and Nova
  * [Openvswitch](http://openvswitch.org/) and Linuxbridge backends for Neutron
  * [MongoDB](https://www.mongodb.org/) as a database backend for Ceilometer and Gnocchi
  * [RabbitMQ](https://www.rabbitmq.com/) as a messaging backend for communication between services.
  * [HAProxy](http://www.haproxy.org/) and [Keepalived](http://www.keepalived.org/) for high availability of services and their endpoints.
  * [MariaDB and Galera](https://mariadb.com/kb/en/mariadb/galera-cluster/) for highly available MySQL databases
  * [Heka](http://hekad.readthedocs.org/) A distributed and scalable logging system for openstack services.


## OpenStack Infra projects

  * [Shade](http://docs.openstack.org/infra/shade/) dlient library for interacting with OpenStack clouds


## OpenStack modules

  * [Ansible](http://docs.ansible.com/ansible/list_of_cloud_modules.html#openstack)
  * [Puppet](http://docs.openstack.org/developer/puppet-openstack-guide/module-list.html#puppet-openstack-modules)


## TripleO

Information for TripleO is collected in a separate scratchpad:

  * [TripleO scratchpad](tripleo.md)
  * [TripleO install](//gist.github.com/gbraad/073052c08457526463369b8b80890afa)


## PackStack scenario001 test using WeIRDO

```
#!/bin/bash
# How to run WeIRDO on localhost from a fresh CentOS installation
yum -y install git epel-release python-devel "@Development Tools"
yum -y install python-pip
pip install tox
git clone https://github.com/redhat-openstack/weirdo.git
cd weirdo
cat <<EOF >hosts
localhost ansible_connection=local

[openstack_nodes]
localhost log_destination=/var/log/weirdo
EOF
tox -e ansible-playbook -- -vv -i hosts playbooks/packstack-scenario001.yml --extra-vars openstack_release=mitaka
```

[WeIRDO localhost](//gist.github.com/gbraad/073052c08457526463369b8b80890afa)


## Bifrost

  * [Source](https://github.com/openstack/bifrost)
  

### Dynamic enrollment

```
$ BIFROST_INVENTORY_SOURCE=~/baremetal.json ansible-playbook -vvvv -i inventory/bifrost_inventory.py enroll-dynamic.yaml
```


## Kolla

  * [Chinese quickstart](https://github.com/hubchao/OpenStack_Deployment/blob/master/kolla_quickstart.rst)
