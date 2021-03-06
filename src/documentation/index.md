Title: Documentation
ShowInNavbar: true
Order: 2500
---

# Documentation

The [Calvinverse](https://github.com/Calvinverse) organization provides a number of repositories that
contain all the necessary code to create a [build environment](https://en.wikipedia.org/wiki/Build_automation)
based on the idea of [immutable infrastructure](https://martinfowler.com/bliki/ImmutableServer.html). In
other words resources, e.g. virtual machines or containers, are created with all the required applications
and tooling in place. Once a specific version of a resources is created it is never be changed, neither
in the resource form or when deployed as an instance. If changes are required then a new version of the
resource is created, tested and released. Once the resource is released new instances based on this
resource can be deployed.

The repositories describe resources different resources which are needed to create a fully functioning
build [environment](environments.html), for instance:

- A [build controller](https://github.com/Calvinverse/resource.build.master): This resource collects
  build jobs and sends them to the build executors which process the jobs.
- A [build executor](https://github.com/Calvinverse/resource.build.agent.windows): This resource
  processes a build job and executes the actual build.
- An [artefact repository](https://github.com/Calvinverse/resource.artefacts): This resource stores
  artefacts that were created during a build and allows other builds or processes to consume these
  artefacts.

The different resources can be combined into a [relatively simple](example-minimal-build-system.html)
or small build system which may consist of only a few machines, or they may be combined into a far
more [complicated environment](example-complete-build-system.html) which consists of many different
services for those cases where the build workflow is more complex.

The code in the repositories will either create an [Azure]() virtual machine, a
[Hyper-V](https://en.wikipedia.org/wiki/Hyper-V) virtual machine image or a
[Docker](https://www.docker.com/) container. However a different
virtualization platform is desired changes can be made to the [Packer](https://packer.io) configurations
relatively easy to change the type of images that are generated.

## Design principles

The resources and configurations in Calvinverse have been developed using the following design principles:

- The original code for all resources and configurations is stored in source control. This includes
  [filters](https://github.com/Calvinverse/calvinverse.logs.filters) for
  [Logstash](https://github.com/Calvinverse/resource.logs.processor),
  [dashboards and data sources](https://github.com/Calvinverse/calvinverse.metrics.dashboards)
  for [Grafana](https://github.com/Calvinverse/resource.metrics.dashboard)
  and general [configuration settings](https://github.com/Calvinverse/calvinverse.configuration)
  for everything else. By storing all the information in source control it is always possible to see
  what changes were made and to roll-back or roll-forward in case of issues.
- All resources and configurations have a version.
  - Note that currently the items in the Consul key-value store don't have a version. Work to add versions
    to the items in the key-value store is planned.
- Secrets are the exception to the first design principle. They are never stored in source control
  and are handled by [Vault](https://github.com/Calvinverse/resource.secrets).
  Resources that need secrets will obtain them from Vault, either through direct interaction or
  via [Consul-Template](https://github.com/hashicorp/consul-template).
- Direct access to any of the resources should never be required. This is enabled by streaming logs
  and metrics from all resources to a central log store and a central metrics store.
  - Note that you can currently log into the resources through SSH or WinRM if this is absolutely
    required.
- No changes to running resources should be made (immutable infrastructure). Required changes will
  be made to the repository, a new image will be made and once tested it will replace the existing
  production instances.
- Consul is used to define a local DNS domain to contain environments. i.e. the
  [environment](environments.html) a resource belongs to is determined by the consul master instances
  it connects to.

## Available resources

The Calvinverse organisation contains a number of repositories with [resources](../resources) that
can be used to create a build system. These resources fall into one of the following groups:

- [Base resources](../resources/category-base.html) - Resources on which other resources are based.
  For instance there are resources that define a VM with just an operating system installed and
  prepared. These base VM images will then be used by other resources as their base, thereby reducing
  the build times for these more advanced resources because the base resource provides an up to date
  operating system install and all the common applications.
- [Build resources](../resources/category-build.html) - Resources which define parts of the build
  infrastructure, e.g. the build controller or build executors.
- [Supporting resources](../resources/category-support.html) - Resources which define parts of the
  infrastructure which support the work of the build resources, e.g. artefact servers or logging
  and metrics services.

In general at least some of the base and build resources are needed to build a functional
build system. The other resources may be required if a larger build system needs to be
created.

In order to [get started](getting-started.html) creating a build system the first thing to do
is to [create](how-to-build.html) the resources that will make up the build system. Once the
resource images have been created one needs to decide on the way the resources are
going to be [assembled](setup.html) into a functioning build environment. Part of this process
involves storing the global configurations for all the resources in a
sensible way.
