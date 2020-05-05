Cockpit
=======


  * [Cockpit](http://www.projectatomic.io/docs/cockpit/)
  * [Homepage](http://cockpit-project.org)


## Install

On Fedora 24
```
$ dnf update
$ dnf install docker
$ systemctl enable docker
$ systemctl start docker
$ dnf install cockpit cockpit-docker
$ systemctl enable cockpit.socket
$ systemctl start cockpit
$ firewall-cmd --add-service=cockpit
$ setenforce 0  # disable selinux :-(
```

Open http://[hostname/ip]:9090
