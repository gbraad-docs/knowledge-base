GitLab
======

  * [Homepage](http://gitlab.com/)


## Deployment

  * Ansible [playbook](https://github.com/gbraad/ansible-playbook-gitlab)
  * [Docker images](https://hub.docker.com/r/gitlab/gitlab-ce/)


### On [OpenShift](../openshift.md)

  * [OpenShift commons](https://blog.openshift.com/code-test-deploy-with-gitlab-on-openshift-commons-briefing-49/) article
  * [Gitlab blog](https://about.gitlab.com/2016/06/28/get-started-with-openshift-origin-3-and-gitlab/)
  * [OpenShift blog](https://blog.openshift.com/deploy-gitlab-openshift/)
  * [OpenShift template](https://gitlab.com/gitlab-org/omnibus-gitlab/raw/master/docker/openshift-template.json)


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
