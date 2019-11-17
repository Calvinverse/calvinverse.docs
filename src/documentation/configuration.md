Title: Configuration
ShowInNavbar: false
---

# Configure an environment

* [Calvinverse.Infrastructure]() has some standard configuration files
* The defaults are baked into the resources, but not everything can be
  configured in source because some things depend on the way the infrastructure
  is set up. Those will have to be configured when the resources are created.
* Some of the configuration is done when the resource is created. For VMs the
  configuration can be read from an ISO which is attached to the VM on start-up.
  For Docker containers configuration can be provided either as a Docker volume
  or via environment variables
* Configurations which need to be available on service start (but not on resource
  start) can also be provided through the [Consul K-V store]()

Note: Provisioning is hard. All resources are designed to be environment-independent,
i.e. they only have standard configurations encoded, ones that are valid for all
environments. In order to get configurations that are environment specific in them
you need a way for the resource to know where to fetch those from, e.g.
a web address or the Consul K-V store. However in order to tell the resource
what the configuration store is you need to provide an environment specific
configuration when the resource is first initialized, i.e. the initial
configuration.

In calvinverse all resources assume that an ISO file (i.e. a CD / DVD) is mounted
when the resource first boots (for VMs), or environment variables are set
(for containers). These initial configurations only contain information on
how to connect to the Consul cluster in the environment. Once connected
all other configuration values can be obtained from the Consul K-V or the
Vault secret store