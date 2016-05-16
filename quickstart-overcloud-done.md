After the overcloud deployment
==============================

Login to overcloud nodes is possible from the undercloud with:

```
$  ssh -i ~/.ssh/id_rsa heat-admin@192.0.2.16
```

for instance. All the host entries can be found in `/etc/hosts`
(This is the output of `heat output-show -F raw overcloud`).
