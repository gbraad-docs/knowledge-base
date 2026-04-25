OpenStack TripleO
=================

[!["TripleO"](tripleo_owl-icon.png)](http://tripleo.org/)



## TripleO

  * [TripleO install](//gist.github.com/gbraad/073052c08457526463369b8b80890afa)


## Quickstart

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


## CI Jobs


### tripleo-quickstart-promote-master-delorean-minimal

    export VIRTHOST='my-cool-virthost.example.com'im
    bash quickstart.sh \
    -u "http://artifacts.ci.centos.org/artifacts/rdo/images/master/delorean/testing/undercloud.qcow2" \
    -t all \
    -c centosci/minimal.yml \
    $VIRTHOST


### Project tripleo-quickstart-promote-master-delorean-ha

    export VIRTHOST='my-cool-virthost.example.com'
    bash quickstart.sh \
    -u "http://artifacts.ci.centos.org/artifacts/rdo/images/master/delorean/testing/undercloud.qcow2" \
    -t all \
    -c centosci/ha.yml \
    $VIRTHOST

This runs the HA setup as defined in playbooks/centosci/ha.yml

    overcloud_nodes:
      - name: control_0
        flavor: control
      - name: control_1
        flavor: control
      - name: control_2
        flavor: control
      - name: compute_0
        flavor: compute

and puppet-pacemaker as extra vargs



### Project tripleo-quickstart-promote-master-delorean-build-images

```
# It is assumed that tripleo-quickstart is installed in the default
# virtualenv created by running quickstart.sh
# (however quickstart.sh does not yet support building images)

    source $HOME/.quickstart/bin/activate
    export VIRTHOST='my-cool-virthost.example.com'
    export ANSIBLE_CONFIG=$HOME/.quickstart/tripleo-quickstart/ansible.cfg

# For delorean, find the delorean hash from this job:
# https://ci.centos.org/job/rdo-promote-get-hash-master/

    export dlrn_hash=\
    ansible-playbook -i local_hosts $HOME/.quickstart/tripleo-quickstart/playbooks/build-images.yml \
    --extra-vars virthost=$VIRTHOST \
    --extra-vars artib_release=master \
    --extra-vars artib_build_system=delorean \
    --extra-vars artib_delorean_hash=$dlrn_hash

# The images will be in /var/lib/oooq-images dir on the virthost
```


### Ceph


  * https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux_OpenStack_Platform/7/html/Director_Installation_and_Usage/sect-Advanced-Scenario_3_Using_the_CLI_to_Create_an_Advanced_Overcloud_with_Ceph_Nodes.html


### Install using quickstart

Install ceph storage, for instance with

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
 
extra_args: "--control-scale 3 --compute-scale 3 --ceph-storage-scale 3 -e /usr/share/openstack-tripleo-heat-templates/environments/puppet-pacemaker.yaml --ntp-server pool.ntp.org"
```

This places the `ceph-mon` on all the _controler_ nodes and `ceph-osd` on the
nodes with _ceph_ as flavor.


### Use `ceph-ansible` to takeover existing deployment

  * https://www.sebastien-han.fr/blog/2016/05/02/Take-over-an-existing-Ceph-cluster-with-Ceph-Ansible/


## Composable Services

  * https://review.openstack.org/#/c/273754/5

  * https://etherpad.openstack.org/p/tripleo-composable-services
  * https://review.openstack.org/#/c/311512/12/doc/source/advanced_deployment/tht_walkthrough.rst
  * https://review.openstack.org/#/c/245804/

## Docker

  * https://hub.docker.com/u/tripleoupstream/


## Network

  * http://docs.openstack.org/developer/tripleo-docs/advanced_deployment/network_isolation.html
  * https://remote-lab.net/rdo-manager-ha-openstack-deployment


### Network isolation
"When isolated networks are configured, the OpenStack services will be
configured to use the isolated networks. If no isolated networks are configured,
all services run on the provisioning network."

Creating custom configuration is possible by modifying the templates. The
original templates are in `/usr/share/openstack-tripleo-heat-templates/network/config/`
A description can be found here: http://docs.openstack.org/developer/tripleo-docs/advanced_deployment/network_isolation.html#customizing-the-interface-templates

"When using numbered interfaces (“nic1”, “nic2”, etc.) instead of named
interfaces (“eth0”, “eno2”, etc.), the network interfaces of hosts within a
role do not have to be exactly the same. For instance, one host may have
interfaces em1 and em2, while another has eno1 and eno2, but both hosts’ NICs
can be referred to as nic1 and nic2."

"The numbered NIC scheme only takes into account the interfaces that are live
(have a cable attached to the switch). So if you have some hosts with 4
interfaces, and some with 6, you should use nic1-nic4 and only plug in 4 cables
on each host."


### Customization of the Heat templates

```
$ mkdir customized
$ cp -r /usr/share/openstack-tripleo-heat-templates/* customized/
```


### Network environment setup

```
$ vi environments/network-environment.yaml
```

  resource_registry:
    OS::TripleO::BlockStorage::Net::SoftwareConfig: /home/stack/nic-configs/cinder-storage.yaml
    OS::TripleO::Compute::Net::SoftwareConfig: /home/stack/nic-configs/compute.yaml
    OS::TripleO::Controller::Net::SoftwareConfig: /home/stack/nic-configs/controller.yaml
    OS::TripleO::ObjectStorage::Net::SoftwareConfig: /home/stack/nic-configs/swift-storage.yaml
    OS::TripleO::CephStorage::Net::SoftwareConfig: /home/stack/nic-configs/ceph-storage.yaml

  parameter_defaults:
    # Customize all these values to match the local environment
    InternalApiNetCidr: 172.17.0.0/24
    StorageNetCidr: 172.18.0.0/24
    StorageMgmtNetCidr: 172.19.0.0/24
    TenantNetCidr: 172.16.0.0/24
    ExternalNetCidr: 10.1.2.0/24
    # CIDR subnet mask length for provisioning network
    ControlPlaneSubnetCidr: '24'
    InternalApiAllocationPools: [{'start': '172.17.0.10', 'end': '172.17.0.200'}]
    StorageAllocationPools: [{'start': '172.18.0.10', 'end': '172.18.0.200'}]
    StorageMgmtAllocationPools: [{'start': '172.19.0.10', 'end': '172.19.0.200'}]
    TenantAllocationPools: [{'start': '172.16.0.10', 'end': '172.16.0.200'}]
    # Use an External allocation pool which will leave room for floating IPs
    ExternalAllocationPools: [{'start': '10.1.2.10', 'end': '10.1.2.50'}]
    # Set to the router gateway on the external network
    ExternalInterfaceDefaultRoute: 10.1.2.1
    # Gateway router for the provisioning network (or Undercloud IP)
    ControlPlaneDefaultRoute: 192.0.2.254
    # Generally the IP of the Undercloud
    EC2MetadataIp: 192.0.2.1
    # Define the DNS servers (maximum 2) for the overcloud nodes
    DnsServers: ["8.8.8.8","8.8.4.4"]
    InternalApiNetworkVlanID: 201
    StorageNetworkVlanID: 202
    StorageMgmtNetworkVlanID: 203
    TenantNetworkVlanID: 204
    ExternalNetworkVlanID: 100
    # May set to br-ex if using floating IPs only on native VLAN on bridge br-ex
    NeutronExternalNetworkBridge: "''"
    # Customize bonding options if required (ignored if bonds are not used)
    BondInterfaceOvsOptions:
        "bond_mode=balance-tcp lacp=active other-config:lacp-fallback-ab=true"

  * Source: http://docs.openstack.org/developer/tripleo-docs/advanced_deployment/network_isolation.html


### Predictable IPs


  * http://tripleo.org/advanced_deployment/node_placement.html#predictable-ips


## OpenDaylight

  * https://wiki.opnfv.org/display/apex/Apex
  * http://artifacts.opnfv.org/apex/brahmaputra/docs/installation-instructions/index.html


## TripleO Heat Templates

the command `openstack overcloud deploy` creates a heat stack and deploys using
the `overcloud.yaml` definition.


### Overcloud.yaml

  https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux_OpenStack_Platform/7/html/Director_Installation_and_Usage/chap-Planning_your_Overcloud.html#sect-Planning_Node_Deployment_Roles

  * Controller :886     -> overcloud-resource-registry.yaml => puppet/controller.yaml
  * Compute :1028                                           => puppet/compute.yaml
  * BlockStorage
  * ObjectStorage
  * CephStorage


In overcloud-resource-registry.yaml the controllerconfig is set as:

```
  # set to controller-config-pacemaker.yaml to enable pacemaker
  OS::TripleO::ControllerConfig: puppet/controller-config.yaml
```


### environments/puppet-pacemaker.yaml

Overrides `OS::TripleO::ControllerConfig: ../puppet/controller-config-pacemaker.yaml`


### all nodes output

```
/tripleo-heat-templates/puppet/all-nodes-config.yaml:
	314: outputs:
```

    hosts_entries:
    description: |
      The content that should be appended to your /etc/hosts if you want to get
      hostname-based access to the deployed nodes (useful for testing without
      setting up a DNS).
    value: {get_attr: [allNodesConfigImpl, config, hosts]}


### Package installation

  * UpdateDeployment


### Deployed servers

  * /tripleo-heat-templates/puppet/compute.yaml:
  * /tripleo-heat-templates/puppet/controller.yaml:
  * /tripleo-heat-templates/puppet/ceph-storage.yaml:
  * /tripleo-heat-templates/puppet/cinder-storage.yaml:
  * /tripleo-heat-templates/puppet/swift-storage.yaml:


### Puppet

the integration between Heat and Puppet is realized using the resource:

  OS::Heat::SoftwareConfig

which is defined with a group: puppet. In the definition they de a get_file pointing to a manifest file:

For instance in `tripleo-heat-templates` they have

  * puppet/compute-post
  * puppet/controller-config
  * puppet/controller-config-pacemaker
  * puppet/swift-stroagepost

that have this defined. In controller-config for instance, you will find the following manifest specified:

  manifests/overcloud_controller.pp    =>   https://github.com/openstack/tripleo-heat-templates/blob/master/puppet/manifests/overcloud_controller.pp


### References

  * http://hardysteven.blogspot.com/2015/05/tripleo-heat-templates-part-1-roles-and.html
  * http://hardysteven.blogspot.com/2015/05/tripleo-heat-templates-part-2-node.html
  * http://hardysteven.blogspot.com/2015/05/tripleo-heat-templates-part-3-cluster.html


## Migrate workload


  * http://tripleo.org/post_deployment/quiesce_compute.html

```
$ . overcloudrc  # admin credentials for the overcloud
$ nova service-list
$ nova service-disable <service-host> nova-compute
```

## Scaling out (overcloud)


Nodes seem to be in maintenance mode after the deployment.

`ironic node-list`


Looking at:

  * https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux_OpenStack_Platform/7/html/Director_Installation_and_Usage/sect-Scaling_the_Overcloud.html

shows that a `ironic node-set-maintenance [NODE UUID] false` is
needed. After this the nodes are available to be deployed to.


Scaling can be done using the `--compute-scale [n]` option to the `openstack overcloud deploy` command.
Editing the `overcloud-deploy.sh` can be done for this.


## Disk image-build

```
yum install -y python-pip
yum install -y python-virtualenv

virtualenv .
source bin/activate
pip install dib-utils pyyaml


export ELEMENTS_PATH=tripleo-image-elements/elements
diskimage-builder/bin/disk-image-create -a i386 -o bootstrap -u base vm bootstrap local-config stackuser heat-cfntools


diskimage-builder/bin/disk-image-create -a amd64 -o compute-image fedora nova-compute nova-kvm
```

The above `disk-image-create` commands do not work.


Able to generate a basic image using:

```
# diskimage-builder/bin/disk-image-create -a amd64 -o compute-image centos
```

after including the following to `/etc/hosts`:

    162.252.80.138 cloud.centos.org


Will look at using the `quickstart.sh` playbook for this:



Prebuilt images that are used are:

  * ${OPT_UNDERCLOUD_URL:=http://artifacts.ci.centos.org/artifacts/rdo/images/${RELEASE}/delorean/stable/undercloud.qcow2}
    /tripleo-quickstart/quickstart.sh
  * image_url: http://artifacts.ci.centos.org/rdo/images/{{ release }}/delorean/stable/undercloud.qcow2
    /tripleo-quickstart/playbooks/roles/libvirt/defaults/main.yml:

Currently available (easy copy-n-paste)
  * http://artifacts.ci.centos.org/artifacts/rdo/images/liberty/delorean/stable/undercloud.qcow2
  * http://artifacts.ci.centos.org/artifacts/rdo/images/mitaka/delorean/stable/undercloud.qcow2

About 2.4G in size. These were made using the generic cloud image:

  * http://cloud.centos.org/centos/7/images/CentOS-7-x86_64-GenericCloud.qcow2 (800M)


Images can be cached or specified using a download url (needs md5):

    /home/username/.quickstart/latest.qcow2



  * https://github.com/redhat-openstack/ansible-role-tripleo-image-build/blob/master/tasks/dib_build.yml#L45-L96


### Build the ironic-python-agent ramdisk

    source {{ artib_working_dir }}/dib-optional-env-inject.sh
    disk-image-create \
    -a amd64 \
    -o {{ artib_working_dir }}/ironic-python-agent \
    centos7 \
    dhcp-all-interfaces \
    dynamic-login \
    selinux-permissive \
    pip-and-virtualenv-override \
    ironic-agent \
    -p python-hardware-detect \
    --min-tmpfs 5


### Build overcloud-full image

    source {{ artib_working_dir }}/dib-optional-env-inject.sh
    disk-image-create \
    -a amd64 \
    -o {{ artib_working_dir }}/overcloud-full.qcow2 \
    centos7 \
    baremetal \
    dhcp-all-interfaces \
    stable-interface-names \
    grub2 \
    dynamic-login \
    selinux-permissive \
    element-manifest \
    heat-config-puppet \
    heat-config-script \
    hosts \
    network-gateway \
    os-net-config \
    sysctl \
    hiera \
    pip-and-virtualenv-override \
    --min-tmpfs

### Convert the overcloud image to an undercloud image

    virt-customize -m {{ artib_virt_customize_ram }}
    --smp {{ artib_virt_customize_cpu }}
    -a {{ artib_working_dir }}/undercloud-base.qcow2
    --run {{ artib_working_dir }}/undercloud-convert.sh
    

To be changed to:

  * https://review.gerrithub.io/#/c/274691/11/tasks/dib_build.yml


### Ansible role

https://github.com/redhat-openstack/ansible-role-tripleo-image-build


```
$ cd tests\pip
$ build.sh $VIRTHOST
```

This will generate an image, based on

  * artib_minimal_base_image_url  
    http://cloud.centos.org/centos/7/images/CentOS-7-x86_64-GenericCloud.qcow2

Successful run currently takes about 1h 30+m


### Packages installed

In the file `vars\default_package_list.yml` of `ansible-role-tripleo-image-build`
the packages to be installed are defined.

  * https://github.com/redhat-openstack/ansible-role-tripleo-image-build/blob/master/vars/default_package_list.yml

The `overcloud` image gets converted into an `undercloud` image by removing and
installing certain packages. This list is defined in `defaults\main.yml`:

  * artib_undercloud_remove_packages
  * artib_undercloud_install_packages

After which `dib_build.yml` will prepare and do the `disk-imaging-build`, this
generates the IPA (ironic-python-agent) ramdisk and the overcloud full image. 
These steps invoke `library\tripleo_build_images.py`. It uses respectively the
following elements:

  * artib_agent_ramdisk_elements
  * artib_overcloud_full_elements


## Quickstart Overcloud deploy

```
$ CONFIG=~/.quickstart/oooq_test_config.yml
$ cat > $CONFIG << EOCONF
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
 
    extra_args: "--control-scale 3 -e /usr/share/openstack-tripleo-heat-templates/environments/puppet-pacemaker.yaml --ntp-server pool.ntp.org"
    EOCONF


### pingtest

`/tmp/deploy_env.yaml`

    parameter_defaults:
      controllerExtraConfig:
      # In releases before Mitaka, HeatWorkers doesnt modify
      # num_engine_workers, so handle via heat::config 
        heat::config::heat_config:
          DEFAULT/num_engine_workers:
            value: 1
        heat::api_cloudwatch::enabled: false
        heat::api_cfn::enabled: false
      HeatWorkers: 1
      CeilometerWorkers: 1
      CinderWorkers: 1
      GlanceWorkers: 1
      KeystoneWorkers: 1
      NeutronWorkers: 1
      NovaWorkers: 1
      SwiftWorkers: 1



### Quickstart overcloud deploy performs

  * openstack overcloud deploy --templates extra_args

  TripleO clientL overcloud_deploy.py
    * heat deploy -> additional topic
    * keystone init
    

### Quickstart overcloud deploy after

  * heat stack-list
  * if contains CREATE_FAILED
    * heat resource-list
      * heat deployment-show > log
    * return 1
  * else
    * OK


## After the overcloud deployment

Login to overcloud nodes is possible from the undercloud with:

```
$  ssh heat-admin@192.0.2.16
```

for instance. All the host entries can be found in `/etc/hosts`
(This is the output of `heat output-show -F raw overcloud`).


## Virtual Environment Setup Complete

Access the undercloud by:

    ssh -F $OPT_WORKDIR/ssh.config.ansible undercloud

There are scripts in the home directory to continue the deploy:

    undercloud-install.sh will run the undercloud install
    undercloud-post-install.sh will perform all pre-deploy steps
    overcloud-deploy.sh will deploy the overcloud
    overcloud-deploy-post.sh will do any post-deploy configuration
    overcloud-validate.sh will run post-deploy validation

Alternatively, you can ignore these scripts and follow the upstream docs:

First:

    openstack undercloud install
    source stackrc

Then continue with the instructions (limit content using dropdown on the left):

    http://ow.ly/Ze8nK
    http://docs.openstack.org/developer/tripleo-docs/basic_deployment/basic_deployment_cli.html#upload-images


## Quickstart Undercloud


### post install

`undercloud-install-post.sh`

    openstack overcloud image upload
    openstack baremetal import --json instackenv.json
    openstack baremetal configure boot

Also deals with the flavors and vlan if defined


## Red Hat OpenStack Platform

  * https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux_OpenStack_Platform/7/html/Director_Installation_and_Usage/ch10s06.html


### Parameters

  * https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux_OpenStack_Platform/7/html/Director_Installation_and_Usage/appe-Deployment_Parameters.html


### References

  * Advanced Deployment with TripleO  
    https://keithtenzer.com/2015/11/09/advanced-deployment-with-tripleo-and-openstack-director/


## Packages

RDO package specs are collected at http://github.com/openstack-packages/

They are created using DLRN: https://github.com/openstack-packages/DLRN


### Delorean

Install and configure DLRN
  * http://dlrn.readthedocs.io/en/latest/installation.html#configuring-your-httpd


```
$ sudo yum install git createrepo python-virtualenv mock gcc redhat-rpm-config rpmdevtools httpd
$ sudo usermod -a -G mock $USER
$ newgrp mock
$ newgrp $USER
$ sudo systemctl start httpd
$ virtualenv dlrn-venv
$ source drln-venv/bin/activate
$ git clone https://github.com/openstack-packages/DLRN.git
$ cd DLRN
$ pip install --upgrade pip
$ pip install -r requirements.txt
$ python setup.py develop
```


## Install Ceph on Compute nodes
Undercloud images need to be regenerated:

  * https://github.com/gbraad/redhat-openstack-ansible-role-tripleo-image-build/tree/compute-ceph-enable
  * https://github.com/gbraad/openstack-tripleo-heat-templates/tree/compute-ceph-enable


### image-build: `override.yml`
```
artib_package_install_script: package-install-workarounds.sh.j2
artib_minimal_base_image_url: file:///var/lib/oooq-images-org/CentOS-7-x86_64-GenericCloud.qcow2
```

### image-build: `package-install-workarounds.sh.j2`
```
#!/bin/bash
set -eux
yum install -y {% for package in artib_overcloud_packages %} {{ package }} {% endfor %}
yum install -y unzip

# ceph enablement
pushd /usr/share/openstack-tripleo-heat-templates
curl -o patch.zip https://review.openstack.org/changes/273754/revisions/32d5658b903a2ac4ab192a5b83c35a31c76d4c06/patch?zip
unzip patch.zip
patch -p1 < 32d5658b.diff
popd
```

### image-build: generate image
```
$ build.sh -e override.yml $VIRTHOST
```

### quickstart: config
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

extra_args: >-
  --control-scale 3
  --compute-scale 3
  --ceph-storage-scale 3
  -e /usr/share/openstack-tripleo-heat-templates/environments/puppet-pacemaker.yaml
  -e ~/storage-environment.yaml
  --ntp-server pool.ntp.org
```

### quickstart: install undercloud
```
./quickstart.sh \
  --config deploy-config.yml \
  -e undercloud_image_url=file:///var/lib/oooq-images/undercloud-mitaka.qcow2 \
  $VIRTHOST
```

### undercloud: `overcloud-deploy.sh`
```
openstack overcloud deploy --templates --libvirt-type qemu --control-flavor oooq_control --compute-flavor oooq_compute --ceph-storage-flavor oooq_ceph --timeout 60 --control-scale 3 --compute-scale 3 --ceph-storage-scale 3 -e /usr/share/openstack-tripleo-heat-templates/environments/puppet-pacemaker.yaml -e ~/storage-environment.yaml --ntp-server pool.ntp.org
```

### undercloud: `~/storage-environment.yaml`
```
parameter_defaults:
  CinderEnableIscsiBackend: false
  CinderEnableRbdBackend: true
  NovaEnableRbdBackend: true
  GlanceBackend: rbd
  GnocchiBackend: rbd
  ComputeEnableCephStorage: true
```

### undercloud

  * `overcloud-deploy.sh`
  * `overcloud-deploy-post.sh`


## Post Installation Customization
By modifying the Heat templates it is simple to install additional software on
the nodes during the post-install phase.


## Create post installation yaml files.

 * http://tripleo.org/advanced_deployment/extra_config.html

```
undercloud$ cat << EOF > ~/templates/firstboot-environment.yaml
resource_registry:
  OS::TripleO::NodeUserData: /home/stack/my_templates/firstboot-config.yaml
EOF
```

```
undercloud$ cat << EOF > ~/templates/firstboot-config.yaml
heat_template_version: 2014-10-16

resources:
  userdata:
    type: OS::Heat::MultipartMime
    properties:
      parts:
      - config: {get_resource: repo_config}

  repo_config:
    type: OS::Heat::SoftwareConfig
    properties:
      config: |
        #!/bin/bash
        yum install -y vim
        # and any other commands you want to be performed

outputs:
  OS::stack_id:
    value: {get_resource: userdata}
EOF
```

Deploy overcloud using custom the post-installation customization.

```
[stack@undercloud ~]$ openstack overcloud deploy --templates \
   --control-flavor control --compute-flavor compute --control-scale 1
   --compute-scale 1 --neutron-tunnel-types vxlan --neutron-network-type vxlan
   -e ~/templates/firstboot-environment.yaml
```


## TripleO baremetal deployment using Instack
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
    "pm_type":"pxe_ipmitool",
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
    "pm_type":"pxe_ipmitool",
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


## TripleO baremetal deployment using Quickstart

  * [TripleO Quickstart on baremetal](https://gist.github.com/gbraad/51bcd499dd7def19a1213381a1468f16)

This gist describes a deployment using the Quickstart in an
interactive way. Tags and setting `step_introspect: true` can
automate the whole flow, however due to issues along the way
this wasn't suggested.


### Configuration

Increase the VM properties by providing these variables
```
undercloud_memory: 16384
undercloud_disk: 80
undercloud_vcpu: 8
```

```
bash quickstart.sh
--ansible-debug
--bootstrap
--working-dir <my dir>
--tags all
--no-clone
--requirements quickstart-role-requirements.txt
--requirements <other roles if needed>
--config <env specific config>
--extra-vars @<env settings fle with settings to overwrite>
--playbook baremetal-virt-undercloud-tripleo.yml
--release mitaka
--teardown all
$VIRTHOST
```



## Introspection issues

```
for i in $(ironic node-list | grep -v UUID | awk ' { print $2 } '); do
  ironic node-set-maintenance $i off;
done
```

```
for i in $(ironic node-list | grep available | grep -v UUID | awk ' { print $2 } '); do
  ironic node-set-maintenance $i false;
done
```

### Store ramdisk logs

```
openstack-config --set /etc/ironic-inspector/inspector.conf processing always_store_ramdisk_logs true
systemctl restart openstack-ironic-inspector
```


## Undercloud issues
It seems to happen that the undercloud becomes unresponsive to network requests. In that case you can reboot the virtual machine

```
sudo su - stack -c "virsh reboot undercloud"
```
If this happens often, please increase the memory and VCPUs assigned to the VM.


## Overcloud issues

### Deployment failed

If nodes have been associated, but the deployment failed, you can reclaim the nodes for provisioning:
```
for i in $(ironic node-list | grep -v UUID | awk ' { print $2 } '); do
  ironic node-set-provision-state $i deleted
done
```

