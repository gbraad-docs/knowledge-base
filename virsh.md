Libvirt/virsh
=============

## Reboot virtual machine

```
virsh reboot [vmname]
```

```
sudo su - stack -c "virsh reboot undercloud"
```


## Create snapshot

```
virsh snapshot-create-as [vmname] snapshot1 "Before installation" --disk-only --atomic
```


## Start and stop

### Start

```
virsh start [vmname]
```

### Stop (forcibly)

```
virsh destroy [vmname]
```

### ACPI events

  * Reboot

```
virsh reboot [vmname]
```

  * Shutdown

```
virsh shutdown [vmname]
```
