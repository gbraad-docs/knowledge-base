GitLab
======

  * [Homepage](http://gitlab.com/)


## Deployment

  * Ansible [playbook](https://github.com/gbraad/ansible-playbook-gitlab)


## `pyapi-gitlab`

  * [GitHub](https://github.com/pyapi-gitlab/pyapi-gitlab)

### Usage
```
import gitlab
import requests.packages.urllib3
requests.packages.urllib3.disable_warnings()
lab = gitlab.Gitlab(host="gitlab.com",token="fdsf")
project = lab.getproject("gbraad/gauth")
issues = lab.getprojectissues(project["id"])
```
