Docker
======


## Get private IP address of container

```
docker inspect --format="{{.NetworkSettings.IPAddress}}" [container id or name]
```


## Registry

  * [Registry API v2](https://docs.docker.com/registry/spec/api/)
