Title: How to build
ShowInNavbar: false
---

# Building the Calvinverse resources

The repositories in the [Calvinverse organization](https://github.com/Calvinverse) are divided into
image repositories, containing code for the creation of VM and Docker images, and configuration
repositories, which contain some form of configuration information. The configuration repositories
either create ZIP and ISO artefacts or they contain scripts that allow pushing data to the
Consul key-value store in the environment.

In order to execute builds that create artefacts of some kind you will for the very least need:

- The [NuGet command line executable](https://www.nuget.org/downloads) installed and available on
  the PATH.
- The [Git](https://git-scm.com/) installed and available on the PATH.
- [MsBuild](https://docs.microsoft.com/en-us/visualstudio/msbuild/msbuild?view=vs-2019) needs to be
  installed. This needs to be at least MsBuild 15.0 but higher should be compatible.

Specific repository types may have additional demands on the environment they are build in. More details
on these specific demands will be provided in the sections below.

## Building VM images

Most of the resources in Calvinverse are VM images at the moment although there are a few repositories
containing code for Docker containers. There are currently two types of VM hypervisors supported,
[Azure](https://en.wikipedia.org/wiki/Microsoft_Azure) and [Hyper-V](https://en.wikipedia.org/wiki/Hyper-V).
Since images are created with [Packer](https://packer.io) it would be relatively easy to create
images for AWS and other hypervisors.

All VM resources are based on either one of the ['base' images](../resources/category-base.html),
one for the [Linux](https://github.com/Calvinverse/base.vm.linux) based resources and
one for the [Windows](https://github.com/Calvinverse/base.vm.windows) based resources.
These base images consist of an operating system install combined with a number of default
applications, e.g. [Consul](https://consul.io) for service discovery,
[Envoy](https://www.envoyproxy.io/) for service mesh and
[Telegraf](https://www.influxdata.com/time-series-platform/telegraf/) for metrics collection.
So in order to build any of the VM resources you will first have to build and store a copy of these
base images.

Additional prerequisites for building the virtual machine images are:

- [Ruby](https://www.ruby-lang.org/en/) installed with the following [Chef](https://www.chef.io/)
  gems
  - [berkshelf](https://rubygems.org/gems/berkshelf)
  - [bundler](https://rubygems.org/gems/bundler)
  - [chef](https://rubygems.org/gems/chef)
  - [chef-zero](https://rubygems.org/gems/chef-zero)
  - [chefspec](https://rubygems.org/gems/chefspec)
  - [foodcritic](https://rubygems.org/gems/foodcritic)
  - [rspec_junit_formatter](https://rubygems.org/gems/rspec_junit_formatter)
  - [rubocop](https://rubygems.org/gems/rubocop)

Once the base images are created you can start creating the VM images. To build an image you can
invoke the following command line.

    msbuild entrypoint.msbuild /t:build

Additional properties should be provided depending on the type of hypervisor, i.e. Hyper-V or Azure,
you are targeting.

Once the image has been created you can run a series of simple smoke tests to verify that the image
is valid. In order to execute these tests invoke the following command line.

    msbuild entrypoint.msbuild /t:test

This will deploy the image to your selected hypervisor, connect to the VM, execute the test scripts
and finally delete the test VM.

### Building Azure images

When you are building images for Azure you will need, in addition to the standard application installs:

- An Azure subscription into which the images can be created and stored.
- Credentials for Packer to use. The assumption is that a
  [service principal](https://www.packer.io/docs/builders/azure#azure-active-directory-service-principal)
  will be used by specifying a client ID and the path to a client certificate.
- A [resource group](https://docs.microsoft.com/en-us/azure/azure-resource-manager/management/manage-resource-groups-portal)
  into which the images will be placed once they are created. Note that in order to pull images
  created with another subscription you will have to use an
  [image gallery](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/shared-image-galleries).

In general building one of the images will take about 10 - 30 minutes depending on what is being
installed in the image. So while the costs of creating images should be fairly small there will still
be a cost, especially if you create lots of images.

Once all the prerequisites have been satisfied you can start the build process with the following
command line

    msbuild entrypoint.msbuild /t:build
      /P:ShouldCreateAzureImage=true
      /P:AzureLocation=<AZURE_LOCATION>
      /P:AzureClientId="<AZURE_CLIENT_ID>"
      /P:AzureClientCertPath="<PATH_TO_CERT_FILE_FOR_AZURE_CLIENT>"
      /P:AzureSubscriptionId="<AZURE_SUBSCRIPTION_ID>"
      /P:AzureImageResourceGroup="<RESOURCE_GROUP_NAME>"

This command line applies to both the base images and the normal resource images. At the end of the
build process the image will be placed in the selected resource group and the following tags will
be applied

- **category** - The category of the image, generally the name of the image repository as set in the
  `ProductName` property in the `ops.props` MsBuild file in the root of the repository.
- **commit** - The SHA1 of the GIT commit the image was created from.
- **createdby** - Set to `packer` to indicate the image was created by Packer.
- **date** - Set to the date the image was created.
- **stage** - By default set to `qa` to indicate that the image is ready for QA.
- **version** - The version of the image. Calculated using
  [GitVersion](https://github.com/GitTools/GitVersion), which bases the version number on the state
  of the GIT repository.

To test the newly created image you can execute the following command line

    msbuild entrypoint.msbuild /t:test
      /P:ShouldCreateAzureImage=true
      /P:AzureLocation=<AZURE_LOCATION>
      /P:AzureClientId="<AZURE_CLIENT_ID>"
      /P:AzureClientCertPath="<PATH_TO_CERT_FILE_FOR_AZURE_CLIENT>"
      /P:AzureSubscriptionId="<AZURE_SUBSCRIPTION_ID>"
      /P:AzureImageResourceGroup="<RESOURCE_GROUP_NAME>"
      /P:AzureTestImageResourceGroup="<TEST_RESOURCE_GROUP_NAME>"

This will create a new VM in Azure with the originally created image and then run a series of smoke
tests. Note that if the tests pass a test image will be dropped in a resource group with the
`TEST_RESOURCE_GROUP_NAME` name.These images should periodically be removed.

### Building Hyper-V images

In order to build images for Hyper-V you will need the following additional prerequisites

- A Windows installation with Hyper-V installed. This can be either a server or a desktop version
  of Windows.
- A virtual switch which allows the VMs to get to the internet so that they can download all the
  required applications and packages. The name of this switch should be given to packer via the
  configuration so that Packer can connect the VM to the correct switch. The default value is set
  to `VM-LAN` and can be changed in the `packer.hyperv.props` MsBuild file.
- A directory in which the OS install ISO files are stored. These ISO files should be the ISO's used
  to install the desired OS, either Linux or Windows, on an empty VM. The ISO's will need to be
  suitable for unattended installs.
- A directory in which the base image artefact files are placed.

Once all the tools are installed you can start the build process of a base image with the following
command line

    msbuild entrypoint.msbuild /t:build
      /P:ShouldCreateHypervImage=true
      /P:IsoDirectory=<PATH_TO_ISO_DIRECTORY>

If you want to build a normal resource image then you should use the following command line

    msbuild entrypoint.msbuild /t:build
      /P:ShouldCreateHypervImage=true
      /P:RepositoryArchive=<PATH_TO_DIRECTORY_CONTAINING_BASE_IMAGE_ARTEFACTS>

Once the build process completes it will place a ZIP artefact containing the Hyper-V files in the
`build/deploy` directory of the workspace. To test the image that was created you can run the
smoke tests by executing the following command line

    msbuild entrypoint.msbuild /t:test
      /P:ShouldCreateHypervImage=true

This assumes that the image artefacts will be available in the `build/deploy` directory in the
workspace.


## Building containers

Some of the Calvinverse services are packaged in Docker containers. As with the virtual machine
images, the Docker containers for these services are also created from a
[base image](https://github.com/Calvinverse/base.container.linux). Once again this base container
contains the default applications.

In order to build these Docker images you will need the following additional prerequisites

- Machine with Docker on it. Currently all containers are Linux Docker containers so you can
  either have a Linux host machine or a Windows host machine with Linux containers.
- A Docker repository to upload the containers.

Once the prerequisites have been satisfied you can run the build by executing the following
command line

    msbuild entrypoint.msbuild /t:build
      /P:DirConsulCertificates="<LOCATION_OF_CONSUL_CERTIFICATE_BUNDLE>"

where `LOCATION_OF_CONSUL_CERTIFICATE_BUNDLE` points to a directory containing the
[Consul certificate files](https://learn.hashicorp.com/tutorials/consul/tls-encryption-secure),
specifically the [client certificate bundle](https://learn.hashicorp.com/tutorials/consul/tls-encryption-secure#configure-the-clients)

Tags for the Docker containers can be specified in the `docker - pack` section of the
`ops.artefacts.props` file.


## Building configurations

- Configurations are stored in different repositories
  - General configurations, mostly setting information for the different resources. While the environment
    is running will be stored in the Consul key-value store. The original values are stored in the
    [Calvinverse.Configuration]() repository
  - Dashboards
  - Log filters
  - Elasticsearch index templates
