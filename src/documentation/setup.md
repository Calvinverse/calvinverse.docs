Title: Environment setup
ShowInNavbar: false
---

# Setting up an environment

An [environment](environments.html) is defined by the [Consul](https://consul.io) server instances,
all the nodes that are connected to the server instances and the configurations of the nodes and
services.

## Environment core

The first part of creating a new environment is to configure the Consul servers which form the
core of the environment. The number of consul servers is determined by the need to ensure
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
VMs an [ISO image](https://github.com/Calvinverse/calvinverse.infrastructure/tree/master/config/iso)
is attached to the VM when it is initially created. This ISO should contain files with the
following information for Consul

* [datacenter](https://www.consul.io/docs/agent/options.html#_datacenter)
* [domain](https://www.consul.io/docs/agent/options.html#_domain)
* [encrypt](https://www.consul.io/docs/agent/options.html#_encrypt)
* [retry_join](https://www.consul.io/docs/agent/options.html#retry-join)

Additionally for server nodes the following information is expected

* [bootstrap_expect](https://www.consul.io/docs/agent/options.html#_bootstrap_expect)
* [server](https://www.consul.io/docs/agent/options.html#_server)

Finally the ISO also contains the
[zone configuration file](https://github.com/Calvinverse/calvinverse.infrastructure/blob/master/config/iso/shared/unbound/unbound_zones.conf)
for [unbound](https://nlnetlabs.nl/projects/unbound/about/), which is the caching DNS resolver which
is installed on all resources to handle DNS requests.

## Configuration of services

[Configurations](configuration.html) for all other services will be obtained from the
[Consul Key-Value store](https://www.consul.io/docs/agent/kv.html) via
[Consul-Template](https://github.com/hashicorp/consul-template).

## Resources

### Core

Required

* Consul servers

* Vault

### Additional

* Monitoring
  * HashiUI
  * Vault UI
  * Influx
  * Grafana
* Diagnostics
  * Elasticsearch
* Queuing
  * RabbitMQ
