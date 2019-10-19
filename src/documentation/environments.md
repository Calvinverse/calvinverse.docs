Title: Environments
ShowInNavbar: false
---

* Explain what an environment is
  * Collection of resources and services that work together to achieve one or more goals,
    i.e. in this case to provide the ability to build, test and release software
* Why do we define an environment
  * Unit of separation, allows you to have multiple groups of services that only see other services
    in the group while sharing an IP network / DNS network with other groups
    * Allows you to have a test environment and a production environment, or have environments
      in different locations etc.
  * Cooperative system, i.e. a service is able to reach outside the environment if it so desires
  * Inside the environment all resources / services are the same and referred to by the same
    names, that means that the same tooling works in all environments without change(!), so you can
    test in your test environment and then push to the production environment without any changes
* What do we need to achieve this
  * A way to group resources. We might have multiple environments. A resource should 'know' which
    environment it belongs to.
    * Note that 'knowing' the environment basically means that a resource should easily be able
      to contact other resources in the environment while making it hard (or even impossible)
      to contact resources outside the environment. To a resource the environment should be the
      only thing around
  * Ways to push information / data into an environment. An environment isn't very useful if
    we can't provide it with information in some way
  * Ways to get information / data out of an environment. Neither is an environment very useful
    if we can't get information out
* How do we achieve this
  * Use [Consul](https://consul.io) to create environments. A [datacenter](https://www.consul.io/docs/glossary.html#datacenter)
    defines an environment. All services in a datacenter can communicate with each other either by
    using Consul as the [DNS](https://www.consul.io/docs/agent/dns.html) resolver or by using more
    advanced [Connect](https://www.consul.io/docs/connect/index.html) feature
    * Can additionally use router settings / firewalls to block resources from going outside
      the environment
* Examples
