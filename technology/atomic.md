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
$ atomic-reactor build git --method hostdocker \
             --build-image buildroot-hostdocker \
             --image hello-world \
             --target-registries 10.5.0.2:5000 \
             --uri "https://github.com/gbraad/docker-hello-world.git"
```
