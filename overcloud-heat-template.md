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
