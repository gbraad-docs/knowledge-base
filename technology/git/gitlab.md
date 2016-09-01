GitLab
======

  * [Homepage](http://gitlab.com/)


## Deployment

  * Ansible [playbook](https://github.com/gbraad/ansible-playbook-gitlab)


## Access API using `pyapi-gitlab`

  * [GitHub](https://github.com/pyapi-gitlab/pyapi-gitlab)

### Usage
```python
import gitlab
import requests.packages.urllib3
requests.packages.urllib3.disable_warnings()
lab = gitlab.Gitlab(host="gitlab.com",token="fdsf")
project = lab.getproject("gbraad/gauth")
issues = lab.getprojectissues(project["id"])

table = PrettyTable(["Title", "Labels", "State"])
table.align["Title"] = "l"

issues = lab.getprojectissues(project["id"])
for issue in issues:
    title = issue['title']
    labels = ",".join(issue['labels'])
    state = issue['state']
    table.add_row([title, labels, state])

print(table)
```
