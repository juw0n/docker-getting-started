## Containerized Go app using Docker.

### Learning Objectives:
* Create a Dockerfile which contains the instructions for building a container image for a program written in Go.
* Run the image as a container on local Docker instance and manage the container's lifecycle.
* Use multi-stage builds for building small images efficiently while keeping Dockerfiles easy to read and maintain.
* Use Docker Compose to orchestrate running of multiple related containers together in a development environment.
* Configure a CI/CD pipeline for your application using GitHub Actions
* Deploy your containerized Go application.

This project is a simple representation of a microservice, intentionally kept basic to help concentrate on learning the fundamentals of containerization for Go applications.

The application offers two HTTP endpoints:
* It responds with a string containing a heart symbol (<3) to requests to /.
* It responds with {"Status" : "OK"} JSON to a request to /health.
* It responds with HTTP error 404 to any other request.

The app is stateless and listen on TCp port defined by the value of an environmental variable.

** The docker build command creates Docker images from the Dockerfile and a context. A build context is the set of files located in the specified path or URL.

Rem: A container is a normal operating system process except that this process is isolated and has its own file system, its own networking, and its own isolated process tree separate from the host.