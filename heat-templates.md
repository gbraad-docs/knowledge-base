Heat
====

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

This is basically how they integrate Puppet into the heat deployment.