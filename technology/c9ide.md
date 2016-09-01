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
Select the preferred flavor from the image selecter. At the moment there is a CentOS 7 and Fedora 24 based image.

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
