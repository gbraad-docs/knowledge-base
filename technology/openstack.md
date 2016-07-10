OpenStack
=========

![OpenStack](http://docs.openstack.org/infra/publications/overview/graphics/openstack-cloud-software-horizontal-small.png)


## Setup configuration

  * [Devstack configurations](https://github.com/gbraad/openstack-devstack-configurations)
  * [Packstack answerfiles](https://github.com/gbraad/openstack-packstack-answerfiles)
  * [Heat templates](https://github.com/gbraad/openstack-heat-templates)
  * [OpenStack client](https://github.com/gbraad/docker-openstack-client)
 

## Documentation

  * [Hands-on-labs](https://github.com/gbraad/openstack-handsonlabs)
  * [Tools training](https://github.com/gbraad/tools-training)
  * [Open Source Culture](https://github.com/gbraad/open-source-culture)


## Projects

  * [Aodh](http://docs.openstack.org/developer/aodh/)
  * [Ceilometer](http://docs.openstack.org/developer/ceilometer/)
  * [Cinder](http://docs.openstack.org/developer/cinder/)
  * [Designate](http://docs.openstack.org/developer/designate/)
  * [Glance](http://docs.openstack.org/developer/glance/)
  * [Gnocchi](http://docs.openstack.org/developer/gnocchi/)
  * [Heat](http://docs.openstack.org/developer/heat/)
  * [Horizon](http://docs.openstack.org/developer/horizon/)
  * [Ironic](http://docs.openstack.org/developer/ironic/)
  * [Keystone](http://docs.openstack.org/developer/keystone/)
  * [Magnum](http://docs.openstack.org/developer/magnum/)
  * [Manila](http://docs.openstack.org/developer/manila/)
  * [Mistral](http://docs.openstack.org/developer/mistral/)
  * [Murano](http://docs.openstack.org/developer/murano/)
  * [Nova](http://docs.openstack.org/developer/nova/)
  * [Neutron](http://docs.openstack.org/developer/neutron/)
  * [Swift](http://docs.openstack.org/developer/swift/)
  * [Tempest](http://docs.openstack.org/developer/tempest/)
  * [Trove](http://docs.openstack.org/developer/trove/)
  * [Zaqar](http://docs.openstack.org/developer/zaqar/)


## Infrastructure components

  * [Ceph](http://ceph.com/) implementation for Cinder, Glance and Nova
  * [Openvswitch](http://openvswitch.org/) and Linuxbridge backends for Neutron
  * [MongoDB](https://www.mongodb.org/) as a database backend for Ceilometer and Gnocchi
  * [RabbitMQ](https://www.rabbitmq.com/) as a messaging backend for communication between services.
  * [HAProxy](http://www.haproxy.org/) and [Keepalived](http://www.keepalived.org/) for high availability of services and their endpoints.
  * [MariaDB and Galera](https://mariadb.com/kb/en/mariadb/galera-cluster/) for highly available MySQL databases
  * [Heka](http://hekad.readthedocs.org/) A distributed and scalable logging system for openstack services.


## Needed to install OpenStack client on Ubuntu 14.04 (or C9)

```
pip install -U pyopenssl ndg-httpsclient pyasn1
```

## OpenStack client

```
pip install python-openstackclient
```

```
dnf install python-openstackclient
```

```
apt-get install python-openstackclient
```

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


## Dealing with values from the table output

```
for i in $( [command] | grep [filter] | awk ' { print $2 } '); do
  [command] $i
done
```


## TripleO quickstart

`deploy-config.yml`
```
overcloud_nodes:
  - name: control_0
    flavor: control
  - name: control_1
    flavor: control
  - name: control_2
    flavor: control

  - name: compute_0
    flavor: compute
  - name: compute_1
    flavor: compute
  - name: compute_2
    flavor: compute

  - name: storage_0
    flavor: ceph
  - name: storage_1
    flavor: ceph
  - name: storage_2
    flavor: ceph

extra_args: "--control-scale 3 --ceph-storage-scale 3 -e /usr/share/openstack-tripleo-heat-templates/environments/puppet-pacemaker.yaml --ntp-server pool.ntp.org"
```

`deploy`
```
#!/bin/sh
export VIRTHOST=pps10.spotsnel.net
./quickstart.sh --tags all --config deploy-config.yml --undercloud-image-url file:///var/lib/oooq-images/undercloud-mitaka.qcow2 $VIRTHOST
```

`undercloud`
```
#!/bin/sh
ssh -F .quickstart/ssh.config.ansible undercloud
```

```
vi deploy-config.yml
curl -sSL https://raw.githubusercontent.com/openstack/tripleo-quickstart/master/quickstart.sh -o quickstart.sh
./deploy
./undercloud
```

Information for TripleO is collected in a separate scratchpad:

  * [TripleO scratchpad](//github.com/gbraad/openstack-tripleo-scratchpad/)
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
