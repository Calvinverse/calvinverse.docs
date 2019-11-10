Title: Getting started
ShowInNavbar: false
---

## Which resources are required

### Build resources

In order to create a build [environment](environments.html) the first step is to determine what the
requirements are for the environment. Important factors to consider are for instance:

* How many builds or pipeline actions are expected to be executed in a specific time period. If this
  is a small number then the build system will not need many executors, nor will all the other
  resources need to be powerful. However if large numbers are expected then all components of the
  build system need to be sized suitably
* How many different products are going to be handled by the pipeline. The number of products
  reflects in both the number of pipeline actions but also in the different available actions. If
  there are many different products it is also likely that there are many different actions that
  need to be executed. Each additional action adds complexity to the system and requires implementation,
  testing and maintenance.
* Related to the number of products is the different types of programming languages that are used
  in the pipeline e.g. .NET, javascript, java, go etc.. The more different languages need to be
  supported the more complex the system becomes, because the build agents need to be able to
  execute builds for the different languages, different package managers are required and potentially
  build agents on different operating systems need to be provided, e.g. when building iOS applications.
  All these additions increase the demands on implementation, testing and maintenance.

Once these factors have been considered it is possible to plan which [pipeline resources](../resources/category-pipeline.html)
are required and roughly what the hardware specs for those resources should be. Obviously it is
always possible to add more resources or to update a resource that has acquired additional
responsibilities.

### Supporting resources

With the pipeline resources decided on the next step is to determine what supporting resources are
needed. For these resources the following factors are important to consider:

* The Calvinverse resources assume that the environment is defined by a [Consul](https://consul.io)
  cluster so additional resources are required for this cluster. It is strongly recommended that
  [at least three](https://www.consul.io/docs/internals/consensus.html#deployment-table) Consul
  server instances are added to the environment to ensure resiliency and to provide the ability to
  upgrade the server hosts without outages or data loss.
* In order to provide a secure way to handle secrets, e.g. usernames and passwords, certificates etc.,
  the Calvinverse resources assume that secret storage is handled by one or more [Vault](https://vaultproject.io)
  instances so additional resources are required for Vault instance. It is possible to have a single
  Vault instance in an environment, however for resiliency it is recommended to have multiple
  instances.
* Is it important to get diagnostics from the different resources in the environment, i.e.
  [logs](../resources/category-diagnostics-logs.html) and [metrics](../resources/category-diagnostics-metrics.html).
  If the environment is small it might not be, however if the environment is large it most likely
  will be.
* Is the environment expected to have some sort of message processing, e.g. for change notifications
  etc.. If so then it may be useful to have a message queue to improve resiliency. By default
  the Calvinverse resources use [RabbitMQ](https://www.rabbitmq.com/) for queueing of change
  notifications for the build controller as well as using RabbitMQ as a queue for log messages
  before they are processed and stored.

### Environments

The last part of the environment design is to consider if one environment is enough. It may for
instance be beneficial to have a build environment in each office. However even if that is not
required it is important to consider whether or not the following two additional environments
are required:

* Test environment: This environment allows the development team to verify that any changes made to
  a resource or a configuration setting will function correctly once that resource is placed in the
  production build environment. If the production build environment is small then a test environment
  might not be necessary however if the production environment is large, complex or in use by many
  people then a test build environment is strongly recommended.
* A build environment to create and update the resources, i.e. a meta build environment. This
  environment provides a space where the development team can create new resources and update the
  images of existing resources. It is helpful to have an environment like this if there are lots
  of changes to the resources in the production build environment or if there is a team that is
  dedicated to maintaining and improving the production build environment.

## Prepare a build machine

With all the requirements for the different build environments decided it is possible to start
creating the resources for the environment.

If you have decided to build a meta build environment then this environment should be created
first. Once it has been created then other environments can be created and deployed with the
help of the meta build environment.

To create any environment the first thing to do is to create the resources. If there is no
build environment to create these then you will have to configure a developer machine with
the [appropriate tooling](how-to-build.html).
The Calvinverse resources are by default configured to be built on Hyper-V but the [Packer](https://packer.io)
configuration files can easily be updated to work with other virtualization technologies. So in
order to build the resources a machine should be configured with the selected virtualization
system.

In addition the build scripts for the Calvinverse resources make use of
[MsBuild](https://docs.microsoft.com/en-us/visualstudio/msbuild/msbuild?view=vs-2019) as the
build engine via the [nBuildKit](https://github.com/nbuildkit/nBuildKit.MsBuild) and
[ops-tools-build](https://github.com/ops-resource/ops-tools-build) libraries which means that
MsBuild should also be installed on the machine.

Finally because the Calvinverse resources are created via [Chef](https://www.chef.io/) scripts it is
required to install [Ruby](https://www.ruby-lang.org/en/) and [some Ruby gems](how-to-build.html) so
that the [Chefspec](https://docs.chef.io/chefspec.html) tests can be executed. It is not necessary to
install Chef itself given that all resources are created by installing Chef on the resource and
running [Chef solo](https://docs.chef.io/chef_solo.html) to configure the resource. Once all the
tools are installed the build process can be [started](how-to-build.html).

## Create the base resources

The first resources to build are the [base resources](../resources/category-base.html), i.e. the
resources that form the base for all other resources. There are three base resources:

* [base.linux](https://github.com/Calvinverse/base.linux) - The base for Linux based resources
  on a virtual machine.
* [base.windows](https://github.com/Calvinverse/base.windows) - The base for Windows based resources
  on a virtual machine.
* [resource.container.linux.consul](https://github.com/Calvinverse/resource.container.linux.consul) -
  The base for Linux based resources in a [Docker](https://docker.io) container.

Obviously you only need to build the base resources which are required for your environment. If you
don't plan to put services in containers then you don't need to create the container base image.

## Create the supporting resources

Once the base resources have been created the next step is to create the supporting resources.

* [resource.hashi.server](https://github.com/Calvinverse/resource.hashi.server) - Defines a
  [Consul]() [server]() instance. Used to form the core of the environment. Once this resource
  has been created you can start your environment. Note however that it won't do anything useful
  without additional resources.
* [resource.hashi.ui](https://github.com/Calvinverse/resource.hashi.ui) - Provides a UI for
  Consul which allows you to view the nodes in the environment, the services that are present
  and the key value (K-V) information.
* [resource.proxy.edge](https://github.com/Calvinverse/resource.proxy.edge) - A
  [reverse proxy]() that allows users to interact with services inside the environment without
  being part of it.
* [resource.secrets](https://github.com/Calvinverse/resource.secrets) - Provides secure storage
  of secrets in the environment.
* Queue
* metrics.storage
* metrics.dashboard
* metrics.monitoring
* documents.storage
* documents.dashboard
* logs.processor

## Create the build resources

Build resources

* build.master
* build.agent.windows
* artefacts

## Configuration

* Create the [environment configuration](configuration.html)

## Deployment

* Deploy the supporting resources
* Deploy the build resources
* Test, fix things and deploy