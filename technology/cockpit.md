Cockpit
=======


  * [Cockpit](http://www.projectatomic.io/docs/cockpit/)
  * [Homepage](http://cockpit-project.org)


```
$ dnf update
$ dnf install docker
$ systemctl enable docker
$ systemctl start docker
$ dnf install cockpit
$ systemctl enable cockpit.socket
$ systemctl start cockpit
$ firewall-cmd --add-service=cockpit
$ setenforce 0
```

