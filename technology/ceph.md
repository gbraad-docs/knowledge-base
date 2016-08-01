Building Ceph
=============

  * https://gitlab.com/gbraad/ceph-build (http://github.com/gbraad/ceph-build-wrapper)


## Standard build using cmake
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
