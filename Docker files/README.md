#DigitalFitness Docker files exemple
This repository contains the Dockerfile, docker-compose.yml, and docker-compose.override.yml files for the DigitalFitness project.

##Dockerfile
The Dockerfile is used to build a Docker image for the DigitalFitness application announcement microservice. It contains instructions for the Docker engine on how to build the image and what dependencies to include. To build the Docker image, use the following command:

```shell
docker build -t my-app:latest .
```
##Docker Compose
Docker Compose is a tool for defining and running multi-container Docker applications. It allows you to manage your application's services, networks, and volumes in a declarative YAML file. In this repository, you will find a docker-compose.yml file that defines the services and their configurations.

To start the services defined in the docker-compose.yml file, execute the following command:
```shell
docker-compose up
```
##docker-compose.override
The docker-compose.override file is an optional file that allows you to extend or override the services and configurations defined in the base `docker-compose.yml` file. It is useful for managing environment-specific settings or adding additional services for development, testing, or production.

To use the docker-compose.override file, simply create a file named `docker-compose.override.yml` in the same directory as the `docker-compose.yml` file. Add your additional services or override configurations specific to your environment.

To start the services using the docker-compose.override file, execute the following command:
```shell
docker-compose -f docker-compose.yml -f docker-compose.override.yml up -d
```
###Note: 
The `-f` flag is used to specify multiple Compose files to be used together.
The `-d` option stands for "detached mode," which means the containers will run in the background. This option allows you to continue using the terminal while the containers are running.
