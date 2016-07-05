ostree
======


## Links

  * [Compose server](https://github.com/projectatomic/rpm-ostree/blob/master/docs/manual/compose-server.md)
  * [BYO Atomic](https://github.com/jasonbrooks/byo-atomic)

  * [BYO Atomic - base](https://gitlab.com/gbraad/byo-atomic)
  * [BYO Atomic - CentOS](https://gitlab.com/gbraad/byo-atomic-centos)
  * [BYO Atomic - Fedora](https://gitlab.com/gbraad/byo-atomic-fedora)


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
