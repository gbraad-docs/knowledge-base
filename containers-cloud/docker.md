Docker
======


Containers and registries
-------------------------

### Docker hub

  * Personal [repos](https://hub.docker.com/r/gbraad/)


### GitLab


  * [devenv](https://gitlab.com/gbraad/devenv)
  * [Jupyter](https://gitlab.com/gbraad/jupyter)
  * [Kubernetes](https://gitlab.com/gbraad/kubernetes-client) client
  * [Mono](https://gitlab.com/gbraad/mono)
  * [OpenStack](https://gitlab.com/gbraad/openstack-client) client
  * [ostreetests](https://gitlab.com/gbraad/ostreetests)
  * [Openshift](https://gitlab.com/gbraad/openshift-client) client


### Proxies (at GitLab)

  * [Alpine](https://gitlab.com/gbraad/alpine)
  * [CentOS](https://gitlab.com/gbraad/centos) + custom
  * [Debian](https://gitlab.com/gbraad/debian)
  * [Fedora](https://gitlab.com/gbraad/fedora) + custom
  * [openSUSE](https://gitlab.com/gbraad/opensuse)
  * [Photon](https://gitlab.com/gbraad/vmware-photon)
  * [Ubuntu](https://gitlab.com/gbraad/ubuntu)

### Test wrappers (at GitLab)

  * [CentOS min](https://gitlab.com/gbraad/centosmin)
  * [Fedora min](https://gitlab.com/gbraad/fedoramin)


## Get private IP address of container

```
docker inspect --format="{{.NetworkSettings.IPAddress}}" [container id or name]
```


## Use LVM thin pool

```
$ systemctl stop docker
$ rm -rf /var/lib/docker
$ pvcreate /dev/vdb
$ vgcreate docker_vol /dev/vdb
$ vi /etc/sysconfig/docker-storage-setup 
```

    VG="docker_vol"

```
$ docker-storage-setup
```


## Use OverlayFS


### Docker container engine
If running Docker engine from docker.io:
```
systemctl stop docker
vi /usr/lib/systemd/system/docker.servic
```

and add `--storage-driver=overlay` to `ExecStart`.

```
rm -rf /var/lib/docker/  # This will remove your existing images/containers
systemctl daemon-reload
systemctl start docker
```

Verify with:

```
docker info | grep Storage
```

`overlay` will be shown instead of `devicemapper`.


### Fedora/CentOS

For Fedora/CentOS you can use `docker-storage-setup` to setup the storage driver.

To use OverlayFS
```
$ systemctl stop docker
$ vi /etc/sysconfig/docker-storage
```

    `DOCKER_STORAGE_OPTIONS= -s overlay`

```
$ rm -rf /var/lib/docker
$ docker-storage-setup
$ systemctl start docker
```

Note:
  * _This involves removing existing images and containers!_
  * _SELinux is not supported with the OverlayFS driver_

[Source](http://www.projectatomic.io/blog/2015/06/notes-on-fedora-centos-and-docker-storage-drivers/), [more info](https://docs.docker.com/engine/userguide/storagedriver/selectadriver/)
[GitHub](https://github.com/projectatomic/docker-storage-setup)


## Registry

  * [Registry API v2](https://docs.docker.com/registry/spec/api/)


## SELinux errors
If you get 'permission denied' from the container and you use CentOS/Fedora, you likely ran into SELinux permission not given:

Relabel the target with:
```
$ chcon -Rt svirt_sandbox_file_t [location]
```


## Remove untagged images
```
$ docker rmi $(docker images -a | grep "^<none>" | awk '{print $3}')
```

```
$ docker rmi $(docker images -f "dangling=true" -q)
```


## Export / Import
Used for containers
```
$ docker run -d --name devenv gbraad/c9ide:f24
$ docker stop devenv
$ docker export devenv > devenv-f24.tar
```

## Save / Load
Used for images

```
$ docker save gbraad/c9ide:c7 > c9ide-c7.tar 
```



## My repositories (on GitHub; after rename)

### Source for images
  * https://github.com/gbraad/dockerfile-openshift-client
  * https://github.com/gbraad/dockerfile-gitbook-server
  * https://github.com/gbraad/dockerfile-openstack-keystone
  * https://github.com/gbraad/dockerfile-gollum
  * https://github.com/gbraad/dockerfile-c9ide
  * https://github.com/gbraad/dockerfile-flatpak-builder-gnome
  * https://github.com/gbraad/dockerfile-flatpak-builder-freedesktop
  * https://github.com/gbraad/dockerfile-jupyter
  * https://github.com/gbraad/dockerfile-mono
  * https://github.com/gbraad/dockerfile-openstack-client
  * https://github.com/gbraad/dockerfile-kubernetes-client
  * https://github.com/gbraad/dockerfile-glusterfs
  * https://github.com/gbraad/dockerfile-flatpak
  * https://github.com/gbraad/dockerfile-httpd

### Containerized `libvirt`
  * https://github.com/gbraad/dockerfile-image-libvirt
  * https://github.com/gbraad/dockerfile-image-libvirt-client

### Brew of wellknown distributions
  * https://github.com/gbraad/dockerfile-brew-debian
  * https://github.com/gbraad/dockerfile-brew-ubuntu-core
  * https://github.com/gbraad/dockerfile-brew-fedora
  * https://github.com/gbraad/dockerfile-alpine

### Source for GitLab proxies/container registries
  * https://github.com/gbraad/container-registry-cirros
  * https://github.com/gbraad/container-registry-arm
  * https://github.com/gbraad/container-registry-steam
  * https://github.com/gbraad/container-registry-fedora
  * https://github.com/gbraad/container-registry-centos
  * https://github.com/gbraad/container-registry-opensuse
  * https://github.com/gbraad/container-registry-photon
  * https://github.com/gbraad/container-registry-debian
  * https://github.com/gbraad/container-registry-docker
  * https://github.com/gbraad/container-registry-ubuntu
