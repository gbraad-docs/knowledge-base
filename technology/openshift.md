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

* https://hub.docker.com/r/openshift/origin/


### Vagrant

```
$ vagrant init openshift/origin-all-in-one
$ vagrant up
```

  * https://atlas.hashicorp.com/openshift/boxes/origin-all-in-one
  * https://atlas.hashicorp.com/openshift/boxes/origin-all-in-one/versions/1.3.0-alpha.3/providers/virtualbox.box


### Convert disk image

```
$ wget https://atlas.hashicorp.com/openshift/boxes/origin-all-in-one/versions/1.3.0-alpha.3/providers/virtualbox.box -O openshift.tar.gz
$ tar -zxvf openshift.tar.gz
$ yum install -y qemu-img
$ qemu-img convert box-disk1.vmdk -O qcow2 openshift.qcow2
```

### Upload disk image to OpenStack
Current image does not come with `cloud-init`, so setup can be tricky.

```
$ yum install -y libguestfs-tools
$ systemctl start libvirtd
$ openssl passwd -1 changeme
$1$BspPlj7e$f/.mW9B8/TCdayId0fuq.0
$ guestfish
><fs> add openshift.qcow2
><fs> run
><fs> mount /dev/VolGroup00/LogVol00 /
><fs> vi /etc/shadow
# change the entry to vagrant with $1$BspPlj7e$f/.mW9B8/TCdayId0fuq.0
><fs> exit
```

The converted Vagrant image can be uploaded to OpenStack

```
$ openstack image create "OpenShift" --file openshift.qcow2 --disk-format qcow2 --container-format bare
```

	+------------------+------------------------------------------------------+
	| Field            | Value                                                |
	+------------------+------------------------------------------------------+
	| container_format | bare                                                 |
	| disk_format      | qcow2                                                |
	| name             | OpenShift                                            |
	| schema           | /v2/schemas/image                                    |
	| size             | 9393799168                                           |
	+------------------+------------------------------------------------------+

Login using 'vagrant'


### Binary

```
$ vi /etc/hosts # make sure your hostname is resolved to an IP
$ yum install -y docker wget screen
$ systemctl enable docker
$ systemctl start docker
$ wget https://github.com/openshift/origin/releases/download/v1.3.0-alpha.3/openshift-origin-server-v1.3.0-alpha.3-7998ae4-linux-64bit.tar.gz
$ tar -zxvf openshift-origin-server-v1.3.0-alpha.3-7998ae4-linux-64bit.tar.gz -C /opt/server
$ wget https://github.com/openshift/origin/releases/download/v1.3.0-alpha.3/openshift-origin-client-tools-v1.3.0-alpha.3-7998ae4-linux-64bit.tar.gz
$ tar -zxvf openshift-origin-client-tools-v1.3.0-alpha.3-7998ae4-linux-64bit.tar.gz -C /opt/client
$ export PATH=/opt/server/:$PATH
$ openshift start
```

```
$ export KUBECONFIG="$(pwd)"/openshift.local.config/master/admin.kubeconfig
$ export CURL_CA_BUNDLE="$(pwd)"/openshift.local.config/master/ca.crt
$ sudo chmod +r "$(pwd)"/openshift.local.config/master/admin.kubeconfig
```


  * https://github.com/openshift/origin/releases


## Ansible

  * https://github.com/openshift/openshift-ansible

