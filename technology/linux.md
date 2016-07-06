Linux
=====


## Kernel

### Building (GitLab CI)

  * [mainline](https://gitlab.com/gbraad/linux-kernel-build-mainline)
  * [stable](https://gitlab.com/gbraad/linux-kernel-build-stable)
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
