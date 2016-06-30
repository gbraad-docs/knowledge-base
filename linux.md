Linux
=====


## Swapfile

```
dd if=/dev/zero of=/.swapfile bs=1M count=1000
mkswap /.swapfile
sudo swapon /.swapfile
```

`/etc/fstab`
```
/.swapfile	swap	swap	defaults	0	0
```
