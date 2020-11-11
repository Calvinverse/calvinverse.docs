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

- The [NuGet command line executable](https://www.nuget.org/downloads) should be on the PATH.
- The [Git](https://git-scm.com/) executable should be on the PATH
- [MsBuild](https://docs.microsoft.com/en-us/visualstudio/msbuild/msbuild?view=vs-2019) needs to be
  installed. This needs to be at least MsBuild 15.0 but higher should be compatible.

Specific repository types may have additional demands on the environment they are build in. More details
on these specific demands will be provided in the sections below.

## Building VM images

Most of the resources in Calvinverse are VM images at the moment although there are a few repositories
containing code for Docker containers. To create one of the resources you need to have the following
applications on the machine you want to build on:





* Build the base resources first




 Work in progress to make them able to go
  to Azure if necessary
  - With some minor changes could also create images for AWS etc. Images are made using [Packer]() so
    anything packer can create can be made

There are currently two types of VM hypervisors supported, Hyper-V and Azure. Depending on
for which you want to build some other things have to be available

* Build the base resources first


Once all the tools are installed you can start the build process with the following command line

    msbuild entrypoint.msbuild /t:build /P:IsoDirectory=<PATH_TO_THE_APPROPRIATE_ISO_FILE>


- How to build
  - Using [nBuildKit]() and [ops-tools-build]() for the build scripts
  - Invoke the build generally with `msbuild entrypoint.msbuild /t:build` with additional properties
  - Should drop outputs in `build/deploy`. Generally a zip file with the Hyper-V files
  - Requirements
    - nuget.exe on the command line
    - Hyper-V installed and the user is a Hyper-V admin
    - Hyper-V switch with the correct name
- How to test
  - Smoke tests -> `msbuild entrypoint.msbuild /t:test` -> runs standard tests
  - Deploy into an environment, run tests

### Building Azure images

Once all the tools are installed you can start the build process with the following command line

    msbuild entrypoint.msbuild /t:build /P:IsoDirectory=<PATH_TO_THE_APPROPRIATE_ISO_FILE>

### Building Hyper-V images

- Image repositories. Contain code to create the VM images.

- Need a machine which has Hyper-V installed
- For Hyper-V you need to configure a virtual switch that allows the VMS to get to the internet
  so that they can download all the required tooling. The name of this switch should be
  given to packer via the configuration so that Packer can connect the VM to the correct switch
- If the VMS are on their own ethernet interface then you can run into the Packer bug
- Using [Packer](https://packer.io) to create VMs. By default everything is configured to work
  with Hyper-V, but the Packer configuration can be adjusted to use a different hypervisor

Once all the tools are installed you can start the build process with the following command line

    msbuild entrypoint.msbuild /t:build /P:IsoDirectory=<PATH_TO_THE_APPROPRIATE_ISO_FILE>




## Building containers



## Building configurations

- Configurations are stored in different repositories
  - General configurations, mostly setting information for the different resources. While the environment
    is running will be stored in the Consul key-value store. The original values are stored in the
    [Calvinverse.Configuration]() repository
  - Dashboards
  - Log filters
  - Elasticsearch index templates
