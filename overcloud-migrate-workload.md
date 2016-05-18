Migrate workload
================


  * http://tripleo.org/post_deployment/quiesce_compute.html

```
$ . overcloudrc  # admin credentials for the overcloud
$ nova service-list
$ nova service-disable <service-host> nova-compute
```
