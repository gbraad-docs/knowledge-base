Photon OS
=========


  * [GitLab](https://gitlab.com/gbraad/photon) buildwrapper
  * [Unofficial mirror](https://gitlab.com/unofficial-mirrors/photon) on GitLab, [my fork](https://gitlab.com/gbraad/vmware-photon)


Install on OpenStack
--------------------

```
$ wget https://bintray.com/artifact/download/vmware/photon/photon-ami-1.0-13c08b6.tar.gz
$ tar -zxvf photon-ami-1.0-13c08b6.tar.gz
$ openstack image create photon --file photon-ami-1.0-13c08b6.raw --container-format bare --disk-format raw
$ openstack server create photon --flavor m1.tiny --image photon --key-name c9
$ ssh root@[ipaddress]
root@photon [ ~ ]#
```


## Disable DHCP
```
root [ ~ ]# mv /etc/systemd/network/10-dhcp-eth0.network  /etc/systemd/network/10-static-eth0.network
root [ ~ ]# vi /etc/systemd/network/10-static-eth0.network
root [ ~ ]# cat /etc/systemd/network/10-static-eth0.network
[Match]
Name=eth0

[Network]
Address=192.168.79.134/24
Gateway=192.168.79.2
DNS=8.8.8.8
Domains=photon.local
root [ ~ ]# systemctl restart systemd-networkd.service
```


## Download a Docker container (https://registry.hub.docker.com/)
```
$ docker pull vmwarecna/nginx
```


## Start Docker Container
```
$ docker run -d -p 80:80 vmwarecna/nginx
```

  * `-d`       - Run the container in the background
  * `-p 80:80` - Publish the container's port to the host


## Display the public-facing port that is NAT-ed to the container 
```
$ docker port 5f6b0e03c6de
```


## Automatically start Docker containers at boot time
```
$ docker run --restart=always -d -p 80:80 vmwarecna/nginx
```
