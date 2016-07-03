Docker
======


## Get private IP address of container

```
docker inspect --format="{{.NetworkSettings.IPAddress}}" [container id or name]
```


## Use OverlayFS

If running Docker engine from docker.io:
```
vi /usr/lib/systemd/system/docker.service
```

and add `--storage-driver=overlay` to `ExecStart`.

For Fedora/CentOS you can use `docker-storage-setup` to setup the storage driver.

[Source](http://www.projectatomic.io/blog/2015/06/notes-on-fedora-centos-and-docker-storage-drivers/)


## Registry

  * [Registry API v2](https://docs.docker.com/registry/spec/api/)
