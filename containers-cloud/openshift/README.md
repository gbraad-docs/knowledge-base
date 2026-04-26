# OpenShift

Red Hat's enterprise Kubernetes distribution. OpenShift Origin (now OKD) is the upstream community version.

- [minishift](minishift.md) - local single-node OpenShift for development

## Client installation

```bash
# CentOS/RHEL
yum install -y centos-release-openshift-origin origin-clients

# Fedora
dnf install -y origin-clients
```

## Start a local cluster

```bash
oc cluster up
```

```bash
oc login http://localhost:8443
```

Web console: http://localhost:8443

## Ansible deployment

```bash
git clone https://github.com/openshift/openshift-ansible -b master
```

- [openshift-ansible](https://github.com/openshift/openshift-ansible)

## Resources

- [OpenShift Origin releases](https://github.com/openshift/origin/releases)
- [OKD (community)](https://www.okd.io/)
