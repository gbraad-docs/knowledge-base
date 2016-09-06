Cloud 9 IDE
===========

This scratchpad will detail how to setup a powerful development environment in the cloud, under your control!

For more information about Cloud 9, have a look at:

  * [Homepage](http://c9.io)
  * [Source](https://github.com/c9/core), [Team](https://github.com/c9) with all the plugins


## Installation
To install, choose your preferred method.

### Oneliner
This will install C9 core (SDK and plugins) and needed dependencies on any machine or virtual machine, by using a single command.

```
$ curl -sSL https://raw.githubusercontent.com/gbraad/oneliners/master/install_c9.sh | bash
```

### Ansible
This will install C9 core (SDK and plugins) and needed dependenciesusing, using an Ansible playbook. It is assumed that you target your localhost with this.

```
$ curl -sSL https://github.com/gbraad/ansible-playbooks/raw/master/playbooks/install-c9sdk.yml -o install-c9sdk.yml`
$ ansible-playbook install-c9sdk.yml
```

Also available available as a role for use in Ansible playbooks: [GitHub](github.com/gbraad/ansible-role-c9sdk), [Galaxy](https://galaxy.ansible.com/gbraad/c9sdk/). This can be helpful if you want to install to several machines. You first need to install the role:

```
$ ansible-galaxy install gbraad.c9sdk
```

After which you can define the playbook `deploy-c9sdk-workstations.yml`:
```
- name: Install C9 SDK
  hosts: workstations
  roles:
    - gbraad.c9sdk
```

And substitute your targetes in the `hosts` file:

```
[workstations]
192.168.1.[2:10]
```

you can deploy to multiple machines with:

```
$ ansible-playbook -i hosts deploy-c9sdk-workstations.yml
```


### Docker
The Docker container is based on standard images, provided by CentOS, Fedora and Ubuntu, and install `curl`, `ansible` and `python` to allow the Docker build process to deploy using the Ansible playbook. This means that the containers are minimal in setup, but allow you to fully customize the environment.

Using the following alias, you can easily develop on code in the current directory from your web browser.
```
$ alias c9ide='docker run -it --rm -v `pwd`:/workspace gbraad/c9ide:c7'
$ cd ~/Projects/[something]
$ c9ide
```

Different flavours exist, such as 'c7' (CentOS 7), 'f24' (Fedora 24) and 'u16.04' (Ubuntu Xenial).

Source: [GitLab](https://gitlab.com/gbraad/c9ide), [GitHub](https://github.com/gbraad/docker-c9ide), [Docker hub](https://hub.docker.com/r/gbraad/c9ide)


### Docker cloud

Create a new account, or re-use your Docker hub acount, at [Docker cloud](https://cloud.docker.com). Now open the [Node list](https://cloud.docker.com/node/cluster/list/) and Bring your own node. Setting up the first node is free of charge. It explaisn you how to deploy the docker cloud engine on a machine with a simple one-liner.

Now open the [Service list](https://cloud.docker.com/container/list/) and create a new service. Search Docker hub for `gbraad/c9ide`, and confirm with clicking Select. Below are the settings to can/need to be changed to allow you to start the C9 service on your own Docker cloud node.

#### General settings
Select the preferred flavor from the image selecter.

  * `c7` is based on CentOS 7
  * `f24` is based on Fedora 24
  * `u1604` is based on Ubuntu 16.04 (xenial)

If you want to know what is in these images, please see the source under the [Docker](#docker) header on this page.

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
Environment variables section and happy coding! Be aware that the first load of the page might take some time, as about 8.5M of data (JavaScript and CSS) need to be trasnferred.

_Note: Be sure to push your code out to a git repository or attach a volume to `/workspace`. Also, the container runs on port 80, which is unprotected (no TLS), which means that you should not work on super-secret code or use ultra-secure user/pass combination, as these will be littered all over the internet._


### Self-hosted
I wrote an article which improves in the previous trial with Docker cloud, by hosting the environment yourself, supported by a service container for Nginx and certificate provided by Let's Encrypt.

  * [Published](http://gbraad.nl/blog/setting-up-a-powerful-self-hosted-ide-in-the-cloud.html) on my blog
  * Source at [GitLab](https://gitlab.com/gbraad/blog-content/blob/master/0004-setup-self-hosted-cloud9.md), [GitHub](https://github.com/gbraad/blog-content/blob/master/0004-setup-self-hosted-cloud9.md)
  * [Published](https://gbraad.gitbooks.io/blog-articles/content/0004-setup-self-hosted-cloud9.html) on GitBook
