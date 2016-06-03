# Personal scratchpad
Just some random notes


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
```
git config --global core.editor "vim"
```

```
export GIT_EDITOR=vim
```

