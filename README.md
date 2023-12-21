This is the repo for all my learning on docker. i should be updating this repo as i continue to learn more about docker.

What are Containers?
Containers are packages of software that contain all of the necessary elements to run in any environment. OR A container is a standard unit of software that packages up code and all its dependencies so the application runs quickly and reliably from one computing environment to another. 

A Docker container image is a lightweight, standalone, executable package of software that includes everything needed to run an application: code, runtime, system tools, system libraries and settings.
At runtime, container images turn into containers; similarly, when Docker containers run on Docker Engine, images turn into containers. Containerised software is available for both Windows and Linux-based apps, and it will always operate the same manner regardless of the infrastructure/hardward. Software is isolated from its surroundings by containers, which also guarantee that it functions consistently even in case of variations, such as between development and staging environments.

At the application layer, containers are an abstraction that bundles/combine dependencies and code. On a single system/computer, several containers can share the OS kernel with one another.

At the application layer, containers are an abstraction that bundles/combine dependencies and code. On a single machine/computer, several containers can operate independently as separate processes in user space(memory), sharing the OS kernel with one another.

![containe vs Virtual Machine](https://github.com/juw0n/docker-getting-started/blob/main/container%20VS%20Vm.png)

Overall, A container is a host machine process that is isolated from all other processes by virtue of being run in a sandbox (i.e the practice of running a program or application in a restricted environment, isolated from the rest of the system or other applications.). Linux has long-standing features like kernel namespaces and cgroups, which are utilised for this kind of isolation.