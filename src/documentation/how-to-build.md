Title: How to build
ShowInNavbar: false
---

**Status: Thoughts only, needs proper formatting**

[Issue #9](https://github.com/Calvinverse/calvinverse.docs/issues/8)

# How to build the resources

To create one of the resources

* The [NuGet command line executable]() should be on the PATH
* The [Git]() executable should be on the PATH
* MsBuild needs to be installed. Needs to be at least MsBuild 15 but higher is compatible
* Need to have a storage place for ISO's and the resource files

* Need a way to build things (i.e. a machine with a hypervisor)
  * For Hyper-V you need to configure a virtual switch that allows the VMS to get to the internet
    so that they can download all the required tooling. The name of this switch should be
    given to packer via the configuration so that Packer can connect the VM to the correct switch
  * If the VMS are on their own ethernet interface then you can run into the Packer bug
* Using [Packer](https://packer.io) to create VMs. By default everything is configured to work
  with Hyper-V, but the Packer configuration can be adjusted to use a different hypervisor

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

  ## Repositories

- Configurations are stored in different repositories
  - General configurations, mostly setting information for the different resources. While the environment
    is running will be stored in the Consul key-value store. The original values are stored in the
    [Calvinverse.Infrastructure]() repository
  - Dashboards
  - Log filters
  - Elasticsearch index templates
- Ideally builds will be configured so that a change to the configuration will be tested and pushed
  to a suitable test environment
- Image repositories. Contain code to create the VM images. Work in progress to make them able to go
  to Azure if necessary
  - With some minor changes could also create images for AWS etc. Images are made using [Packer]() so
    anything packer can create can be made

 ... More details on how to build things go here ...

