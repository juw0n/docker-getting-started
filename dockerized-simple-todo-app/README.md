# Getting started
A mini dockerrized simple todo list manager that runs on Node.js project, from docker docs.

This repository is a sample application for users following the getting started guide at https://docs.docker.com/get-started/.

The application is based on the application from the getting started tutorial at https://github.com/docker/getting-started

Steps:
1. Get project files/repo
Clone the project repo from the cloud and confirm that it has all the required files and dependeincies.
2. Build the app's image
To build the image, you'll need to use a Dockerfile. 
* A Dockerfile is simply a text-based file with no file extension that contains a script of instructions. 
* Docker uses this script to build a container image.

==> docker build -t simple-todo-app .

docker build command uses the Dockerfile to build a new image. the -t flag tags the image for the name it should be called. The . at the end of the docker build command tells Docker that it should look for the Dockerfile in the current directory.
3.  Starting an app container

==> docker run -dp 127.0.0.1:3000:3000 simple-todo-app

The -d flag (short for --detach) runs the container in the background. The -p flag (short for --publish) creates a port mapping between the host and the container. The -p flag takes a string value in the format of HOST:CONTAINER, where HOST is the address on the host, and CONTAINER is the port on the container. The command publishes the container's port 3000 to 127.0.0.1:3000 (localhost:3000) on the host. 
Without the port mapping, you wouldn't be able to access the application from the host.

running http://localhost:3000 of a web browser will open/lunch the app running in the coutainer.

## To list running container from the CLI:
==> docker ps
## To list both running and stopped container from the CLI:
==> docker ps -a
## To stop a running container:
==> docker stop <container-id>
## To removed a stopped container;
==> docker rm <container-id>
## To check the list of local availbale docker images:
==> docker image ls || docker images || docker image list

#### Sharing Docker Images
After building a docker image, you can share it. To share Docker images, you have to use a Docker registry. This is the default registry for storing and sharing docker images.
All you need to do is create an account with docker and push docker images to the registry. you have the option of making the image public or private.
#### Push the image:
* Use the docker tag command to give the getting-started image a new name. Replace YOUR-USER-NAME with your Docker ID.
==> docker tag getting-started YOUR-USER-NAME/NEW-IMAGE-NAME
* After tagging, use *docker push* to push the image to docker registry.
==>  docker push YOUR-USER-NAME/NEW-IMAGE-NAME