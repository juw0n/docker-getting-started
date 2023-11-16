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
##### PS: anything (dependency or library) needed in a container at runtime should be built into the image!

#### Run a container based on that image
docker run -it --rm my-ubuntu-image

##### Confirm that ping was pre-installed
ping 8.8.8.8 -c 5

B. Persisting Data Produced by Application running in docker:
Applications frequently generate data that must be carefully preserved, even in the event that the containers are destroyed and rebuilt (data from databases, user-uploaded files, etc.). Fortunately, Volumes and mounts, a feature of Docker (and containers more generally) can handle this use case!

Volumes and mounts enable to definition of location for data to remain after a container life cycle. The data may reside in a host filesystem location (bind mount), or in a volume mount location controlled by Docker.

#### Let's see what happens when we create some data at runtime inside a container!

#### Create a container from the ubuntu image
docker run -it --rm ubuntu:22.04

##### Make a directory and store a file in it
mkdir my-data
echo "Hello from the container!" > /my-data/hello.txt

##### Confirm the file exists
cat my-data/hello.txt
exit

#### If we then create a new container, (as expected) the file does not exist!
##### Create a container from the ubuntu image
docker run -it --rm ubuntu:22.04

##### Check if the file exists
cat my-data/hello.txt 

i. Volume Mounts
We can use volumes and mounts to safely persist the data.

# create a named volume
docker volume create my-volume

# Create a container and mount the volume into the container filesystem
docker run  -it --rm --mount source=my-volume,destination=/my-data/ ubuntu:22.04
# There is a similar (but shorter) syntax using -v which accomplishes the same
docker run  -it --rm -v my-volume:/my-data ubuntu:22.04

# Now we can create and store the file into the location we mounted the volume
echo "Hello from the container!" > /my-data/hello.txt
cat my-data/hello.txt
exit

We can now create a new container and mount the existing volume to confirm the file persisted:
# Create a new container and mount the volume into the container filesystem
docker run  -it --rm --mount source=my-volume,destination=/my-data/ ubuntu:22.04
cat my-data/hello.txt # This time it succeeds! 
exit

Where is this data located? 
On linux it would be at /var/lib/docker/volumes... but remember, on docker desktop, Docker runs a linux virtual machine.

# Create a container that can access the Docker Linux VM
# Pinning to the image hash ensures it is this SPECIFIC image and not an updated one helps minimize the potential of a supply chain attack
docker run -it --rm --privileged --pid=host justincormack/nsenter1@sha256:5af0be5e42ebd55eea2c593e4622f810065c3f45bb805eaacf43f08f3d06ffd8

# Navigate to the volume inside the VM at:
ls /var/lib/docker/volumes/my-volume/_data
cat /var/lib/docker/volumes/my-volume/_data/hello.txt # Woohoo! we found our data!

This approach can then be used to mount a volume at the known path where a program persists its data:

# Create a container from the postgres container image and mount its known storage path into a volume named pgdata
docker run -it --rm -v pgdata:/var/lib/postgresql/data -e POSTGRES_PASSWORD=foobarbaz postgres:15.1-alpine

ii. Bind Mounts
Alternatively, we can mount a directory from the host system using a bind mount:

Alternatively, we can mount a directory from the host system using a bind mount:

# Create a container that mounts a directory from the host filesystem into the container
docker run  -it --rm --mount type=bind,source="${PWD}"/my-data,destination=/my-data ubuntu:22.04
# Again, there is a similar (but shorter) syntax using -v which accomplishes the same
docker run  -it --rm -v ${PWD}/my-data:/my-data ubuntu:22.04

echo "Hello from the container!" > /my-data/hello.txt

# You should also be able to see the hello.txt file on your host system
cat my-data/hello.txt
exit

Bind mounts can be nice if you want easy visibility into the data being stored, but there are a number of reasons outlined at https://docs.docker.com/storage/volumes/ (including speed if you are running Docker Desktop on windows/mac) for why volumes are preferred.