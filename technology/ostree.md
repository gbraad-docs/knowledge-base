ostree
======


## Build your own Atomic - CentOS
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


## Build your own Atomic - Fedora
```
git clone https://git.fedorahosted.org/git/fedora-atomic.git
cd fedora-atomic
git checkout f23
```


## PXE boot Atomic image

Steven Dake [describes](https://sdake.io/2014/12/09/isnt-it-atomic-on-openstack-ironic-dont-you-think/) the process of [converting](https://github.com/sdake/fedora-atomic-to-liveos-pxe) an Atomic image to be used by Ironic for PXE-boot.


## Using CentOS continuous ostree

If you are running an existing Atomic image
```
ostree remote add --set=gpg-verify=false centos-atomic-continuous https://ci.centos.org/artifacts/sig-atomic/rdgo/centos-continuous/ostree/repo/
rpm-ostree rebase centos-atomic-continuous:centos-atomic-host/7/x86_64/devel/continuous
systemctl reboot
```

[Source](https://wiki.centos.org/SpecialInterestGroup/Atomic/Devel)


## Using Fedora Workstation ostree

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


## Links

  * [Compose server](https://github.com/projectatomic/rpm-ostree/blob/master/docs/manual/compose-server.md)
  * [BYO Atomic](https://github.com/jasonbrooks/byo-atomic)
  * [BYO Atomic - base](https://gitlab.com/gbraad/byo-atomic-base)
    * [Registry](https://gitlab.com/gbraad/byo-atomic/container_registry)
  * [BYO Atomic - CentOS](https://gitlab.com/gbraad/byo-atomic-centos)
    * Artifacts: [Archive](https://gitlab.com/gbraad/byo-atomic-centos/builds/2175934/artifacts/download), [Browse](https://gitlab.com/gbraad/byo-atomic-centos/builds/2175934/artifacts/browse)
  * [BYO Atomic - Fedora](https://gitlab.com/gbraad/byo-atomic-fedora)
  * [BYO Atomic - Toolbox](https://gitlab.com/gbraad/byo-atomic-toolbox)

