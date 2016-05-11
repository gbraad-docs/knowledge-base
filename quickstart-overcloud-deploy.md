Quickstart Overcloud deploy
===========================

```
$ CONFIG=~/.quickstart/oooq_test_config.yml
$ cat > $CONFIG << EOCONF
```

    overcloud_nodes:
       - name: control_0
         flavor: control
       - name: control_1
         flavor: control
       - name: control_2
         flavor: control
     
       - name: compute_0
         flavor: compute
 
    extra_args: "--control-scale 3 -e /usr/share/openstack-tripleo-heat-templates/environments/puppet-pacemaker.yaml --ntp-server pool.ntp.org
    EOCONF



pingtest
--------

`/tmp/deploy_env.yaml`

    parameter_defaults:
      controllerExtraConfig:
      # In releases before Mitaka, HeatWorkers doesn't modify
      # num_engine_workers, so handle via heat::config 
        heat::config::heat_config:
          DEFAULT/num_engine_workers:
            value: 1
        heat::api_cloudwatch::enabled: false
        heat::api_cfn::enabled: false
      HeatWorkers: 1
      CeilometerWorkers: 1
      CinderWorkers: 1
      GlanceWorkers: 1
      KeystoneWorkers: 1
      NeutronWorkers: 1
      NovaWorkers: 1
      SwiftWorkers: 1



Performs
--------

  * openstack overcloud deploy --templates extra_args

  TripleO clientL overcloud_deploy.py
    * heat deploy -> additional topic
    * keystone init
    

After
-----

  * heat stack-list
  * if contains CREATE_FAILED
    * heat resource-list
      * heat deployment-show > log
    * return 1
  * else
    * OK