Scratchpad for TripleO
======================

Just some thoughts and notes on OpenStack TripleO


_Gerard Braad <me@gbraad.nl>_



## CI Jobs


### tripleo-quickstart-promote-master-delorean-minimal

    export VIRTHOST='my-cool-virthost.example.com'
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


### Use ceph-ansible to takeover existing deployment

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


### Customization of the Heat templates

```
$ mkdir customized
$ cp -r /usr/share/openstack-tripleo-heat-templates/* customized/
```


### Network isolation

```
$ vi environments/network-environment.yaml
```

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
```

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

About 2.4G in size. These were made using the generic cloud image:

  * http://cloud.centos.org/centos/7/images/CentOS-7-x86_64-GenericCloud.qcow2 (800M)


Images can be cached or specified using a download url (needs md5):

    /home/[username:stack]/.quickstart/latest.qcow2



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
 
    extra_args: "--control-scale 3 -e /usr/share/openstack-tripleo-heat-templates/environments/puppet-pacemaker.yaml --ntp-server pool.ntp.org
    EOCONF


### pingtest

`/tmp/deploy_env.yaml`

    parameter_defaults:
      controllerExtraConfig:
      # In releases before Mitaka, HeatWorkers doesn't modify
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
$  ssh -i ~/.ssh/id_rsa heat-admin@192.0.2.16
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
$ pip install dlrn
```
