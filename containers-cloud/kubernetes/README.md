# Kubernetes

Container orchestration platform originally developed by Google, now governed by the CNCF.

- [OpenShift](../openshift/README.md) - enterprise Kubernetes distribution by Red Hat

## Quick start

```bash
# Install kubectl
curl -LO "https://dl.k8s.io/release/$(curl -Ls https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl && mv kubectl /usr/local/bin/

# Basic commands
kubectl get nodes
kubectl get pods -A
kubectl apply -f manifest.yaml
kubectl logs <pod> -f
kubectl exec -it <pod> -- bash
```

## Build from source

```bash
git clone https://github.com/kubernetes/kubernetes.git
cd kubernetes
make
```

## Ansible roles

- [gbraad.kubernetes-master](https://github.com/gbraad/ansible-role-kubernetes-master)
- [gbraad.kubernetes-node](https://github.com/gbraad/ansible-role-kubernetes-node)
- [gbraad.kubernetes-client](https://github.com/gbraad/ansible-role-kubernetes-client)
- [Deployment playbook](https://github.com/gbraad/ansible-playbook-kubernetes)

## Resources

- [Hands-on-labs](https://github.com/gbraad/kubernetes-handsonlabs)
