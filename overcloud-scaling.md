Scaling out (overcloud)
=======================


Nodes seem to be in maintenance mode after the deployment.

`ironic node-list`



Looking at:
  https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux_OpenStack_Platform/7/html/Director_Installation_and_Usage/sect-Scaling_the_Overcloud.html

shows that a `ironic node-set-maintenance [NODE UUID] false` is
needed. After this the nodes are available to be deployed to.


Scaling can be done using the `--compute-scale [n]` option to the `openstack overcloud deploy` command.
Editing the `overcloud-deploy.sh` can be done for this.
