Title: Base resources
---

# Base resources

The base resources are the resources on which all other resources are based. They contain the default
applications and settings that are shared by all resources. In general these are:

* [Consul](https://consul.io) - The service discovery and service mesh application
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



* windows base
* linux base

* linux container base