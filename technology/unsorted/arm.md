ARM
===

  * RaspberryPi
  * [Mozilla B2G](#mozilla-b2g) / Firefox OS
  * etc.


Distros
-------

### Fedora

  * https://dl.fedoraproject.org/pub/fedora/linux/releases/24/Spins/armhfp/images/Fedora-Minimal-armhfp-24-1.2-sda.raw.xz


### CentOS

  * http://mirror.centos.org/altarch/7/isos/aarch64/
  * http://mirror.centos.org/altarch/7/isos/armhfp/
  * https://wiki.centos.org/SpecialInterestGroup/AltArch/Arm32


Docker
------

  * [Installing and running Docker](https://github.com/umiddelb/armhf/wiki/Installing,-running,-using-docker-on-armhf-(ARMv7)-devices)

### Containers

  * [Debian](https://hub.docker.com/r/armv7/armhf-debian/)
  * [Fedora <24](https://hub.docker.com/r/armv7/armhf-fedora/)  
    [Fedora 24+](https://hub.dokcer.com/r/gbraad/armhf-fedora/)
  * [Ubuntu](https://hub.docker.com/r/armv7/armhf-ubuntu/)

### Repository

  * [GitLab](https://gitlab.com/gbraad/ARM), [GitHub](https://github.com/gbraad/docker-container-registry-arm)


Mozilla B2G
-----------

Mozilla B2G
```
$ git clone https://github.com/mozilla-b2g/B2G.git
```

Makermade (Node.JS + IoT)
```
$ git clone http://github.com/makermade/b2g.git
```

```
$ cd b2g
$ ./config.sh flame-kk       # or nexus5
$ docker pull gbraad/b2g-build  # registry.gitlab.com/gbraad/b2g-build:base
$ docker run -v $PWD:/B2G -i -t gbraad/b2g-build -j4
```

  * B2G-build: [GitLab](https://gitlab.com/gbraad/B2G-build), [GitHub](https://github.com/gbraad/B2G-build), [Docker hub](https://hub.docker.com/r/gbraad/b2g-build/)
