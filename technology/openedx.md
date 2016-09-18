Open edX
========


Deployment
----------

  * https://github.com/gbraad/deploy-openedx
  * https://openedx.atlassian.net/wiki/display/OpenOPS/Open+edX+Installation+Options





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
+  command: update-alternatives --set libblas.so.3g/usr/lib/libblas/libblas.so.3
 
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
```
