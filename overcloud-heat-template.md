TripleO Heat Templates
======================

the command `openstack overcloud deploy` creates a heat stack and deploys using
the `overcloud.yaml` definition.


Overcloud.yaml
--------------

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


environments/puppet-pacemaker.yaml
----------------------------------

Overrides `OS::TripleO::ControllerConfig: ../puppet/controller-config-pacemaker.yaml`


all nodes output
----------------

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


Package installation
--------------------

  * UpdateDeployment



Deployed servers
----------------

  * /tripleo-heat-templates/puppet/compute.yaml:
  * /tripleo-heat-templates/puppet/controller.yaml:
  * /tripleo-heat-templates/puppet/ceph-storage.yaml:
  * /tripleo-heat-templates/puppet/cinder-storage.yaml:
  * /tripleo-heat-templates/puppet/swift-storage.yaml:


Puppet
------

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



References
----------

  * http://hardysteven.blogspot.com/2015/05/tripleo-heat-templates-part-1-roles-and.html
  * http://hardysteven.blogspot.com/2015/05/tripleo-heat-templates-part-2-node.html
  * http://hardysteven.blogspot.com/2015/05/tripleo-heat-templates-part-3-cluster.html
