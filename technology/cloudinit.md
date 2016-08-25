Cloud-init
==========


## Post-creation script

A simple example of using a post-creation script, as possible with OpenStack, is as follows:

```
#cloud-config
password: passw0rd
chpasswd: { expire: False }
ssh_pwauth: True
```

This will set a password for the login user (differs per image?!) and allow this user to login using `ssh`.


## Force run of cloud-init script

You can just run it like this:
```
/usr/bin/cloud-init -d init
```
This runs the cloud init setup with the initial modules. Note: the `-d` option is for debug. 

If want to run all the modules you have to run:

```
/usr/bin/cloud-init -d modules
```

Keep in mind that the second time you run these it doesn't do much since it has already run at boot time. To force to run after boot time you can run from the command line:

```
( cd /var/lib/cloud/ && sudo rm -rf * )
```


## Always run cloud-init scripts

In 11.10, 12.04 and later, you can achieve this by making the 'scripts-user' run 'always'. In `/etc/cloud/cloud.cfg` you'll see something like:
```
cloud_final_modules:
 - rightscale_userdata
 - scripts-per-once
 - scripts-per-boot
 - scripts-per-instance
 - scripts-user
 - keys-to-console
 - phone-home
 - final-message
```

This can be modified after boot, or `cloud-config` data overriding this stanza can be inserted via `user-data`. Ie, in user-data you can provide:

```
#cloud-config
cloud_final_modules:
 - rightscale_userdata
 - scripts-per-once
 - scripts-per-boot
 - scripts-per-instance
 - [scripts-user, always]
 - keys-to-console
 - phone-home
 - final-message
```

[More info](http://bazaar.launchpad.net/~cloud-init-dev/cloud-init/trunk/view/head:/doc/examples/cloud-config.txt)


## Config drive

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


## Automated creation of configdrive

  * Project at [GitLab](https://gitlab.com/gbraad/cloud-init-configdrive) and [output](https://gitlab.com/gbraad/cloud-init-configdrive/builds/2572382/artifacts/browse/public/)

