Project Atomic
==============

  * See also [ostree/Atomic](ostree.md)


Atomic Reactor
--------------

On Fedora 24
```
$ dnf install -y atomic-reactor
$ docker pull registry.gitlab.com/gbraad/fedora:atomicreactor
$ docker tag registry.gitlab.com/gbraad/fedora:atomicreactor buildroot-hostdocker
$ atomic-reactor build git --method hostdocker --build-image buildroot --image test-image --uri "https://github.com/gbraad/docker-hello-world.git"
```
