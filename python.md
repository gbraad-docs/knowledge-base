Python
======


## Install in userhome using `pip`

```
pip install --user [name]
```

## Install specific version using `pip`

```
pip install ansible==2.0
```

## Install virtualenv

```
dnf install python-virtualenv
```

```
dnf install python-pip
pip install virtualenv
```

## Create (and activate) virtualenv

```
mkdir .venv
virtualenv .venv
source .venv/bin/activate
```

## Debugging: trigger trace/breakpoint

Insert the following somewhere in your code:
```
import pdb; pdb.set_trace()
```

More information: [Tools training](https://github.com/gbraad/tools-training/blob/master/md/slides.md#pdb)
