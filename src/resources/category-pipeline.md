Title: Pipeline
---

# Pipeline resources

The pipeline resources are the resources that provide the actual build capacity in the environment.
This category contains the following resources

* [resource.build.master](https://github.com/Calvinverse/resource.build.master) - Contains the source
  code and Packer configuration files to create an image, for either Hyper-V or Azure, that contains
  an instance of the Jenkins build server.
* [resource.build.agent.windows](https://github.com/Calvinverse/resource.build.agent.windows) - Contains
  the source code and Packer configuration files to create an image, for either Hyper-V or Azure, that
  contains an instance of a Jenkins build agent on a Windows operating system.
* [resource.artefacts](https://github.com/Calvinverse/resource.artefacts) - Contains the source code
  and Packer configuration files to create an image, for either Hyper-V or Azure, that contains an
  instance of the Sonatype Nexus server.
