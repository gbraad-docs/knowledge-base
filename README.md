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

[TripleO scratchpad](//github.com/gbraad/openstack-tripleo-scratchpad/)


### TripleO physical install
```
cat > /etc/hosts <<EOF
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
192.168.1.101  ooo1 ooo1.test.spotsnel.int
192.168.1.102  ooo2 ooo2.test.spotsnel.int
192.168.1.103  ooo3 ooo3.test.spotsnel.int
EOF

useradd stack
echo "stack" | passwd --stdin stack
echo "stack ALL=(root) NOPASSWD:ALL" | sudo tee -a /etc/sudoers.d/stack
sudo chmod 0440 /etc/sudoers.d/stack

su - stack
sudo yum -y install epel-release  yum-plugin-priorities 
sudo curl -o /etc/yum.repos.d/delorean.repo http://trunk.rdoproject.org/centos7/current-tripleo/delorean.repo
sudo curl -o /etc/yum.repos.d/delorean-current.repo http://trunk.rdoproject.org/centos7/current/delorean.repo
sudo sed -i 's/\[delorean\]/\[delorean-current\]/' /etc/yum.repos.d/delorean-current.repo
sudo /bin/bash -c "cat <<EOF>>/etc/yum.repos.d/delorean-current.repo
includepkgs=diskimage-builder,openstack-heat,instack,instack-undercloud,openstack-ironic,openstack-ironic-inspector,os-cloud-config,os-net-config,python-ironic-inspector-client,python-tripleoclient,tripleo-common,openstack-tripleo-heat-templates,openstack-tripleo-image-elements,openstack-tuskar-ui-extras,openstack-puppet-modules
EOF"

sudo curl -o /etc/yum.repos.d/delorean-deps.repo http://trunk.rdoproject.org/centos7/delorean-deps.repo
sudo yum install -y python-tripleoclient

cat > ~/undercloud.conf <<EOF
[DEFAULT]
local_interface = eth0
[auth]
EOF

openstack undercloud install 2>&1 | tee undercloud_install.log

export NODE_DIST=centos7
export USE_DELOREAN_TRUNK=1
export DELOREAN_TRUNK_REPO="http://trunk.rdoproject.org/centos7/current-tripleo/"
export DELOREAN_REPO_FILE="delorean.repo"
time openstack overcloud image build --all 2>&1 | tee build_images.log

openstack overcloud image upload

cat > instackenv.json << EOF
{
  "nodes":[
  {
    "_comment":"ooo2",
    "pm_type":"pxe_ipmi",
    "mac": [
        "ec:a8:6b:ca:fe:c7"
    ],
    "cpu": "2",
    "memory": "4000",
    "disk": "100",
    "arch": "x86_64",
    "pm_user":"admin",
    "pm_password":"admin",
    "pm_addr":"192.168.1.102"
  },
  {
    "_comment":"ooo3",
    "pm_type":"pxe_ipmi",
    "mac": [
        "b8:ae:ed:ca:fe:20"
    ],
    "cpu": "2",
    "memory": "4000",
    "disk": "100",
    "arch": "x86_64",
    "pm_user":"admin",
    "pm_password":"admin",
    "pm_addr":"192.168.1.103"
  }
  ]
}
EOF
json_verify < instackenv.json

openstack baremetal import --json instackenv.json
openstack baremetal configure boot

openstack baremetal introspection bulk start
openstack overcloud deploy --templates
```

[TripleO install](//gist.github.com/gbraad/073052c08457526463369b8b80890afa)


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
