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


## Use OverlayFS

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

For Fedora/CentOS you can use `docker-storage-setup` to setup the storage driver.

Note:
_This involves removing existing images and containers!_

[Source](http://www.projectatomic.io/blog/2015/06/notes-on-fedora-centos-and-docker-storage-drivers/), [more info](https://docs.docker.com/engine/userguide/storagedriver/selectadriver/)


## Registry

  * [Registry API v2](https://docs.docker.com/registry/spec/api/)
