Building Ceph
=============

https://gitlab.com/gbraad/ceph-build

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
