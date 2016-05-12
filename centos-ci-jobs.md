CI Jobs
=======


tripleo-quickstart-promote-master-delorean-minimal
--------------------------------------------------


    export VIRTHOST='my-cool-virthost.example.com'
    bash quickstart.sh \
    -u "http://artifacts.ci.centos.org/artifacts/rdo/images/master/delorean/testing/undercloud.qcow2" \
    -t all \
    -c centosci/minimal.yml \
    $VIRTHOST
    


Project tripleo-quickstart-promote-master-delorean-ha
-----------------------------------------------------

    export VIRTHOST='my-cool-virthost.example.com'
    bash quickstart.sh \
    -u "http://artifacts.ci.centos.org/artifacts/rdo/images/master/delorean/testing/undercloud.qcow2" \
    -t all \
    -c centosci/ha.yml \
    $VIRTHOST

This runs the HA setup as defined in playbooks/centosci/ha.yml

    overcloud_nodes:
      - name: control_0
        flavor: control
      - name: control_1
        flavor: control
      - name: control_2
        flavor: control
      - name: compute_0
        flavor: compute

and puppet-pacemaker as extra vargs



Project tripleo-quickstart-promote-master-delorean-build-images
---------------------------------------------------------------

# It is assumed that tripleo-quickstart is installed in the default
# virtualenv created by running quickstart.sh
# (however quickstart.sh does not yet support building images)

    source $HOME/.quickstart/bin/activate
    export VIRTHOST='my-cool-virthost.example.com'
    export ANSIBLE_CONFIG=$HOME/.quickstart/tripleo-quickstart/ansible.cfg

# For delorean, find the delorean hash from this job:
# https://ci.centos.org/job/rdo-promote-get-hash-master/

    export dlrn_hash=\
    ansible-playbook -i local_hosts $HOME/.quickstart/tripleo-quickstart/playbooks/build-images.yml \
    --extra-vars virthost=$VIRTHOST \
    --extra-vars artib_release=master \
    --extra-vars artib_build_system=delorean \
    --extra-vars artib_delorean_hash=$dlrn_hash

# The images will be in /var/lib/oooq-images dir on the virthost

