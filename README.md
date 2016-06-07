Personal scratchpad
===================

Just a collection of some random notes

_Gerard Braad <me@gbraad.nl>_


## OpenStack

### Needed to install OpenStack client on Ubuntu 14.04 (or C9)

```
pip install -U pyopenssl ndg-httpsclient pyasn1
```

### OpenStack client

```
pip install python-openstackclient
```

### TripleO quickstart

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


### PackStack scenario001 test using WeIRDO

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


### Basic commands

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


## Git

### Delete commits from a branch
Careful: git reset --hard WILL DELETE YOUR WORKING DIRECTORY CHANGES

```
  git reset --hard HEAD~1
```

### Set vim as default editor
Needed on Ubuntu and C9 containers

```
git config --global core.editor "vim"
```

```
export GIT_EDITOR=vim
```

### Update all subdirectories
Used for a project directory with no `git submodule`.

```
#!/bin/bash
for i in $(find . -maxdepth 1 -type d)
do
    pushd $i
    git checkout master
    git pull --reb
    popd
done
```


## Install software

### Install PackStack (bash)
```
curl -sSL https://raw.githubusercontent.com/gbraad/oneliners/master/install_packstack.sh | bash
```

### Install C9 (bash)
```
curl -sSL https://raw.githubusercontent.com/gbraad/oneliners/master/install_c9.sh | bash
```

### Install C9 (ansible)
```
curl -sSL https://github.com/gbraad/ansible-playbooks/raw/master/playbooks/install-c9sdk.yml -o install-c9sdk.yml
ansible-playbook install-c9sdk.yml
```
