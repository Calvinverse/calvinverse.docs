# Getting started

* Figure out what you need
  * Small system or a big system
  * Do you want metrics for the environment
  * Do you want logs for the environment
  * Do you need a test environment for your build system
    * Unless you have a small environment you probably do
    * Really depends on how many people use your prod system and if you can afford
      to take it down
    * How much churn are you expecting in the build system itself
  * Do you need a build environment to create your build environment (inception warning)
    * If you don't expect much change then this is not necessary. If you expect
      lots of changes and / or you have lots of people working on improving your
      build system then they will need their own build system
* Configure a machine with Hyper-V to create some resources
* Build the base resources
* Build the images for the supporting infrastructure
* Build the images for the build resources
* Deploy the supporting resources
* Deploy the build resources
* Test, fix things and deploy