Virtual Environment Setup Complete
==================================

Access the undercloud by:

    ssh -F $OPT_WORKDIR/ssh.config.ansible undercloud

There are scripts in the home directory to continue the deploy:

    undercloud-install.sh will run the undercloud install
    undercloud-post-install.sh will perform all pre-deploy steps
    overcloud-deploy.sh will deploy the overcloud
    overcloud-deploy-post.sh will do any post-deploy configuration
    overcloud-validate.sh will run post-deploy validation

Alternatively, you can ignore these scripts and follow the upstream docs:

First:

    openstack undercloud install
    source stackrc

Then continue with the instructions (limit content using dropdown on the left):

    http://ow.ly/Ze8nK
    http://docs.openstack.org/developer/tripleo-docs/basic_deployment/basic_deployment_cli.html#upload-images
