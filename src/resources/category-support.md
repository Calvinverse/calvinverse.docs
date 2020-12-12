Title: Support
---

# Support resources

The support resources are the resources that perform a supporting role in the environment. Examples
of this are [Vault](https://vaultproject.io) for secure secret processing or
[RabbitMQ](https://www.rabbitmq.com/) for robust message queueing.

The following repositories provide capabilities that improve the ability of the environment

* [resource.hashi.server](https://github.com/Calvinverse/resource.hashi.server) - Contains the
  source code and Packer configuration files to create an image, for either Hyper-V or Azure, that
  contains an instance of the Consul application configured as server instance.
* [resource.hashi.ui](https://github.com/Calvinverse/resource.hashi.ui) - Contains the source code
  and Packer configuration files to create an image, for either Hyper-V or Azure, that contains an
  instance of Consul with the UI capability enabled.
* [resource.proxy.edge](https://github.com/Calvinverse/resource.proxy.edge) - Contains the source
  code and Packer configuration files to create an image, for either Hyper-V or Azure, that contains
  an instance of the Fabio load balancer.
* [resource.secrets](https://github.com/Calvinverse/resource.secrets) - Contains the source code and
  Packer configuration files to create an image, for either Hyper-V or Azure, that contains an
  instance of a Vault server.
* [resource.queue](https://github.com/Calvinverse/resource.queue) - Contains the source code and
  Packer configuration files to create an image, for either Hyper-V or Azure, that contains an
  instance of the RabbitMQ server.

## Environment creation support

* [service.provisioning.controller](https://github.com/Calvinverse/service.provisioning.controller) - Service for creating
  and configuring environments.
* [service.provisioning.ui.web](https://github.com/Calvinverse/service.provisioning.ui.web) - Contains the source code
  for a web service that displays the current environments and the images and settings that were used
  to create these environments.

### Azure

* [infrastructure.azure.network.spoke](https://github.com/Calvinverse/infrastructure.azure.network.spoke) - Stores the
  configuration files to create a
  [spoke network](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)
  which forms the basis of a Calvinverse environment in Azure.
* [infrastructure.azure.core.servicediscovery](https://github.com/Calvinverse/infrastructure.azure.core.servicediscovery) - Defines
  the configuration for the creation of a Consul service discovery cluster.
* [infrastructure.azure.core.ingress](https://github.com/Calvinverse/infrastructure.azure.core.ingress) - Defines the
  configuration for the creation of the ingress points for the environment.
* [infrastructure.azure.core.secrets](https://github.com/Calvinverse/infrastructure.azure.core.secrets) - Defines the
  configuration for the creation of a Vault cluster.
* [infrastructure.azure.core.events](https://github.com/Calvinverse/infrastructure.azure.core.events) - Defines
  the configuration for the creation of a RabbitMQ cluster for event handling in Calvinverse.
