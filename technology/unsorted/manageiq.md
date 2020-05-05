ManageIQ
========


## Deployment

### As container
```
$ docker pull manageiq/manageiq:latest-darga
$ docker run --privileged -di -p 80:80 -p 443:443 manageiq/manageiq:latest-darga
```

Credentials: admin/smartvm

[Source](https://hub.docker.com/r/manageiq/manageiq/)


### As image (OpenStack/KVM)
```
$ curl -O -L http://manageiq.org/download/manageiq-openstack-darga-3.qc2
$ openstack image create --name "manageiq" --disk-format qcow2 \
  --container-format=bare --file manageiq-openstack-darga-3.qc2
```

