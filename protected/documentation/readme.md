@title Server Images

# Server Images

This module builds upon the versioning provided by the ``nabu.misc.git`` module. Specifically, it assumes the git repository layout as provided by that module is available.

While the git module focuses on delivering environment specific versions of a particular project, this module focuses on combining one or more projects with a stable snapshot of the Nabu platform and the necessary configuration to generate a runnable version.

This image can then be wrapped in an AMI, docker image, ansible script,... depending on your infrastructure of choice.

## Build Image

It all starts with a build image, it represents a single server layout. You have have hundreds of server instances, but they will all have this particular image.

An image has three things:

- :cloud profile: an image has a configurable cloud profile, this will determine which modules are available
- :projects: an image can contain one or more projects, available from the same build folder as configured using ``git.build``.
- :environments: an image can contain one or more environments

For simplicity sake, the name of the environment as defined in the image is assumed to correspond to an environment you have configured in your build.
Each environment can be configured.

[:.resources/overview-configuration.png]

## Build Instance

Once you have configured your image, you can create an instance of it.

Your build instance will combine a specific version of each listed project with a specific nabu version. 

You can then proceed to download or otherwise generate the full image for each environment, this image will contain the nabu version as it was at the time of the instance creation as well as the projects specifically in the version they were at the time of the image and for their respective environments.
Note that you always receive the latest RC from a given project, so if you download the instance, then do some additional environment-specific configuration, you can redownload the instance and it will contain these new parameters.

An instance is a _static_ cross-environment snapshot of a particular set of projects. That means if you download the qlty version of a particular instance, run it through an exhaustive quality assurance process and weeks later, download the prd version of that exact release. You can download it, knowing that you are getting the exact same codebase differing only in environment-specific parameters.

### Versioning

An instance has a version and a patch level. Suppose you are running v1.0 in prd. In the mean time you keep on developing and you may have released the project multiple times without doing an additional prd deployment. However, you notice a problem in prd, it has to be hotfixed.
You can create a hotfix for the affected project(s) in the git management screens. Once the hotfixed version is available, you can "patch" the v1.0 image instance. At that point, it will look for any patches to the projects that were already in v1.0. You can also choose to update the nabu instance or leave it as it was during the v1.0 release. This will result in a v1.1 patched release which you can push to prd.

## Web Interface

There is a web interface shipped with this module that you can use create, configure and download your images:

[:.resources/overview-web.png]

## Testing

When you add a project, you can set the scope to runtime or testing. If the scope is set to "testing", the project will only be packaged if the environment you are requesting is also marked as a testing environment.
This allows you to ship test projects for automated testing reasons.

This module comes with the necessary tools to define test suites and test cases. These tests can be run manually or can be linked to a particular test service in a test package. At that point they can be run automated.

The end goal is to validate the quality of a particular image instance.