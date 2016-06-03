Personal scratchpad
===================

Just a collection of some random notes

_Gerard Braad <me@gbraad.nl>_


## OpenStack

### Needed to install OpenStack client on Ubuntu 14.04 (or C9)

```
pip install -U pyopenssl ndg-httpsclient pyasn1
```

### OpenStack client

```
pip install python-openstackclient
```


## Git

### Delete commits from a branch
Careful: git reset --hard WILL DELETE YOUR WORKING DIRECTORY CHANGES

```
  git reset --hard HEAD~1
```

### Set vim as default editor
Needed on Ubuntu and C9 containers

```
git config --global core.editor "vim"
```

```
export GIT_EDITOR=vim
```


## Install software

### Install PackStack (bash)
```
curl -sSL https://raw.githubusercontent.com/gbraad/oneliners/master/install_packstack.sh | bash
```

### Install C9 (bash)
```
curl -sSL https://raw.githubusercontent.com/gbraad/oneliners/master/install_c9.sh | bash
```

### Install C9 (ansible)
```
curl -sSL https://github.com/gbraad/ansible-playbooks/raw/master/playbooks/install-c9sdk.yml -o install-c9sdk.yml
ansible-playbook install-c9sdk.yml
```
