Open edX
========


Deployment
----------

  * https://github.com/gbraad/deploy-openedx
  * https://openedx.atlassian.net/wiki/display/OpenOPS/Open+edX+Installation+Options


### In a few steps

#### 1. Update and upgrade Ubuntu 14.04
```
sudo apt-get update
sudo apt-get upgrade
```

#### 2. Install dependencies
```
sudo apt-get install -y build-essential software-properties-common python-software-properties curl git-core libxml2-dev libxslt1-dev python-pip python-apt python-dev  libxmlsec1-dev swig
sudo pip install --upgrade pip
sudo pip install --upgrade virtualenv
```

#### 3. Clone the configuration repository
```
$ cd /var/tmp
$ git clone -b release https://github.com/edx/configuration
```

#### 4. Allow password-based SSH authentication
`configuration/playbooks/roles/common/defaults/main.yml`
```
COMMON_SSH_PASSWORD_AUTH to "yes"
```

#### 5. Install requirements
```
$ cd /var/tmp/configuration
$sudo pip install -r requirements.txt
```

#### 6. Run the edx_sandbox.yml playbook in the configuration/playbooks directory
```
$ cd /var/tmp/configuration/playbooks && sudo ansible-playbook -c local ./edx_sandbox.yml -i "localhost,"
```

#### 7. Open edX
[http://localhost](http://localhost)


Troubleshooting
---------------

### `no alternatives for libblas.so.3gf`

```
TASK: [edxapp | code sandbox | Use libblas for 3gf] *************************** 
failed: [localhost] => {"changed": true, "cmd": ["update-alternatives", "--set", "libblas.so.3gf", "/usr/lib/libblas/libblas.so.3gf"], "delta": "0:00:00.002302", "end": "2016-09-18 09:26:37.399477", "rc": 2, "start": "2016-09-18 09:26:37.397175", "warnings": []}
stderr: update-alternatives: error: no alternatives for libblas.so.3gf

FATAL: all hosts have already failed -- aborting
```

`cd /edx/app/edx_ansible/edx_ansible/`
```
diff --git a/playbooks/roles/edxapp/tasks/python_sandbox_env.yml b/playbooks/roles/edxapp/tasks/python_sandbox_env.yml
index 1793cb5..08070b3 100644
--- a/playbooks/roles/edxapp/tasks/python_sandbox_env.yml
+++ b/playbooks/roles/edxapp/tasks/python_sandbox_env.yml
@@ -2,11 +2,11 @@
 # MITx 6.341x course.
 # TODO: Switch to using alternatives module in 1.6
 - name: code sandbox | Use libblas for 3gf
-  command: update-alternatives --set libblas.so.3gf /usr/lib/libblas/libblas.so.3gf
+  command: update-alternatives --set libblas.so.3 /usr/lib/libblas/libblas.so.3
 
 # TODO: Switch to using alternatives module in 1.6
 - name: code sandbox | Use liblapac for 3gf
-  command: update-alternatives --set liblapack.so.3gf /usr/lib/lapack/liblapack.so.3gf
+  command: update-alternatives --set liblapack.so.3 /usr/lib/lapack/liblapack.so.3
 
 - name: code sandbox | Create edxapp sandbox user
   user: name={{ edxapp_sandbox_user }} shell=/bin/false home={{ edxapp_sandbox_venv_dir }}
```

```
$ vi /edx/app/edx_ansible/edx_ansible/playbooks/roles/edxapp/tasks/python_sandbox_env.yml
# change .3gf to .3
$ ansible-playbook -i localhost, -c local vagrant-fullstack.yml -e@$ANSIBLE_ROOT/server-vars.yml -e@$ANSIBLE_ROOT/extra-vars.yml --skip-tags install:code
```

