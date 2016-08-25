Ceph
====


Deployment
----------

  * [ceph-ansible](https://github.com/ceph/ceph-ansible)
  * [ceph-docker](https://github.com/ceph/ceph-docker)


### Play `ceph-ansible` with Atomic hosts

`hosts`
```
[mons]
atomic-01 ansible_ssh_host=10.3.0.15
atomic-02 ansible_ssh_host=10.3.0.18
atomic-03 ansible_ssh_host=10.3.0.14

[osds]
atomic-04 ansible_ssh_host=10.3.0.16
atomic-05 ansible_ssh_host=10.3.0.17
atomic-06 ansible_ssh_host=10.3.0.19
```

`ansible.cfg`
```
[defaults]
retry_files_enabled = False
host_key_checking = False
remote_user = centos
```

```
$ ansible -i hosts all -m ping
atomic-05 | SUCCESS => {
    "changed": false, 
    "ping": "pong"
}
atomic-01 | SUCCESS => {
    "changed": false, 
    "ping": "pong"
}
atomic-04 | SUCCESS => {
    "changed": false, 
    "ping": "pong"
}
atomic-03 | SUCCESS => {
    "changed": false, 
    "ping": "pong"
}
atomic-06 | SUCCESS => {
    "changed": false, 
    "ping": "pong"
}
atomic-02 | SUCCESS => {
    "changed": false, 
    "ping": "pong"
}
```

```
$ cp site.yml.sample site.yml
$ cp group_vars/all.sample group_vars/all
$ cp group_vars/mons.sample group_vars/mons
$ cp group_vars/osds.sample group_vars/osds
```

... **in progress** ...


Building
--------

### Build wrapper

  * https://gitlab.com/gbraad/ceph-build (http://github.com/gbraad/ceph-build-wrapper)


### Standard build using cmake
The following GitLab CI runner script can run on a shared runner. it performs a basic build using `cmake`. Note: artifacts are not stored.

`.gitlab-ci.yml`

```
image: registry.gitlab.com/gbraad/centos:7

before_script:
  - yum install -y git
  - git clone git://github.com/ceph/ceph --depth 1
  - cd ceph
  - git submodule update --init --recursive
  - ./install-deps.sh

build:
  script:
    - ./do_cmake.sh
    - cd build
    - make
```

Links
-----

  * [Ceph](http://ceph.com/) homepage
