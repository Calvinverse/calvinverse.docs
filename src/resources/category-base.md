The base resources are ...


* Base images / containers that other images / containers are based on
* Have all the shared applications
  * Consul
  * Telegraf
  * Log forwarder
    * syslog-ng on linux
    * winlogbeat and filebeat on windows
  * DNS resolution / caching via unbound
    * redirect the consul domain to consul and cache
    * others via the standard DNS servers

* windows base
* linux base

