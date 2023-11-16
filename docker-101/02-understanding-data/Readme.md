## Experiment with how installing something into a container at runtime behaves! -->

### Create a container from the ubuntu image
#### The --interactive --tty make the container interactive often used together to run a container in interactive mode, allowing you to interact with the command line inside the container, the --rm remove the container upn exit-
docker run --interactive --tty --rm ubuntu:22.04

### Try to ping google.com
ping 8.8.8.8 -c 5 # This results in `bash: ping: command not found`

### Install ping
apt update
apt install iputils-ping --yes

ping 8.8.8.8 -c 5 # This time it succeeds!
exit

### Let's try that again:
docker run -it --rm ubuntu:22.04
ping 8.8.8.8 -c 5 # It fails!

It fails the second time because we installed it into that read/write layer specific to the first container, and when we tried again it was a separate container with a separate read/write layer!

#### We can give the container a name so that we can tell docker to reuse it:
docker run -it --name my-ubuntu-container ubuntu:22.04

#### Install & use ping
apt update
apt install iputils-ping --yes
ping 8.8.8.8 -c 5
exit

#### List all containers
docker container ps -a | grep my-ubuntu-container
docker container inspect my-ubuntu-container

#### Restart the container and attach to running shell
docker start my-ubuntu-container
docker attach my-ubuntu-container

We generally never want to rely on a container to persist the data, so for a dependency like this, we would want to include it in the image:

#### Build a container image with ubuntu image as base and ping installed i.e buidling a dependencies into the image
docker build --tag my-ubuntu-image -<<EOF
FROM ubuntu:22.04
RUN apt update && apt install iputils-ping --yes
EOF
##### The FROM... RUN... stuff is part of what is called a Dockerfile that is used to specify how to build a container image. 

# Run a container based on that image
docker run -it --rm my-ubuntu-image

# Confirm that ping was pre-installed
ping 8.8.8.8 -c 5