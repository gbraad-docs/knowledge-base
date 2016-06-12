Docker
======


## Get private IP address of container

```
docker inspect --format="{{.NetworkSettings.IPAddress}}" [container id or name]
```
