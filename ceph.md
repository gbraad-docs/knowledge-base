Ceph
====


  * https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux_OpenStack_Platform/7/html/Director_Installation_and_Usage/sect-Advanced-Scenario_3_Using_the_CLI_to_Create_an_Advanced_Overcloud_with_Ceph_Nodes.html


Install using quickstart
------------------------

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


Use ceph-ansible to takeover existing deployment
------------------------------------------------

  * https://www.sebastien-han.fr/blog/2016/05/02/Take-over-an-existing-Ceph-cluster-with-Ceph-Ansible/
