Linux
=====


## Swapfile

```
dd if=/dev/zero of=/.swapfile bs=1M count=1024
chmod 0600 /.swapfile
mkswap -f /.swapfile
sudo swapon /.swapfile
```

`/etc/fstab`
```
/.swapfile	swap	swap	defaults	0	0
```


## Distributions

### Atomic

  * [Getting started](http://www.projectatomic.io/download/)


#### Fedora Atomic

  * [Landingpage](https://getfedora.org/cloud/download/atomic.html)
  * [Download images](https://mirrors.kernel.org/fedora-alt/atomic/stable/Cloud-Images/x86_64/Images/)


#### CentOS Atomic

  * [Landingpage](https://wiki.centos.org/SpecialInterestGroup/Atomic/Download/)
  * [Download images](http://cloud.centos.org/centos/7/atomic/images/)
