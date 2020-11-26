Title: Example - A minimal environment
ShowInNavbar: false
---

# Example - A minimal environment

A minimal build environment provides the ability to build artefacts with
the minimum number of compute resources used. Note that there will be no redundancy
in an environment like this due to the limited number of resources. It is recommended
that this environment set up is not used as a production environment.

* [Consul server instance](https://github.com/Calvinverse/resource.hashi.server) - One instance
  is possible although it is not recommended for a production environment due to the fact
  that a failure of this instance will cause data loss.
* [Reverse proxy instance](https://github.com/Calvinverse/resource.proxy.edge)
* [Vault instances](https://github.com/Calvinverse/resource.secrets)
* [Jenkins build controller instances](https://github.com/Calvinverse/resource.build.master)
* [Build agent instances](https://github.com/Calvinverse/resource.build.agent.windows) - As many as
  required to be able to process the required number of builds

Optionally you may want

* [Nexus instances](https://github.com/Calvinverse/resource.artefacts)

Note that performing updates in this environment may be difficult because of the lack of
redundancy.
