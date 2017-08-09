Title: Documentation
---

The [Calvinverse](https://github.com/Calvinverse) organization provides a number of repositories that contain all the necessary code to create a
[build environment](https://en.wikipedia.org/wiki/Build_automation) based on the idea of [immutable infrastructure](https://martinfowler.com/bliki/ImmutableServer.html).

The repositories describe resources different resources which are part of a build environment, like:

- A [build controller](../resources/build-controller.html): This resource collects [build jobs]() and sends them to the
  build executors which process the jobs.
- A [build executor](): This resource processes a build job and executes the actual
  build.

The different resources can be combined into a relatively simple or small build system which may consist of only one or two machines, one for the build controller and one for the build executor, or they may be combined into a far more complicated environment
which consist of many different services.

The code in the repositories will either create a [Hyper-V](https://en.wikipedia.org/wiki/Hyper-V) virtual machine image or
a [Docker](https://www.docker.com/) container. If however a different virtualization platform is desired changes can be made to the [Packer](https://packer.io) configurations relatively easy to change the type of images that are generated.

- Resources
- What does the base infrastructure look like
    - Consul
    - Nomad
    - Vault
    - Base images