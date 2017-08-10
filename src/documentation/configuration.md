Title: Configuration
---

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