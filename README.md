Scratchpad for OpenStack
========================

Just some thoughts and notes on OpenStack


_Gerard Braad <me@gbraad.nl>_


## See also

### Tools

  * [Devstack configurations](https://github.com/gbraad/openstack-devstack-configurations)
  * [Packstack answerfiles](https://github.com/gbraad/openstack-packstack-answerfiles)
  * [Heat templates](https://github.com/gbraad/openstack-heat-templates)
  * [OpenStack client](https://github.com/gbraad/docker-openstack-client)


### Documentation

  * [Hands-on-labs](https://github.com/gbraad/openstack-handsonlabs)
  * [Tools training](https://github.com/gbraad/tools-training)
  * [Open Source Culture](https://github.com/gbraad/open-source-culture)


### Notes

  * [General scratchpad](https://github.com/gbraad/scratchpad)
  * [TripleO scratchpad](https://github.com/gbraad/openstack-tripleo-scratchpad)


## Bifrost

  * [Source](https://github.com/openstack/bifrost)
  

### Dynamic enrollment

```
$ BIFROST_INVENTORY_SOURCE=~/baremetal.json ansible-playbook -vvvv -i inventory/bifrost_inventory.py enroll-dynamic.yaml
```
