Title: Example - A complete environment
ShowInNavbar: false
---

# Example - A complete environment

A complete build environment provides the scalable build capacity as well as good
observability. In order to create and maintain one of these environments for
production purposes you will need to create at least two environments. The
first one being the production environment, the second one a test environment in
which you can verify that changes to the resources are suitable.

Each of these environments should contain at least the following resources

* [Consul server instance](https://github.com/Calvinverse/resource.hashi.server) - Either
  three or five depending on the required degree of
  [fault tolerance](https://www.consul.io/docs/internals/consensus.html#deployment-table)
* [Consul UI instance](https://github.com/Calvinverse/resource.hashi.ui) - At least
  two instances for redundancy, although more instances may be added if desired
* [Reverse proxy instance](https://github.com/Calvinverse/resource.proxy.edge) - At least
  two instances for redundancy, although more instances may be added if desired
* [Vault instances](https://github.com/Calvinverse/resource.secrets) - Again at least
  two instances for redundancy, with more instances added if desired
* [RabbitMQ instances](https://github.com/Calvinverse/resource.queue) - At least
  [three instances](https://www.rabbitmq.com/clustering.html) are required for a redundant
  cluster. If higher degrees of redundancy are required then follow the
  [documentation](https://www.rabbitmq.com/partitions.html)
* [Elasticsearch instances](https://github.com/Calvinverse/resource.documents.storage) -
  A cluster size of at least three instances is recommended for redundancy purposes. However
  the cluster might have to be larger for
  [performance reasons](https://www.elastic.co/guide/en/elasticsearch/reference/current/scalability.html)
* [Kibana instances](https://github.com/Calvinverse/resource.documents.dashboard) - Currently
  only one instance per Elasticsearch cluster is supported. Providing a
  [cold standby](https://en.wikipedia.org/wiki/High_availability_software) is recommended if
  small [mean-time-to-recovery (MTTR)](https://en.wikipedia.org/wiki/Mean_time_to_recovery)
  is important for user log search
* [Logstash instances](https://github.com/Calvinverse/resource.logs.processor) - The number
  of these depends on the rate at which you want to process text documents. Start with two
  instances for redundancy and then add more as needed.
* [InfluxDb instances](https://github.com/Calvinverse/resource.metrics.storage) - Currently
  only one instance is supported due to the use of the open source version of Influx.
* [Grafana instances](https://github.com/Calvinverse/resource.metrics.dashboard) - Currently
  only one instance is supported because the
  [high availability](https://grafana.com/docs/grafana/latest/administration/set-up-for-high-availability/)
  configurations have not been implemented at the moment
* [Jenkins build controller instances](https://github.com/Calvinverse/resource.build.master) - Currently
  only one instance is supported due to the fact that Jenkins does not support high availability
  configurations. A cold standby may be called for if a small MTTR is required.
* [Build agent instances](https://github.com/Calvinverse/resource.build.agent.windows) - As many as
  required to be able to process the required number of builds
* [Nexus instances](https://github.com/Calvinverse/resource.artefacts) - Currently only one instance
  is supported due to the fact that the open source version only supports a single instance

In this list you will note that there are a number of resources of which multiple instances need to
be created. This is done so that there is some redundancy in the resources, i.e. if one fails there
will still be enough to keep going. Some resources, e.g. Consul hosts, Elasticsearch hosts and
RabbitMQ hosts, should be deployed in uneven numbers to prevent
[split brain](https://en.wikipedia.org/wiki/Split-brain_(computing)) situations. For more specific
information read the clustering guides for the respective applications.

Obviously for redundancy purposes it is wise to deploy the resources to different VM hosts so that
a failure in one of the hosts does not lead to failures in the cluster.
