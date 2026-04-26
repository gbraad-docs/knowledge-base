# OpenStack

Open source cloud infrastructure platform. Originally developed by NASA and Rackspace; now governed by the OpenInfra Foundation.

- [Keystone](keystone.md) - identity service
- [Client](client.md) - OpenStack CLI client
- [Puppet](puppet.md) - Puppet modules for OpenStack
- [RDO](rdo.md) - community OpenStack distribution for RHEL/Fedora
- [TripleO](tripleo.md) - OpenStack-on-OpenStack deployment
- [Devstack](devstack.md) - development/test deployment
- [Fuel](fuel.md) - Mirantis deployment tool
- [PackStack](packstack.md) - quick all-in-one deployment via Puppet

## Core services

| Service | Description |
|---------|-------------|
| Nova | Compute - virtual machine lifecycle |
| Neutron | Networking - virtual networks, routers, security groups |
| Cinder | Block storage |
| Swift | Object storage |
| Glance | Image registry |
| Keystone | Identity and authentication |
| Heat | Orchestration via declarative templates |
| Horizon | Web dashboard |
| Ironic | Bare-metal provisioning |
| Ceilometer | Telemetry and metering |
| Magnum | Container orchestration engines |

## Infrastructure components

- **Ceph** - storage backend for Cinder, Glance, Nova
- **Open vSwitch / Linuxbridge** - Neutron backends
- **RabbitMQ** - message bus between services
- **MariaDB + Galera** - highly available database
- **HAProxy + Keepalived** - service high availability

## Contributions

- [Devstack configurations](https://github.com/gbraad/openstack-devstack-configurations)
- [Packstack answerfiles](https://github.com/gbraad/openstack-packstack-answerfiles)
- [Heat templates](https://github.com/gbraad/openstack-heat-templates)
- [Hands-on-labs](https://github.com/gbraad/openstack-handsonlabs)
