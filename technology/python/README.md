Python
======

  * [Django](django.md)


## Setup development

```
$ python -m ensurepip --user
$ python -m pip install --user --upgrade pip
$ python -m pip install --user --upgrade virtualenv
```

```
$ python -m virtualenv lets-go
$ . ./lets-go/bin/activate
(lets-go) $ _
```

[Source](https://glyph.twistedmatrix.com/2016/08/python-packaging.html)

```
$ python3 -m venv lets-go
$ source ./lets-go/bin/activate
(lets-go) $ pip install -U pip setuptools
```


## Install `pip`
```
$ yum -y install epel-release
$ yum -y install python-pip
```

```
$ apt-get -y install python-pip
```


## Upgrade `pip`
```
$ pip install -U pip
```


## Install requirements using `pip`
```
$ pip install -U -r requirements.txt
```


## Install in userhome using `pip`
```
$ pip install --user [name]
```


## Install specific version using `pip`
```
$ pip install ansible==2.0
```


## Install virtualenv
```
$ dnf install python-virtualenv
```

```
$ dnf install python-pip
$ pip install virtualenv
```


## Create (and activate) virtualenv
```
$ mkdir .venv
$ virtualenv .venv
$ source .venv/bin/activate
```


## Debugging: trigger trace/breakpoint

Insert the following somewhere in your code:
```
$ import pdb; pdb.set_trace()
```

More information: [Tools training](https://github.com/gbraad/tools-training/blob/master/md/slides.md#pdb)


## Debugging using 'q'
```
$ pip install q
```

 * Output is sent to $TMPDIR/q
 * replace `print` with `q`
 * use the `@q` decorator

[Source](https://pypi.python.org/pypi/q)


Troubleshooting
---------------

### Needed on Ubuntu 14.04 when dealing with oudated SSL and `InsecurePlatformWarning`

```
$ pip install -U pyopenssl ndg-httpsclient pyasn1
```
