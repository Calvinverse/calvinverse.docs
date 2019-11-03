Title: Getting started
ShowInNavbar: false
---

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

With all the requirements for the different build environments decided it is possible to start
creating the resources for the environment.

* Ideally you'll have elected to have the meta build environment. In that case you'll have to
  bootstrap the environment. If you have elected not to have this environment then you are essentially
  in always bootstrapping mode
  * Configure a developer machine with Hyper-V to create the (first) resources
    * note that calvinverse is currently setup to use Hyper-V but all images are created by using
      [Packer](https://packer.io) which means it should be relatively straight forward to change to
      a different hosting technology
* Build the base resources first
* Build the images for the supporting infrastructure
* Build the images for the build resources
* Create the [environment configuration](configuration.html)
* Deploy the supporting resources
* Deploy the build resources
* Test, fix things and deploy