opsworks_delayed_job
====================

These recipes set up an [AWS OpsWorks](http://aws.amazon.com/opsworks/) instance to run [delayed_job](https://github.com/collectiveidea/delayed_job) workers for a Rails application.


Configuration Examples
----------------------

By default, **4** delayed_job workers will be configured. To customize this, you may override the `delayed_job.pool_size` attribute in your stack's custom JSON:

    {
      "delayed_job": { "pool_size": 6 }
    }

You can provide alternate `delayed_job` script location by overriding `delayed_job.path_to_script` attribute in the stack's custom JSON. Useful for Rails 4 projects as `delayed_job` moved to `bin` from `script`:

    {
      "delayed_job": { "pool_size": 3, "path_to_script": "bin" }
    }

By default, workers will read from all queues. You can also specify custom `queues` parameters by adding attributes corresponding to each instance name and worker process. A special `default` pool can be specified to supply configuration for instances that don't otherwise appear in the JSON:

    {
      "delayed_job": {
        "YOUR_APP_NAME": {
          "pools": {
            "worker1": {
              "0": { "queues": "highpriority,images" },
              "1": { "queues": "emails" },
              "2": { "queues": "images" }
            },
            "default": {
              "0": { "queues": "emails" },
              "1": { "queues": "emails" },
              "2": { "queues": "emails" }
            }
          }
        }
      }
    }


OpsWorks Set-Up
---------------

Create a custom layer for the delayed_job instances. Add the `AWS-OpsWorks-Rails-App-Server` security group to the layer, so that the instances have the usual cache and database access. Then, assign this cookbook's custom chef recipes to OpsWorks events as follows:

* **Setup**: `opsworks_delayed_job::setup`
* **Configure**: `opsworks_delayed_job::configure`
* **Deploy**: `opsworks_delayed_job::deploy`
* **Undeploy**: `opsworks_delayed_job::undeploy`
* **Shutdown**: `opsworks_delayed_job::stop`


To Do
-----

* Attributes should be better structured (allowing customization without requiring all worker processes to be listed).


&copy; 2013 Joey Aghion, Artsy. [MIT License](LICENSE).
