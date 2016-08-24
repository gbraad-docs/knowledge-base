Project Atomic
==============


Atomic Reactor
--------------

```
$ docker pull registry.gitlab.com/gbraad/fedora:atomicreactor
$ docker tag registry.gitlab.com/gbraad/fedora:atomicreactor buildroot-hostdocker
$ atomic-reactor build git --method hostdocker --build-image buildroot --image test-image --uri [url]
```
