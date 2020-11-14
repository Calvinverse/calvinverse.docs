Title: Getting started
ShowInNavbar: false
---

# Getting started

In order to create a build [environment](environments.html) the first step is to determine what the
requirements are for the environment. Once that is known the correct resources can be selected and
then deployed.

## Select the resources

### Build resources

 Important factors to consider are for instance:

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
  in the pipeline e.g. .NET, JavaScript, Java, go etc.. The more different languages need to be
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
  [logs](../resources/category-diagnostics-logs.html) and [metrics](../resources/category-diagnostics-metrics.html)?
  If the environment is small it might not be, however if the environment is large it most likely
  will be.
* Is the environment expected to have some sort of message processing, e.g. for change notifications
  etc.? If so then it may be useful to have a message queue to improve resiliency. By default
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
The Calvinverse resources are  configured to be built on Azure or Hyper-V but the [Packer](https://packer.io)
configuration files can easily be updated to work with other virtualization technologies. So in
order to build the resources a machine should be configured with the selected virtualization
system.

In addition the build scripts for the Calvinverse resources make use of
[MsBuild](https://docs.microsoft.com/en-us/visualstudio/msbuild/msbuild?view=vs-2019) as the
build engine via the [nBuildKit](https://github.com/nbuildkit/nBuildKit.MsBuild) and
[ops-tools-build](https://github.com/ops-resource/ops-tools-build) libraries which means that
MsBuild should also be installed on the machine.

Finally because most of the Calvinverse resources are created via [Chef](https://www.chef.io/) scripts it is
required to install [Ruby](https://www.ruby-lang.org/en/) and [some Ruby gems](how-to-build.html) so
that the [Chefspec](https://docs.chef.io/chefspec.html) tests can be executed. It is not necessary to
install Chef itself given that all resources are created by installing Chef on the resource and
running [Chef solo](https://docs.chef.io/chef_solo.html) to configure the resource. Once all the
tools are installed the build process can be [started](how-to-build.html).

## Create the base resources

The first resources to build are the [base resources](../resources/category-base.html), i.e. the
resources that form the base for all other resources. There are three base resources:

* [base.vm.linux](https://github.com/Calvinverse/base.vm.linux) - The base for Linux based resources
  on a virtual machine.
* [base.vm.windows](https://github.com/Calvinverse/base.vm.windows) - The base for Windows based resources
  on a virtual machine.
* [base.container.linux](https://github.com/Calvinverse/base.container.linux) -
  The base for Linux based resources in a [Docker](https://docker.io) container.

Obviously you only need to build the base resources which are required for your environment. If you
don't plan to put services in containers then you don't need to create the container base image.

## Create the supporting resources

Once the base resources have been created the next step is to create the
[supporting resources](../resources/category-support.html). A number of these resources are required
but not all of them are, so you only need to create those that are required for the
environment you have selected, whether it is [small](example-minimal-build-system.html) or
[large](example-complete-build-system.html).

* [resource.hashi.server](https://github.com/Calvinverse/resource.hashi.server) - Defines a
  [Consul](https://consul.io) [server](https://www.consul.io/docs/internals/architecture.html)
  instance. Used to form the core of the environment. Once this resource has been created you can
  start your environment. Note however that it won't do anything useful without additional resources.
* [resource.hashi.ui](https://github.com/Calvinverse/resource.hashi.ui) - Provides a UI for
  Consul which allows you to view the nodes in the environment, the services that are present
  and the key value (K-V) information.
* [resource.proxy.edge](https://github.com/Calvinverse/resource.proxy.edge) - A
  [reverse proxy](https://fabiolb.net/) that allows users to interact with services inside the
  environment without being part of it.
* [resource.secrets](https://github.com/Calvinverse/resource.secrets) - Provides
  [secure storage](https://vaultproject.io) of secrets in the environment.
* [resource.queue](https://github.com/Calvinverse/resource.queue) - Message
  [queueing](https://www.rabbitmq.com/) for notifications and to relay log messages.
* [resource.metrics.storage](https://github.com/Calvinverse/resource.metrics.storage) - Stores
  [time series metrics](https://www.influxdata.com/products/influxdb-overview/) for all services and
  nodes in the environment.
* [resource.metrics.dashboard](https://github.com/Calvinverse/resource.metrics.dashboard) -
  [Dashboards](https://grafana.com/) for all the time series metrics.
* [resource.metrics.monitoring](https://github.com/Calvinverse/resource.metrics.monitoring) -
  [Alerting](https://www.influxdata.com/time-series-platform/kapacitor/) based on the time series metrics
* [resource.documents.storage](https://github.com/Calvinverse/resource.documents.storage) -
  Indexes [documents](https://www.elastic.co/products/elasticsearch) for search.
* [resource.documents.dashboard](https://github.com/Calvinverse/resource.documents.dashboard) -
  Provides [dashboards](https://www.elastic.co/products/kibana) for the indexed documents
* [resource.logs.processor](https://github.com/Calvinverse/resource.logs.processor) - Processes
  [logs](https://www.elastic.co/products/logstash) from different sources and sends them to the
  document storage.

## Create the build resources

Finally the last set of resources that need to be created are the
[pipeline resources](../resources/category-pipeline.html) which provide the actual build pipeline
capabilities.

* [resource.build.master](https://github.com/Calvinverse/resource.build.master) - The
  [controller](https://jenkins.io) for the build system. Divides the build jobs over the available
  agents and provides the UI for users to interact with.
* [resource.build.agent.windows](https://github.com/Calvinverse/resource.build.agent.windows) -
  Provides the compute resources for the build jobs.
* [resource.artefacts](https://github.com/Calvinverse/resource.artefacts) - Stores different types
  of [artefacts](https://www.sonatype.com/nexus-repository-oss) so that they can be used during in
  the different pipeline stages.

## Configuration

Once all the resources have been created the next step is to create the
[environment configuration](configuration.html) which will determine what the environment will
look like.

The configuration consists of three different parts:

* The configuration files that indicate to Consul which datacenter it should be connecting to. For
  the VM resources these files are stored on an ISO file which can be attached to the VM when a
  new instance of a resource is made. There are ISOs for the Consul server and for Linux and Windows
  Consul clients.
  * Additionally the ISO file also contains a [zone configuration](https://nlnetlabs.nl/documentation/unbound/unbound.conf/)
    file for [Unbound](https://nlnetlabs.nl/projects/unbound/about/) which is the recursive DNS
    resolver which is placed on the base resources to allow cached resolution of DNS addresses from
    both Consul and external sources
* The configuration information for all other services. This configuration information is stored
  in YAML files and should be uploaded to the Consul Key-Value store as soon as the Consul servers
  have been created. All other resources will use the information in the Consul K-V to complete the
  environment specific provisioning steps once a new instance of a resource is connected to the
  environment.
* The configuration, roles and policies which should be uploaded to Vault once at least one of the
  Vault servers has been created. This then allows other resources to obtain the required secrets
  when they are authenticate with Vault.


## Deployment

The final step is to [deploy](setup.html) the newly created resources. The following list is
the suggested order in which the resources should be created. This list assumes that you have
elected to create all the resources. Any steps that describe resources which are not in your
environment can be skipped.

First deploy all the supporting services and make sure they are running correctly.

* To form the core of the environment the first resource to deploy are the Consul server resources.
  These are deployed by creating the desired number of VMs with the generated VM hard drives for the
  [resource.hashi.server](https://github.com/Calvinverse/resource.hashi.server) resource. Once
  the VMs are created start them and wait for the Consul server instances to connect to each other.
* Upload all the K-V configuration values to one of the Consul server instances so that all other
  resources can configure themselves.
* In order to be able to connect to the different resources being deployed you need to deploy at
  least one proxy server by creating an instance of the [resource.proxy.edge](https://github.com/Calvinverse/resource.proxy.edge)
  resource. It pays to do this early so that you can follow the deployment of the different
  resources. So create the desired number of VMs for the reverse proxies. Start the VMs and wait for
  the proxy server to start. Once it is started  you should be able to get to the
  routing page by browsing to `http://<PROXY_IP_ADDRESS>:9998`.
* In order to further follow the instances as they start up it is sensible to deploy the Consul UI
  services by creating an instance of the [resource.hashi.ui](https://github.com/Calvinverse/resource.hashi.ui)
  resource. Once started this should show up on the proxy after which you can reach it by browsing
  to  `http://<PROXY_IP_ADDRESS>/dashboards/consul`.
* Deploy the queue service by deploying at least one but preferably at least three instances of
  the [resource.queue](https://github.com/Calvinverse/resource.queue) resource and configure the
  desired [vhosts](https://www.rabbitmq.com/vhosts.html), queues and users.
* Deploy the Vault instances by creating the desired number of VMs of the [resource.secrets](https://github.com/Calvinverse/resource.secrets)
  resource. Start the VMs and wait for the Vault service to show up in the Consul cluster. Once the
  service is connected it can be [initialized](https://www.vaultproject.io/docs/commands/operator/init.html).
* After creating and initializing the Vault resources we can [unseal](https://www.vaultproject.io/docs/concepts/seal.html)
  the services and then set all the [mounts](https://www.vaultproject.io/docs/secrets/) and
  [policies](https://www.vaultproject.io/docs/concepts/policies.html).
* Deploy the metrics resources in the following order:
  * Deploy the InfluxDb database server by deploying one instance of the
    [resource.metrics.storage](https://github.com/Calvinverse/resource.metrics.storage) resource.
  * Deploy the Grafana service by deploying one instance of the
    [resource.metrics.dashboard](https://github.com/Calvinverse/resource.metrics.dashboard) resource.
    Once the service is up an running you can connect to it by browsing to
    `http://<PROXY_IP_ADDRESS>/dashboards/metrics`
  * Optionally deploy a Kapacitor service by deploying an instance of the
    [resource.metrics.monitoring](https://github.com/Calvinverse/resource.metrics.monitoring) resource.
* Deploy the document resources in the following order:
  * Deploy an Elasticsearch cluster by deploying the desired number of instances of the
    [resource.documents.storage](https://github.com/Calvinverse/resource.documents.storage) resource.
  * Deploy a Kibana service by deploying an instance of the
    [resource.documents.dashboard](https://github.com/Calvinverse/resource.documents.dashboard)
    resource. Once the Kibana service has started you can reach it by browsing to
    `http://<PROXY_IP_ADDRESS>/dashboards/documents`.
  * Deploy one or more Logstash instances by deploying the desired number of instances of the
    [resource.logs.processor](https://github.com/Calvinverse/resource.logs.processor) resource.

Once the supporting services have been deployed then the build resources can be deployed.

* Deploy one instance of the Jenkins controller by deploying an instance of the
  [resource.build.master](https://github.com/Calvinverse/resource.build.master) resource. Once
  the instance is running the Jenkins service will start and can be reached by browsing to
  `http://<PROXY_IP_ADDRESS>/builds`
* Deploy one or more build agents by deploying the desired number of instances of the
  [resource.build.agent.windows](https://github.com/Calvinverse/resource.build.agent.windows) resource.
* Deploy the Nexus artefact storage service by deploying a single instance of the
  [resource.artefacts](https://github.com/Calvinverse/resource.artefacts) resource. Once Nexus is
  running it can be reached by browsing to `http://<PROXY_IP_ADDRESS>/artefacts`.

Once the build resources have been added the environment is complete and the next stage can begin,
adding the product builds.
