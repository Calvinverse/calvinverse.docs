Title: Base resources
---

# Base resources

The base resources are the resources on which all other resources are based. Calvinverse
provides base resources for virtual machines and for Docker containers. They contain the default
applications and settings that are shared by all resources. These shared applications are:

* [Consul](https://consul.io) - The service discovery and service mesh application
* [Envoy](https://www.envoyproxy.io/) - The proxy used by Consul to create a
  [service mesh](https://www.consul.io/docs/connect)
* [Consul-Template](https://github.com/hashicorp/consul-template) - The application that generates
  configuration files based on information from the [Consul K-V](https://www.consul.io/docs/dynamic-app-config/kv)
  and [Vault](https://vaultproject.io)
* [Telegraf](https://www.influxdata.com/time-series-platform/telegraf/) - The application that collects
  metrics and ships them to the metrics and alerting systems
* A log forwarding application to ship logs from the resource to a central logging platform. On
  Linux platforms the [syslog-ng](https://en.wikipedia.org/wiki/Syslog-ng) log forwarder is used.
  On Windows based platforms a modified versions of [Filebeat](https://github.com/pvandervelde/filebeat.mqtt)
  and [Winlogbeat](https://github.com/pvandervelde/winlogbeat.mqtt) are used.
* [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-collector) - The application that
  provides ways to forward distributed traces to a central store
* [Unbound](https://www.nlnetlabs.nl/projects/unbound/about/) - The recursive DNS resolver that
  coordinates DNS responses from multiple sources including Consul

## VM base resources

The following repositories are used to create base images for virtual machines

* [base.vm.linux](https://github.com/Calvinverse/base.vm.linux) - Stores the scripts and configurations
  for the creation of Linux base images
* [base.vm.windows](https://github.com/Calvinverse/base.vm.windows) - Stores the scripts and configurations
  for the creation of Windows base images

## Container base resources

* [base.container.linux](https://github.com/Calvinverse/base.container.linux) - Stores the scripts and configurations
  for the creation of a Linux base container