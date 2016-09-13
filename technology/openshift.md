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

  * https://github.com/openshift/origin/releases

