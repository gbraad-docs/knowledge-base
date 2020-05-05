Libvirt/virsh
=============

## Create snapshot

```
$ virsh snapshot-create-as [vmname] snapshot1 "Before installation" --disk-only --atomic
```


## Start and stop

### Start

```
$ virsh start [vmname]
```

### Stop (forcibly)

```
$ virsh destroy [vmname]
```

### ACPI events

  * Reboot

```
$ virsh reboot [vmname]
```

```
$ sudo su - stack -c "virsh reboot undercloud"
```

  * Shutdown

```
$ virsh shutdown [vmname]
```


## Configure libvirt pool
Create a libvirt persistent pool and start it:
```
$ virsh pool-define-as --type=dir --name=default --target=/var/lib/libvirt/images
$ virsh pool-autostart default
$ virsh pool-start default
```

`/var/lib/libvirt/images` is where QEMU QCOW images will be stored, so make sure this directory is attached to a file system with sufficient storage.