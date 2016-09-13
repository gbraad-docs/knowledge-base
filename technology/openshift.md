OpenShift
=========

  * Local [development setup](https://github.com/projectatomic/osbs-client/blob/master/docs/development-setup.md).
  * Deploy OpenShift [Build System](https://github.com/projectatomic/osbs-client/blob/master/docs/osbs_instance_setup.md)


## All-in-one

### Docker
```
$ sudo docker run -d --name "origin" \
        --privileged --pid=host --net=host \
        -v /:/rootfs:ro -v /var/run:/var/run:rw -v /sys:/sys -v /var/lib/docker:/var/lib/docker:rw \
        -v /var/lib/origin/openshift.local.volumes:/var/lib/origin/openshift.local.volumes \
        openshift/origin start
```


### Vagrant

```
$ vagrant init openshift/origin-all-in-one
$ vagrant up
```

  * https://atlas.hashicorp.com/openshift/boxes/origin-all-in-one


### Binary

```
$ yum install -y docker wget screen
$ systemctl enable docker
$ systemctl start docker
$ wget https://github.com/openshift/origin/releases/download/v1.3.0-alpha.3/openshift-origin-server-v1.3.0-alpha.3-7998ae4-linux-64bit.tar.gz
$ tar -zxvf openshift-origin-server-v1.3.0-alpha.3-7998ae4-linux-64bit.tar.gz -C /opt/server
$ wget https://github.com/openshift/origin/releases/download/v1.3.0-alpha.3/openshift-origin-client-tools-v1.3.0-alpha.3-7998ae4-linux-64bit.tar.gz
$ tar -zxvf openshift-origin-client-tools-v1.3.0-alpha.3-7998ae4-linux-64bit.tar.gz -C /opt/client
$ /opt/server/openshift start
```

  * https://github.com/openshift/origin/releases


## Ansible

  * https://github.com/openshift/openshift-ansible

