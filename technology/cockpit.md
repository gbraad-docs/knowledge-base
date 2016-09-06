Cockpit
=======


```
$ dnf update
$ dnf install docker
$ dnf install cockpit
$ systemctl enable cockpit.socket
$ systemctl start cockpit
$ firewall-cmd --add-service=cockpit
$ setenforce 0
```

