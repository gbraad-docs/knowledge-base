diskimaging
===========

```
yum install -y python-pip
yum install -y python-virtualenv

virtualenv .
source bin/activate
pip install dib-utils pyyaml


export ELEMENTS_PATH=tripleo-image-elements/elements
diskimage-builder/bin/disk-image-create -a i386 -o bootstrap -u base vm bootstrap local-config stackuser heat-cfntools


diskimage-builder/bin/disk-image-create -a amd64 -o compute-image fedora nova-compute nova-kvm
```

The above `disk-image-create` commands do not work.


Able to generate a basic image using:

```
# diskimage-builder/bin/disk-image-create -a amd64 -o compute-image centos
```

after including the following to `/etc/hosts`:

    162.252.80.138 cloud.centos.org


Will look at using the `quickstart.sh` playbook for this:


---

Prebuilt images that are used are:

  * ${OPT_UNDERCLOUD_URL:=http://artifacts.ci.centos.org/artifacts/rdo/images/${RELEASE}/delorean/stable/undercloud.qcow2}
    /tripleo-quickstart/quickstart.sh
  * image_url: http://artifacts.ci.centos.org/rdo/images/{{ release }}/delorean/stable/undercloud.qcow2
    /tripleo-quickstart/playbooks/roles/libvirt/defaults/main.yml:

About 2.4G in size. These were made using the generic cloud image:

  * http://cloud.centos.org/centos/7/images/CentOS-7-x86_64-GenericCloud.qcow2 (800M)


Images can be cached or specified using a download url (needs md5):

    /home/[username:stack]/.quickstart/latest.qcow2


---

  * https://github.com/redhat-openstack/ansible-role-tripleo-image-build/blob/master/tasks/dib_build.yml#L45-L96

# Build the ironic-python-agent ramdisk

    source {{ artib_working_dir }}/dib-optional-env-inject.sh
    disk-image-create \
    -a amd64 \
    -o {{ artib_working_dir }}/ironic-python-agent \
    centos7 \
    dhcp-all-interfaces \
    dynamic-login \
    selinux-permissive \
    pip-and-virtualenv-override \
    ironic-agent \
    -p python-hardware-detect \
    --min-tmpfs 5


# Build overcloud-full image

    source {{ artib_working_dir }}/dib-optional-env-inject.sh
    disk-image-create \
    -a amd64 \
    -o {{ artib_working_dir }}/overcloud-full.qcow2 \
    centos7 \
    baremetal \
    dhcp-all-interfaces \
    stable-interface-names \
    grub2 \
    dynamic-login \
    selinux-permissive \
    element-manifest \
    heat-config-puppet \
    heat-config-script \
    hosts \
    network-gateway \
    os-net-config \
    sysctl \
    hiera \
    pip-and-virtualenv-override \
    --min-tmpfs

# Convert the overcloud image to an undercloud image

    virt-customize -m {{ artib_virt_customize_ram }}
    --smp {{ artib_virt_customize_cpu }}
    -a {{ artib_working_dir }}/undercloud-base.qcow2
    --run {{ artib_working_dir }}/undercloud-convert.sh
    

To be changed to:

  * https://review.gerrithub.io/#/c/274691/11/tasks/dib_build.yml