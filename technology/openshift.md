OpenShift
=========

  * Local [development setup](https://github.com/projectatomic/osbs-client/blob/master/docs/development-setup.md).
  * Deploy OpenShift [Build System](https://github.com/projectatomic/osbs-client/blob/master/docs/osbs_instance_setup.md)


## Installation of Origin Client

### CentOS
```
$ yum install -y centos-release-openshift-origin
$ yum install -y origin-clients
```

### Fedora
```
$ dnf install -y origin-clients
```

### Any
```
$ wget https://github.com/openshift/origin/releases/download/v1.1.6/openshift-origin-client-tools-v1.1.6-ef1caba-linux-64bit.tar.gz
$ tar zxvf openshift-origin-client-tools-v1.1.6-ef1caba-linux-64bit.tar.gz
$ mv openshift-origin-client-tools-v1.1.6-ef1caba-linux-64bit/oc /usr/bin/oc
```

### Containerized client **WIP**
```
$ docker pull gbraad/openshift-client:fedora   # registry.gitlab.com/gbraad/openshift-client:fedora
$ docker run --name oc -d gbraad/openshift-client:fedora
$ docker exec oc oc login https://x.x.x.x:8443
```


## Installation of Origin Server

  * Review [prerequisites](https://docs.openshift.org/latest/install_config/install/prerequisites.html#install-config-install-prerequisites)
  * Add `--insecure-registry 172.30.0.0/16` or similar to your `/etc/sysconfig/docker` configuration.
  * Configure [Docker storage](https://docs.openshift.org/latest/install_config/install/prerequisites.html#configuring-docker-storage)


### On CentOS 7

```
$ yum install wget git net-tools bind-utils iptables-services bridge-utils bash-completion
```

```
$ yum install -y centos-release-openshift-origin
$ yum install -y origin
```


### As Docker container from client
```
$ oc cluster up  # --version=v1.3.0-alpha.1 | origin:v1.3.0-alpha.3
```

Note: the version specifier refers to tags on the Docker Hub: eg. `openshift/origin:v1.3.0-alpha.3`


### As Docker container
```
$ sudo docker run -d --name "origin" \
        --privileged --pid=host --net=host \
        -v /:/rootfs:ro -v /var/run:/var/run:rw -v /sys:/sys -v /var/lib/docker:/var/lib/docker:rw \
        -v /var/lib/origin/openshift.local.volumes:/var/lib/origin/openshift.local.volumes \
        openshift/origin start
```

* https://hub.docker.com/r/openshift/origin/


### Binary

```
$ vi /etc/hosts # make sure your hostname is resolved to an IP
$ yum install -y docker wget screen
$ systemctl enable docker
$ systemctl start docker
# modify /etc/sysconfig/docker to use --insecure-registry 172.30.0.0/16
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


## All-in-one

### Vagrant

```
$ vagrant init openshift/origin-all-in-one
$ vagrant up
```

  * https://atlas.hashicorp.com/openshift/boxes/origin-all-in-one
  * https://atlas.hashicorp.com/openshift/boxes/origin-all-in-one/versions/1.3.0-alpha.3/providers/virtualbox.box
  * https://github.com/openshift-evangelists/vagrant-origin/


### Convert disk image

```
$ wget https://atlas.hashicorp.com/openshift/boxes/origin-all-in-one/versions/1.3.0-alpha.3/providers/virtualbox.box -O openshift.tar.gz
$ tar -zxvf openshift.tar.gz
$ yum install -y qemu-img
$ qemu-img convert box-disk1.vmdk -O qcow2 openshift.qcow2
```

### Upload disk image to OpenStack
Current image does not come with `cloud-init`:

```
$ yum install -y libguestfs-tools
$ systemctl start libvirtd
$ virt-customize -a openshift.qcow2 --install cloud-init
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


## Ansible

  * https://github.com/openshift/openshift-ansible

```
$ yum install ansible
$ git clone https://github.com/openshift/openshift-ansible -b master
$ cd openshift-ansible
```


## Usage

```
$ openshift start
```

```
$ oc login http://localhost:8443
```
Note: `~/.kube/config`

Or use a browser to open: http://localhost:8443
