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

## Debug modules

`print` will not work, so instead use the following at the top of the module:

```
import logging
logging.basicConfig(filename="/tmp/ansible-debug.log', level=logging.DEBUG)
```

Somewhere else in the code do:
```
logging.debug('your message')
```

And then `tail` the log file:
```
$ tail -f /tmp/ansible-debug.log
```

## Debug messages

```
- debug: msg="System {{ inventory_hostname }}"
```

[Source](http://docs.ansible.com/ansible/debug_module.html)
