Kubernetes
==========

  * Hands-on-labs
    * Source: [GitHub](https://github.com/gbraad/kubernetes-handsonlabs), [GitLab](https://gitlab.com/gbraad/kubernetes-handsonlabs)
    * Published: [GitBook](https://gbraad.gitbooks.io/kubernetes-handsonlabs/content/), [HTML](http://gbraad.gitlab.io/kubernetes-handsonlabs/)


Build from source
-----------------
```
$ git clone https://github.com/kubernetes/kubernetes.git
$ cd kubernetes
$ export GOROOT=/opt/go
$ export PATH=$PATH:$PWD/_gopath/bin
$ GOPATH=$PWD/_gopath make
```


Client
------

```
$ wget http://storage.googleapis.com/kubernetes-release/release/v1.3.4/bin/linux/amd64/kubectl
$ chmod +x kubectl
```

  * [Docker container](https://github.com/gbraad/docker-kubernetes-client)


Ansible
-------

### Deployment playbook

  * [GitHub](https://github.com/gbraad/ansible-playbook-kubernetes)


### Ansible Roles
  * gbraad.docker  
    [Galaxy](https://galaxy.ansible.com/gbraad/docker/), [GitHub](https://github.com/gbraad/ansible-role-docker) / [GitLab](https://gitlab.com/gbraad/ansible-role-docker)
  * gbraad.docker-registry  
    [Galaxy](https://galaxy.ansible.com/gbraad/docker-registry/), [GitHub](https://github.com/gbraad/ansible-role-docker-registry) / [GitLab](https://gitlab.com/gbraad/ansible-role-docker-registry)
  * gbraad.kubernetes-master  
    [Galaxy](https://galaxy.ansible.com/gbraad/kubernetes-master/), [GitHub](https://github.com/gbraad/ansible-role-kubernetes-master) / [GitLab](https://gitlab.com/gbraad/ansible-role-kubernetes-master)
  * gbraad.kubernetes-node  
    [Galaxy](https://galaxy.ansible.com/gbraad/kubernetes-node/), [GitHub](https://github.com/gbraad/ansible-role-kubernetes-node) / [GitLab](https://gitlab.com/gbraad/ansible-role-kubernetes-node)
  * gbraad.kubernetes-client  
    [Galaxy](https://galaxy.ansible.com/gbraad/kubernetes-client/), [GitHub](https://github.com/gbraad/ansible-role-kubernetes-client) / [GitLab](https://gitlab.com/gbraad/ansible-role-kubernetes-client)
