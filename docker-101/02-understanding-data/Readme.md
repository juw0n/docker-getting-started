<!-- experiment with how installing something into a container at runtime behaves! -->

# Create a container from the ubuntu image
<!-- the --interactive --tty make the container interactive often used together to run a container in interactive mode, allowing you to interact with the command line inside the container, the --rm remove the container upn exit-->
docker run --interactive --tty --rm ubuntu:22.04

# Try to ping google.com
ping 8.8.8.8 -c 1 # This results in `bash: ping: command not found`

# Install ping
apt update
apt install iputils-ping --yes

ping 8.8.8.8 -c 1 # This time it succeeds!
exit

# Let's try that again:
docker run -it --rm ubuntu:22.04
ping google.com -c 1 # It fails!

It fails the second time because we installed it into that read/write layer specific to the first container, and when we tried again it was a separate container with a separate read/write layer!