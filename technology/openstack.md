OpenStack
=========

![OpenStack](http://docs.openstack.org/infra/publications/overview/graphics/openstack-cloud-software-horizontal-small.png)


## Links

### Tools

  * [Devstack configurations](https://github.com/gbraad/openstack-devstack-configurations)
  * [Packstack answerfiles](https://github.com/gbraad/openstack-packstack-answerfiles)
  * [Heat templates](https://github.com/gbraad/openstack-heat-templates)
  * [OpenStack client](https://github.com/gbraad/docker-openstack-client)


### Documentation

  * [Hands-on-labs](https://github.com/gbraad/openstack-handsonlabs)
  * [Tools training](https://github.com/gbraad/tools-training)
  * [Open Source Culture](https://github.com/gbraad/open-source-culture)


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
