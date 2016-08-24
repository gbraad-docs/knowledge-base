Linux
=====


## Kernel

### Build wrappers (at GitLab)

  * [mainline](https://gitlab.com/gbraad/linux-kernel-build-mainline), [kernel-mainline.repo](https://gitlab.com/gbraad/linux-kernel-build-stable/blob/master/kernel-mainline.repo)
  * [stable](https://gitlab.com/gbraad/linux-kernel-build-stable), [kernel-stable.repo](https://gitlab.com/gbraad/linux-kernel-build-stable/blob/master/kernel-stable.repo)
  * [centos](https://gitlab.com/gbraad/linux-kernel-build-centos)


## Distributions

  * [Fedora](https://fedoraproject.org/)
  * [Ubuntu](http://ubuntu.com/)
  * [OpenSUSE](http://opensuse.org/)
  * [Debian](http://debian.org/)
  * ...


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
