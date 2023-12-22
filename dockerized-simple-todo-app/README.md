# Getting started
A mini dockerrized simple todo list manager that runs on Node.js project, from docker docs.

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

docker run command is used to run/start a container by specifying the name of the image to use to create the container.

The -d flag (short for --detach) runs the container in the background. The -p flag (short for --publish) creates a port mapping between the host and the container. The -p flag takes a string value in the format of HOST:CONTAINER, where HOST is the address on the host, and CONTAINER is the port on the container. The command publishes the container's port 3000 to 127.0.0.1:3000 (localhost:3000) on the host. 
Without the port mapping, you wouldn't be able to access the application from the host.

running http://localhost:3000 of a web browser will open/lunch the app running in the coutainer.

* To list running container from the CLI:
==> docker ps
* To list both running and stopped container from the CLI:
==> docker ps -a
* To stop a running container:
==> docker stop <container-id>
* To removed a stopped container;
==> docker rm <container-id>
* To force remove a runnit container:
==> docker rm -f <container-id>
* To check the list of local availbale docker images:
==> docker image ls || docker images || docker image list

### Sharing Docker Images
After building a docker image, you can share it. To share Docker images, you have to use a Docker registry. This is the default registry for storing and sharing docker images.
All you need to do is create an account with docker and push docker images to the registry. you have the option of making the image public or private.

### Push the image:
* Use the docker tag command to give the getting-started image a new name. Replace YOUR-USER-NAME with your Docker ID.
==> docker tag getting-started YOUR-USER-NAME/NEW-IMAGE-NAME
* After tagging, use *docker push* to push the image to docker registry.
==>  docker push YOUR-USER-NAME/NEW-IMAGE-NAME
* To runs a new command inside a runnig container you use docker exec
==> docker exec <container-id> <command>


### The container's filesystem
When a container runs, it uses the various layers from an image for its filesystem. Each container also gets its own "scratch space" to create/update/remove files. Any changes made in a container won't be seen in another container, even if they're using the same image.

### Container volumes
Every container starts from the image definition each time it starts. While containers can create, update, and delete files, those changes are lost when you remove the container. Docker isolates all changes to that container. 
With volumes, you can change all of this i.e change made in a container.

*Volumes* provide the ability to connect specific filesystem paths of the container back to the host machine. If you mount a directory in the container, changes in that directory are also seen on the host machine. If you mount that same directory on a container on restarts, you'd see the same files.

There are two main types of volumes or ways of mounting volume to a container:
* volume mounts:
This type of volume are created on docker deamon i.e on docker app itself. this type of volume is created using *volume create* command. and mount the created volume on/to acontainer using *--mount* command. 
docker is the one that decide the location of this volume. the location of the volume can be seen/verify using the *volume inspec* command.

Create a volume by using the docker volume create command.
==> docker volume create <volume-name>
* you mount exiting volume on docker deamon to a container using <--mount> commad
==> docker run -dp 127.0.0.1:3000:3000 --mount type=volume,src=todo-db,target=/etc/todos simple-todo-app
* To know where docker store data inspect the volumes using <docker volume inspect> command.

** The Mountpoint is the actual location of the data on the disk.

* Bind mounts:
A bind mount is another type of mount that allows you to connect a directory from the host's machine file system to the container.
While developing an application, you can employ a bind mount to link the source code directly into the container. Any modifications made to the code are instantly visible to the container upon saving a file. Consequently, you can execute processes within the container that actively monitor filesystem alterations and react accordingly.

bind mount allow shareing of file between the host and the container, and changes are immediately reflected on both sides. 

==> docker run -it --mount type=bind,src="$(pwd)",target=/src ubuntu bash

The above docker command starts a ubuntu container from an ubuntu image in an interactive bash session in the root directory of the container's filesystem.

The *--mount* option tells Docker to create a bind mount, where *src* is the current working directory on your host machine, and *target* is where that directory should appear inside the container (/src).

### Run your app in a development container
The following steps describe how to run a development container with a bind mount that does the following:

* Mount your source code into the container
* Install all dependencies
* Start nodemon to watch for filesystem changes
You can use the CLI or Docker Desktop to run your container with a bind mount.

for example:
==>
docker run -dp 127.0.0.1:3000:3000 \
    -w /app --mount type=bind,src="$(pwd)",target=/app \
    node:18-alpine \
    sh -c "yarn install && yarn run dev"


The following is a breakdown of the command:

* -dp 127.0.0.1:3000:3000 - same as before. Run in detached (background) mode and create a port mapping
* -w /app - sets the "working directory" or the current directory that the command will run from
* --mount type=bind,src="$(pwd)",target=/app - bind mount the current directory from the host into the /app directory in the container
* node:18-alpine - the image to use. Note that this is the base image for your app from the Dockerfile
* sh -c "yarn install && yarn run dev" - the command. You're starting a shell using sh (alpine doesn't have bash) and running yarn install to install packages and then running yarn run dev to start the development server. If you look in the package.json, you'll see that the dev script starts nodemon.

### You can watch the logs using

==> docker logs <container-id>

When done watching the logs, exit out by hitting *Ctrl+C*

## Multi container apps
One of the best practice in app containarization i belive is that "a container should do one thing and do it well". hence different part if an app should run in their own container and all the different container should then be connected to work together via NETWORKING.
Some of the reason to run different part of an app in seperate container are:

* Scaling: There's a good chance you'd have to scale APIs and front-ends differently than databases.
* Versioning: Separate containers let you version and update versions in isolation.
* Running multiple processes will require a process manager which adds complexity to container startup/shutdown.

### Container networking
Containers, by default, run in isolation and don't know anything about other processes or containers on the same machine. containers are allow to talk to one another via NETWORKING (container networking - placing containers on the same network). each container has its own IP address.

There are two ways to put a container on a network:

* Assign the network when starting the container.
* Connect an already running container to a network.

To create a network in docker, you use the *network create* command:
==> docker network create <network-name>

Start a MySQL container and attach it to the network;
==>
docker run -d \
    --network todo-app --network-alias mysql \
    -v todo-mysql-data:/var/lib/mysql \
    -e MYSQL_ROOT_PASSWORD=secret \
    -e MYSQL_DATABASE=todos \
    mysql:8.0

* If you run another container on the same network, how do you find the container?

Ans: To answer the questions above and better understand container networking, you're going to make use of the *nicolaka/netshoot* container, which ships with a lot of tools that are useful for troubleshooting or debugging networking issues.

* Start a new container using the nicolaka/netshoot image. Make sure to connect it to the same network.

==> docker run -it --network todo-app nicolaka/netshoot

* Inside the container, you're going to use the dig command, which is a useful DNS tool. You're going to look up the IP address for the hostname mysql.

==> dig mysql
*  start your dev-ready container:
==> docker run -dp 127.0.0.1:3000:3000 \
  -w /app -v "$(pwd):/app" \
  --network todo-app \
  -e MYSQL_HOST=mysql \
  -e MYSQL_USER=root \
  -e MYSQL_PASSWORD=secret \
  -e MYSQL_DB=todos \
  node:18-alpine \
  sh -c "yarn install && yarn run dev"

*  To see the logs for the container (docker logs -f <container-id>)
*  Connect to the mysql database and prove that the items are being written to the database. 
==> docker exec -it <mysql-container-id> mysql -p todos
==> mysql> select * from todo_items;

### Use Docker Compose

Docker Compose is a tool that helps you define and share multi-container applications. With Compose, you can create a YAML file to define the services and with a single command, you can spin everything up or tear it all down.

sample of a docker compose document:
=====================================================
services:
  app:
    image: node:18-alpine
    command: sh -c "yarn install && yarn run dev"
    ports:
      - 127.0.0.1:3000:3000
    working_dir: /app
    volumes:
      - ./:/app
    environment:
      MYSQL_HOST: mysql
      MYSQL_USER: root
      MYSQL_PASSWORD: secret
      MYSQL_DB: mysql

  mysql:
    image: mysql:8.0
    volumes:
      - todo-mysql-data:/var/lib/mysql
    environment:
      MYSQL_ROOT_PASSWORD: secret
      MYSQL_DATABASE: mysql

volumes:
  todo-mysql-data:
=======================================================================

* start/run docke compose file by using the command below:
==> docker compose up -d
(-d flag to run everything in the background.)
* Look at the compose logs using:
==> docker compose logs -f
* Tear all containers created by docker compose using the command:
==> docker compose down

### Image-building best practices
To see the command that was used to create each layer within an image, use:
==> docker image history <image name>

Each of the lines represents a layer in the image. The display here shows the base at the bottom with the newest layer at the top. Using this, you can also quickly see the size of each layer, helping diagnose large images.
#### Layer caching and multi-stage builds.
there's an important lesson to learn to help decrease build times for container images. Once a layer changes, all downstream layers have to be recreated as well.