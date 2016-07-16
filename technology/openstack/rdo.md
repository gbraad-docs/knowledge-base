# RDO

![RDO](http://community.redhat.com/images/blog/rdo-logo.png)


## PackStack scenario001 test using WeIRDO

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


## Repositories

  * [liberty](http://mirror.centos.org/centos/7/cloud/x86_64/openstack-liberty/)
  * [mitaka](http://mirror.centos.org/centos/7/cloud/x86_64/openstack-mitaka/)
