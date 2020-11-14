Title: Environment setup
ShowInNavbar: false
---

# Setting up an environment

An [environment](environments.html) is defined by the [Consul](https://consul.io) server instances,
all the nodes that are connected to the server instances and the configurations of the nodes and
services.

## Environment core

The first part of creating a new environment is to configure the Consul servers which form the
core of the environment. The number of Consul servers is determined by the need to ensure
[quorum](https://www.consul.io/docs/internals/consensus.html) between the servers. Practically this
means that there should be at [least three servers](https://www.consul.io/docs/internals/consensus.html#deployment-table)
but possibly five depending on the degree of fault tolerance required.

Initially when starting a new environment you need to [bootstrap](https://www.consul.io/docs/install/bootstrapping.html)
Consul. In general it is sensible to set the number of server nodes that is
[expected](https://www.consul.io/docs/agent/options.html#_bootstrap_expect) which will allow Consul
to automatically bootstrap itself when the desired number of server nodes have connected.

One of the more difficult parts of creating an environment is to determine a way in which the
nodes, server or client, can [find the server nodes](https://www.consul.io/docs/agent/options.html#retry-join).
This requires that there is some way for Consul server nodes to identify themselves to a node that
wants to join the cluster. Several options exist:

* The simplest way is by making sure that the Consul server nodes always have a fixed set of
  IP addresses and then to get each node to try to connect to those addresses. From a performance
  perspective it makes sense to put the addresses most likely to be alive first in the
  configuration. This method is simple but does require that the Consul server nodes always get
  an IP allocated from the fixed pool of addresses. This can for instance be done by setting a
  static IP on those nodes or by setting a known MAC address in combination with DHCP rules.
  The obvious drawback is that it is harder to automate this method.
* A more complex but better method is to use known DNS names which are attached to the Consul hosts
  when they start. Nodes could obtain these via initial provisioning or a service on the node
  could request a free name from a pool when it starts up.
* Finally if you are in a cloud then you can use
  [auto-join](https://www.consul.io/docs/agent/cloud-auto-join.html) method which uses information
  known to the cloud system to point to the server nodes.

## Initial provisioning

In order to get the initial configuration into a resource instance Calvinverse assumes that for
VMs an ISO image is attached to the VM when it is initially created. This ISO should contain files
with the following information for Consul

* [datacenter](https://www.consul.io/docs/agent/options.html#_datacenter)
* [domain](https://www.consul.io/docs/agent/options.html#_domain)
* [encrypt](https://www.consul.io/docs/agent/options.html#_encrypt)
* [retry_join](https://www.consul.io/docs/agent/options.html#retry-join)

Additionally for server nodes the following information is expected

* [bootstrap_expect](https://www.consul.io/docs/agent/options.html#_bootstrap_expect)
* [server](https://www.consul.io/docs/agent/options.html#_server)

Finally the ISO also contains the
[zone configuration file](https://github.com/Calvinverse/calvinverse.configuration/blob/master/config/iso/shared/unbound/unbound_zones.conf)
for [unbound](https://nlnetlabs.nl/projects/unbound/about/), which is the caching DNS resolver which
is installed on all resources to handle DNS requests.

The [Calvinverse.Configuration](https://github.com/Calvinverse/calvinverse.configuration/tree/master/config/iso)
repository provide an example of the different ISO files that will be made. From this repository
three different ISO files will be created

* Consul server on Linux
* Consul client on Linux
* Consul client on Windows

## Configuration of services

[Configurations](configuration.html) for all other services will be obtained from the
[Consul Key-Value store](https://www.consul.io/docs/agent/kv.html) via
[Consul-Template](https://github.com/hashicorp/consul-template). Getting the configurations after
the VM has connected to the Consul environment allows easily changing the configurations and it
disconnects the configuration of the services from the provisioning of the resource.

Again the [Calvinverse.Configuration](https://github.com/Calvinverse/calvinverse.configuration/tree/master/config/kv)
repository provides examples of the different configuration values that need to be set.
For resource specific configuration information the readme for the different resource
repositories provides the required information.

## Resources

With the provisioning and configuration information collected the final part of the setup is to
create instances of the desired resources in the correct order.

1) Determine which method of discovery is going to be used for Consul nodes to discover the server
   nodes, be that via IP address, DNS name or some other discovery method. If you plan to use
   either IP addresses or DNS names, decide them ahead of time. Then add those to the
   [retry_join](https://www.consul.io/docs/agent/options.html#retry-join) entry in the Consul
   configuration file that is going to be included in the different
   [ISO](https://github.com/Calvinverse/calvinverse.infrastructure/tree/master/config/iso) files.
1) Determine the DNS name for the environment. This will later be pointed to the IP address, or
   addresses, of the reverse proxies. Thereby giving users access to the services provided by
   the environment via a single entrypoint instead of having to use the IP addresses of the
   individual instances.
1) The first instances that should be created are for the [Consul servers](https://github.com/Calvinverse/resource.hashi.server).
   As indicated at least three virtual machines should be created from the virtual hard drives. The
   correct virtual machine settings are described in the readme for the repository. In order for an
   environment to form the server instances need to connect to each other.
    1) Start a single instance and once it is started make sure that it either has a known IP
       address or a known DNS name. Make sure this is one of the IP addresses / DNS names that
       was decided on earlier on. Once the instance is available see that Consul is running. Note
       that it will not have elected itself as a leader because it should be expecting multiple
       servers before it will start acting as a server.
    1) Start the next instances one by one. Ensure that they connect to the other node via the
       [`consul members`](https://www.consul.io/docs/commands/members.html) command.
    1) Once the last server instance connects the Consul cluster will bootstrap itself and a leader
       will be elected. From this point on the Consul cluster will be active and ready to work.
1) Once the cluster is ready to work the next step is to upload K-V values. These can be pushed
   to the cluster via the [KV store endpoints](https://www.consul.io/api/kv.html#create-update-key)
   that Consul provides.
1) With the Consul servers up and running and the configuration values set in the KV store the next
   step is to create the resource instances that provide insight into the environment. These are
   the reverse proxies which allow services to be easily reached outside the environment and the
   Consul UI for checking the Consul cluster status.
    1) Create one or more instances of the [reverse proxy](https://github.com/Calvinverse/resource.proxy.edge)
       resource. The [Fabio loadbalancer](https://github.com/fabiolb/fabio) does not automatically
       provide high availability, however it is fairly trivial to provide a DNS round-robin approach
       for high availability by pointing one DNS addres to the IP addresses of the reverse proxy
       instances. Once the first instance is provisioned you can find the UI for Fabio on
       `http://<ENVIRONMENT_DNS_NAME>:9998`
    1) Create one or more instances of the [Consul UI](https://github.com/Calvinverse/resource.hashi.ui)
       resource. Once the instance is provisioned the UI can be found on
       `http://<ENVIRONMENT_DNS_NAME>/dashboards/consul`.
1) Once the Consul server and the Consul management services are available the supporting services
   can be added to the environment. Supporting services that are optional are marked as such. If you
   have decided to not include those then you can skip those steps.
    1) Create at least [three instances](https://www.rabbitmq.com/clustering.html) of the
       [RabbitMQ](https://github.com/Calvinverse/resource.queue) resource. The RabbitMQ
       [documentation](https://www.rabbitmq.com/partitions.html) should help you decide how many
       instances you need for your purposes. In general an odd number of nodes is recommended with the
       minimum of three nodes for high availability. After the instances have been provisioned you
       can reach the management page via `http://<ENVIRONMENT_DNS_NAME>/services/queue`.
    1) Once the RabbitMQ cluster is up you can create the necessary [vhosts](https://www.rabbitmq.com/vhosts.html)
       and [users](https://www.rabbitmq.com/access-control.html). The
       [Calvinverse.Infrastructure repository](https://github.com/Calvinverse/calvinverse.infrastructure/blob/master/src/rabbitmq/create.md)
       provides a description of the minimum vhosts, users and queues that should be created.
       Make sure to create at least an administrator level user which will be used by
       [Vault](https://vaultproject.io) to create temporary users in RabbitMQ for services to
       write to exchanges or read from queues in RabbitMQ.
    1) Create at least two instances of the [Vault](https://github.com/Calvinverse/resource.secrets)
       resource.
    1) Once the Vault instances have been provisioned you can
       [initialize](https://www.vaultproject.io/docs/commands/operator/init.html) one instance. This
       will provide a number of [keys](https://www.vaultproject.io/docs/concepts/seal.html) of which
       a subset will be required to unseal the Vault instance. Note that initialization only needs
       to be done on a single node but all nodes need to be unsealed individually for them to be used.
    1) Once the vault instances are initialized and unsealed you can mount
       [secret engines](https://www.vaultproject.io/docs/secrets/index.html) and set
       [policies](https://www.vaultproject.io/docs/concepts/policies.html) which describe how the
       secret engines should be used. The minimum secret engines that should be mounted is the
       [RabbitMQ](https://www.vaultproject.io/docs/secrets/rabbitmq/index.html) secret engine. Additionally
       at least one authentication method should be configured for authenticating users. The
       [Calvinverse.Infrastructure repository](https://github.com/Calvinverse/calvinverse.infrastructure/tree/master/src/vault)
       provides scripts and configuration files to mount both the RabbitMQ secret engine and the
       [LDAP](https://www.vaultproject.io/docs/auth/ldap.html) authentication method for user authentication.
1) Next deploy the metrics instances so that you can get information about the status of all your
   instances. Deploy the metrics instances in the following order:
    1) First deploy an instance of the [InfluxDb](https://github.com/Calvinverse/resource.metrics.storage)
       resource. Since the open source version doesn't allow H/A it is sensible to only deploy one
       instance.Once the instance has been provisioned and connected to the Consul cluster metrics
       should start streaming into the database.
    1) Deploy an instance of the [Grafana](https://github.com/Calvinverse/resource.metrics.dashboard)
       resource. After provisioning the instance you can reach it on
       `http://<ENVIRONMENT_DNS_NAME>/dashboards/metrics`. Initially this instance will not have any
       dashboards. You can either make those manually or import them by pushing the
       [dashboard definitions](https://github.com/Calvinverse/calvinverse.metrics.dashboards) to the
       Consul K-V from where they will automatically be provisioned with Grafana.
    1) Finally you can optionally deploy an instance of [Kapacitor and Chronograf](https://github.com/Calvinverse/resource.metrics.monitoring).
       These services provide alerting and a different way of displaying metrics information.
1) The last of the supporting services are the document and log processing services. These are used
   to process, store and display logs and other documents which are generated in the environment.
   Deploy these resources in the following order:
    1) First deploy multiple instances of the [Elasticsearch](https://github.com/Calvinverse/resource.documents.storage)
       resource. As with the other H/A resources you will need an odd number of instances with three
       being the minimum.
    1) Once the Elasticsearch cluster is running you can deploy a single instance of the
       [Kibana](https://github.com/Calvinverse/resource.documents.dashboard) resource. Once provisioning
       is completed you can find it on `http://<ENVIRONMENT_DNS_NAME>/dashboards/documents`. The
       [monitoring](https://www.elastic.co/what-is/elasticsearch-monitoring) tab in Kibana provides
       information about your Elasticsearch cluster.
    1) Finally you can deploy one or more instances of the [Logstash](https://github.com/Calvinverse/resource.logs.processor)
       resource. You can have as many of these instances as you need to process all your logs. Log
       processing rules can be loaded into the Consul K-V from where they will be provided to the
       Logstash instances. For an example have a look at the
       [calvinverse.logs.filters](https://github.com/Calvinverse/calvinverse.logs.filters) repository.
1) Finally the build instances can be added to the environment. The first instance that should be
   added is that of the [Jenkins build controller](https://github.com/Calvinverse/resource.build.master).
1) Once the build controller has been provisioned, one or more
   [build agents](https://github.com/Calvinverse/resource.build.agent.windows) can be provisioned.
   The agents will automatically connect to the build controller when they are provided with an
   authorization to connect to Vault, from where they will get the username and password to
   connect to the build controller.
1) The final resource that can optionally be added is the [Nexus](https://github.com/Calvinverse/resource.artefacts)
   resource which stores artefacts, packages and Docker image layers.
