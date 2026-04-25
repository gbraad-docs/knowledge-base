Install software
================


## Install PackStack (bash)
```
curl -sSL https://raw.githubusercontent.com/gbraad/oneliners/master/install_packstack.sh | bash
```

## Install C9 (bash)
```
curl -sSL https://raw.githubusercontent.com/gbraad/oneliners/master/install_c9.sh | bash
```

## Install C9 (ansible)
```
curl -sSL https://github.com/gbraad/ansible-playbooks/raw/master/playbooks/install-c9sdk.yml -o install-c9sdk.yml
ansible-playbook install-c9sdk.yml
```
