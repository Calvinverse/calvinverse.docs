Title: Documentation
---

The [Calvinverse](https://github.com/Calvinverse) organization provides a number of repositories that
contain all the necessary code to create a [build environment](https://en.wikipedia.org/wiki/Build_automation)
based on the idea of [immutable infrastructure](https://martinfowler.com/bliki/ImmutableServer.html).

The repositories describe resources different resources which are needed to create a fully functioning
build environment, like:

- A [build controller](../resources/build-controller.html): This resource collects [build jobs]()
  and sends them to the build executors which process the jobs.
- A [build executor](): This resource processes a build job and executes the actual build.

The different resources can be combined into a [relatively simple](example-minimal-build-system.html)
or small build system which may consist of only a few machines, or they may be combined into a far
more [complicated environment](example-complete-build-system.html) which consist of many different
services for those cases where the build workflow is more complex.

The code in the repositories will either create a [Hyper-V](https://en.wikipedia.org/wiki/Hyper-V)
virtual machine image or a [Docker](https://www.docker.com/) container. If however a different
virtualization platform is desired changes can be made to the [Packer](https://packer.io) configurations
relatively easy to change the type of images that are generated.


## Available resources

The Calvinverse organisation contains a number of repositories with [resources](../resources) that
can be used to create a build system. These resources fall into one of the following groups:

* [Base resources](../resources/category-base.html) - Resources on which other resources are based.
  For instance there are resources that define a VM with just an operating system installed and
  prepared. These base VM images will then be used by other resources as their base, thereby reducing
  the build times for these more advanced resources.
* [Build resources](../resources/category-build.html) - Resources which define parts of the build
  infrastructure, e.g. the build controller or build executors.
* [Supporting resources](../resources/category-support.html) - Resources which define parts of the
  infrastructure which support the work of the build resources, e.g. web servers.

In general at least some of the base and build resources are needed to build a functional
build system. The other resources may be required if a larger build system needs to be
created.

## Setup and configuration

* [Getting started](getting-started.html)
* [How to build](how-to-build.html)

## Examples

 * [Minimal configuration](example-minimal-build-system.html)
 * [Complete configuration](example-complete-build-system.html)


## Contribution

* [About](about.html)