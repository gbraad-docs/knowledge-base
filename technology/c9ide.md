Cloud 9 IDE
===========

a web-based IDE

  * [Homepage](http://c9.io)
  * [Source](https://github.com/c9/core)


## Installation

### Oneliner

```
$ curl -sSL https://raw.githubusercontent.com/gbraad/oneliners/master/install_c9.sh | bash
```

### Ansible

```
$ curl -sSL https://github.com/gbraad/ansible-playbooks/raw/master/playbooks/install-c9sdk.yml -o install-c9sdk.yml`
$ ansible-playbook install-c9sdk.yml
```

### Docker

```
$ alias c9ide='docker run -it --rm -v `pwd`:/workspace gbraad/c9ide:c7'
$ cd ~/Projects/[something]
$ c9ide
```

[Source](https://github.com/gbraad/docker-c9ide)


### Docker cloud

Create a new service and search Docker hub for `gbraad/c9ide`, and confirm with clicking Select.

#### General settings
Select the preferred flavor from the image selecter.

  * `c7` is based on CentOS 7
  * `f24` is based on Fedora 24
  * `u1604` is based on Ubuntu 16.04 (xenial)

_Note: Images with the `-devenv` suffix are based on my [devenv](htttp://github.com/gbraad/devenv) environment. Unless you know what you are doing, it is best to not use these._


#### Ports
```
Container port    Protocol   Published   Node port
8181              tcp        X           80
```

#### Environment variables
```
Name              Value
PASSWORD          pass
USERNAME          user
```

After clicking `Create & Deploy` you will be shown the endpoints Open the Service endpoint for `tcp/80` in your browser
(without the tcp:// prefix) and you will be shown a authentication prompt. User the parameters you set from the
Environment variables section and happy coding!

_Note: Be sure to push your code out to a git repository or attach a volume to `/workspace`. Also, the container runs on port 80, which is unprotected (no TLS), which means that you should not work on super-secret code or use ultra-secure user/pass combination, as these will be littered all over the internet._
