Title: Example - A complete environment
ShowInNavbar: false
---

# Example - A complete environment

**Status: Thoughts only, needs proper formatting**


* 3 consul hosts
* 1 Consul UI host
* 2 Vault hosts
* 3 RabbitMQ hosts
* 3 Elastic search hosts
* 1 Kibana host
* 1+ logstash host
* 1 Influx host
* 1 Grafana host
* 1 Jenkins host
* 1+ Jenkins agent (potentially different OS's)
* 1 Artefact server
* 1 Proxy

Notes:
* There are a number of resources in this environment of which there are multiple. That is done
  so that there is some redundancy in the resources, i.e. if one fails there will still be enough
  to keep going. Note that some resources, e.g. consul hosts, elasticsearch hosts and rabbitmq hosts,
  should be deployed in uneven numbers to prevent [split brain]() situations. For more specific
  information read the clustering guides for the respective applications
* It is wise to deploy the resources to different VM hosts so that a failure in one of the hosts
  does not lead to failures in the cluster
  * But they have to be in the same LAN otherwise latency starts creating issues.

## Environments

* Need at least two environments so that you can have one test environment.
* For some cases you want a third environment which is the one you use to build the resources
  so that resource creation / updates don't interfere with normal builds

## Repositories

- Need repositories for the code you want to build
- All configurations should be stored in one or more repositories. Need to configure these so that
  you can push configurations to the different environments so you can test configuration changes
  as well.
  - Some configurations are environment specific. Ideally have as few of these as possible
