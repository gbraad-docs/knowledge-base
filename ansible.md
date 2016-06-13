Ansible
=======


## Passing variables from the command line

```
---

- hosts: '{{ hosts }}'
  remote_user: '{{ user }}'

  tasks:
     - ...
```

```
ansible-playbook release.yml --extra-vars "hosts=vipers user=starbuck"
```

Ref: [source](http://docs.ansible.com/ansible/playbooks_variables.html#passing-variables-on-the-command-line)


## Executable playbooks

```
#!/usr/bin/env ansible-playbook -i ../hosts -K
---
- hosts: ...
```
