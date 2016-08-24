Project Atomic
==============

  * See also [ostree](ostree.md)
  * [Getting started](http://www.projectatomic.io/download/)


Fedora Atomic
-------------

  * [Landingpage](https://getfedora.org/cloud/download/atomic.html)
  * [Download images](https://mirrors.kernel.org/fedora-alt/atomic/stable/Cloud-Images/x86_64/Images/)


CentOS Atomic
-------------

  * [Landingpage](https://wiki.centos.org/SpecialInterestGroup/Atomic/Download/)
  * [Download images](http://cloud.centos.org/centos/7/atomic/images/)


Kubernetes on Atomic
--------------------

Deploy kubernetes on CentOS and CentOS Atomic hosts using Ansible

  * About [Kubernetes](kubernetes/README.md)
  * [Ansible playbook](https://github.com/gbraad/ansible-playbook-kubernetes)
  * [Getting started with containers](https://access.redhat.com/documentation/en/red-hat-enterprise-linux-atomic-host/version-7/getting-started-with-containers/)
  * [Getting started guide](http://www.projectatomic.io/docs/gettingstarted/) at Project Atomic


Atomic Reactor
--------------

On Fedora 24 or CentOS 7
```
$ dnf install -y atomic-reactor
$ docker pull registry.gitlab.com/gbraad/fedora:atomicreactor
$ docker tag registry.gitlab.com/gbraad/fedora:atomicreactor buildroot-hostdocker
$ atomic-reactor build git --method hostdocker \
             --build-image buildroot-hostdocker \
             --image hello-world \
             --target-registries 10.5.0.2:5000 \
             --uri "https://github.com/TomasTomecek/docker-hello-world.git"
```

[More info](https://github.com/projectatomic/atomic-reactor)

Note: currently `FROM:scratch` builds are not supported.


Build your own Atomic
---------------------

  * [BYO Atomic](https://github.com/jasonbrooks/byo-atomic)
  * [BYO Atomic](https://gitlab.com/gbraad/byo-atomic) - base
    * [Registry](https://gitlab.com/gbraad/byo-atomic/container_registry)
  * [BYO Atomic - Toolbox](https://gitlab.com/gbraad/byo-atomic-toolbox)


### Variant: CentOS
Automated GitLab CI runner is available: [BYO Atomic - CentOS](https://gitlab.com/gbraad/byo-atomic-centos)


#### Instructions
Use Fedora as basis for the buildserver. In this example F24-Cloud is used.

```
dnf install -y git rpm-ostree rpm-ostree-toolbox python

git clone https://github.com/CentOS/sig-atomic-buildscripts
cd sig-atomic-buildscripts
git checkout downstream

mkdir -p /srv/repo
ostree --repo=/srv/repo init --mode=archive-z2

rpm-ostree compose tree --repo=/srv/repo sig-atomic-buildscripts/centos-atomic-host.json

cd /srv/repo
python -m SimpleHTTPServer
```

```
sudo ostree remote add foo http://$YOUR_IP:8000/repo --no-gpg-verify
sudo rpm-ostree rebase foo:centos-atomic/7/x86_64/docker-host
```


### Variant: Fedora
Automated GitLab CI runner is available: [BYO Atomic - Fedora](https://gitlab.com/gbraad/byo-atomic-fedora)


#### Instructions
```
git clone https://git.fedorahosted.org/git/fedora-atomic.git
cd fedora-atomic
git checkout f23
```


Upstream ostree
---------------

### Using CentOS continuous ostree

If you are running an existing Atomic image
```
ostree remote add --set=gpg-verify=false centos-atomic-continuous https://ci.centos.org/artifacts/sig-atomic/rdgo/centos-continuous/ostree/repo/
rpm-ostree rebase centos-atomic-continuous:centos-atomic-host/7/x86_64/devel/continuous
systemctl reboot
```

[Source](https://wiki.centos.org/SpecialInterestGroup/Atomic/Devel)


### Using Fedora Workstation ostree

From inside an existing system:
```
yum -y install ostree
ostree admin init-fs /
ostree remote add --set=gpg-verify=false fedora-ws-centosci https://ci.centos.org/artifacts/sig-atomic/rdgo/fedora-workstation/ostree/repo/
ostree --repo=/ostree/repo pull fedora-ws-centosci:fedora/24/x86_64/desktop/continuous
ostree admin os-init fedora
ostree admin deploy --os=fedora --karg-proc-cmdline --karg=enforcing=0 fedora-ws-centosci:fedora/24/x86_64/desktop/continuous
cp /etc/fstab /ostree/deploy/fedora/deploy/$checksum.0/etc
#  (You may need to modify the fstab)
grub2-mkconfig -o /boot/grub2/grub.cfg
```

If you already deployed F23 and want to rebase to F24:
```
  rpm-ostree rebase fedora/24/x86_64/desktop_continuous
```

[Source](https://ci.centos.org/job/atomic-fedora-ws/), [See also](https://fedoraproject.org/wiki/Changes/WorkstationOstree)


## PXE boot Atomic image

Steven Dake [describes](https://sdake.io/2014/12/09/isnt-it-atomic-on-openstack-ironic-dont-you-think/) the process of [converting](https://github.com/sdake/fedora-atomic-to-liveos-pxe) an Atomic image to be used by Ironic for PXE-boot.


## Grow the `/` filesystem
```
lvremove /dev/mapper/atomicos-docker--pool
lvresize -l +100%FREE /dev/atomicos/root
xfs_growfs /dev/atomicos/root
```

Note: this trashes the `docker` storage pool


Atomic on OpenStack
----------------

```
$ wget http://http://cloud.centos.org/centos/7/atomic/images/CentOS-Atomic-Host-7-GenericCloud.qcow2
$ openstack image create "CentOS7 Atomic" --progress --file CentOS-Atomic-Host-7-GenericCloud.qcow2 --disk-format qcow2 --container-format bare --property os_distro=centos
```

```
$ wget https://download.fedoraproject.org/pub/alt/atomic/stable/CloudImages/x86_64/images/Fedora-Atomic-24-20160809.0.x86_64.qcow2
$ openstack image create "Fedora24 Atomic" --progress --file Fedora-Atomic-24-20160809.0.x86_64.qcow2 --disk-format qcow2 --container-format bare --property os_distro=fedora
```

Note: for Ceph you might have to `convert-img` to RAW format.


Atomic using Vagrant
--------------------

### CentOS

```
$ vagrant init centos/atomic-host && vagrant up --provider virtualbox 
```

[Source](https://atlas.hashicorp.com/centos/boxes/atomic-host)


### Fedora

```
$ vagrant init fedora/24-atomic-host && vagrant up --provider virtualbox
```

[Source](https://atlas.hashicorp.com/fedora/boxes/24-atomic-host)


Other resources
---------------

  * [Cloud-init](cloudinit.md)
  * [Compose server](https://github.com/projectatomic/rpm-ostree/blob/master/docs/manual/compose-server.md)
  * [Photon OS](https://github.com/vmware/photon), [wiki](https://github.com/vmware/photon/wiki/Photon-RPM-OSTree:-Preface)
  * [Testing Atomic for OpenStack](https://gist.github.com/gbraad/36c572fe58aeee703c829c94d9dc8a95)
