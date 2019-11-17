Title: How to build
ShowInNavbar: false
---

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



 ... More details on how to build things go here ...

