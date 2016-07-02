Cloud-init
==========

Most virtual platforms will provision instances with no root password, so it's necessary to supply a
password or key to log in using cloud-init. If you're using a virtualization application without
cloud-init support, such as virt-manager or VirtualBox, you can create a simple iso image to provide
a key or password to your image when it boots.

To create this iso image, you must first create two text files.

Create a file named `meta-data` that includes an "instance-id" name and a "local-hostname." For instance:

    instance-id: instance0
    local-hostname: instance-00


The second file is named `user-data`, and includes password and key information. For instance:

    #cloud-config
    password: secrete
    chpasswd: {expire: False}
    ssh_pwauth: True
    ssh_authorized_keys:
      - ssh-rsa AAA...EFus user1@mydomain.com
      - ssh-rsa AAB...RDie user2@mydomain.com

Once you have completed your files, they need to packaged into an ISO image. For instance:

```
# genisoimage -output instance0-cidata.iso -volid cidata -joliet -rock user-data meta-data
```

You can boot from this iso image, and the auth details it contains will be passed along to your instance.

For more information about creating these `cloud-init` iso images, see [config-drive](http://cloudinit.readthedocs.org/en/latest/topics/datasources.html#config-drive).
