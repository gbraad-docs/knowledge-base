Git
===


## Delete commits from a branch
Careful: git reset --hard WILL DELETE YOUR WORKING DIRECTORY CHANGES

```
  git reset --hard HEAD~1
```

## Set vim as default editor
Needed on Ubuntu and C9 containers

```
git config --global core.editor "vim"
```

```
export GIT_EDITOR=vim
```

## Update all subdirectories
Used for a project directory with no `git submodule`.

```
#!/bin/bash
for i in $(find . -maxdepth 1 -type d)
do
    pushd $i
    git checkout master
    git pull --reb
    popd
done
```

## Git submodules

```
git submodule add [repo] [location/name]
```

```
git submodule init
```

```
git submodule update
```
