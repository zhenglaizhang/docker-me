# Docker Tutorials

> #### Feedback and Pull Request are always welcome!

## Setup

### Linux


### Mac

```bash
brew cask install dockertoolbox
```

Run `Docker Quickstart Terminal`



### Commands

```bash

# check docker
docker version

# create dev machine with VB (or Azure/AWS)
docker-machine ls
docker-machine create --driver virtualbox dev
docker-machine ls

# switch to default machine 
eval "$(docker-machine env dev)"


echo "$(docker-machine env dev)"
export DOCKER_TLS_VERIFY="1"
export DOCKER_HOST="tcp://192.168.99.101:2376"
export DOCKER_CERT_PATH="/Users/BX/.docker/machine/machines/dev"
export DOCKER_MACHINE_NAME="dev"
# Run this command to configure your shell: 
# eval $(docker-machine env dev)



# check containers
docker ps

docker run -p 8080:8080 -d rickfast/hello-oreilly-http
docker-machine ip dev
```


## Docker Containers

```bash
docker run ubuntu:latest pwd
docker run -i -t ubuntu /bin/bash

# run a command in a running container
# run the /bin/bash as the secondary cmd in the redis running container
docker exec -i -t redis /bin/bash
# in the container
ps -A

# show running containers
docker ps

# show even stopped containers
docker ps -a
 
# show the only one last started container
docker ps -l

# show the interactive logs of one daemon
docker start -d -p 6379:6379 --name redis redis
docker logs redis
docker logs -f redis

# inspect the container with large json payload 
docker inspect redis
docker inspect --format='{{.NetworkSettings.IPAddress}}' redis


# docker run Run a command in a new container
# docker start Start one or more stopped containers
```

## Docker lifecycle

start
stop
kill
restart
rm

how to handle signal


* `--restart unless-stopped`, docker starts container when the container is stopped, we can only kill the container to stop it

## Docker Images
Real power of docker, build and share the images, packaging versioned applications, and make sure they are extremely portable 

Docker only runs natively on Linux machines, our apps can only link against linux binaries and libraries.
 

Docker image contains immutable layers like git commits, we can name or tag(version) layers:
 
	Base image => Python installed => Flask framework => Our app code (@app.route("/") => :5000

### Docker file

```dockfile
FROM Ubuntu:15.10
RUN apt-get install python
RUN pip install flask
ADD app.py
EXPOSE 5000

ENTRYPOINT python app.py 
```

### Docker hub

Search hub.docker.com for any available docker image

```bash
docker pull rabbitmq # for latest tag
docker run rabbitmq

docker pull rabbitmq:3.6.0-management
# This download will be much faster as it will reuse the first downloaded rabbitmq image layers 
docker run -d -p 15672:15672 rabbitmq:3.6.0-management
# goto {ip}:15672 to check the admin UI
```

### Build our image

* Choose the **good base image** based on our app requirement
* alpine is the lightweight linux for lightweight containers
    
    `docker search alpine`

2 ways to build our image

1. From container
2. Docker file
    * prefered, versioned and reproduced
    * hide internal entry point and details
    * easily built

2 ways to specify the default executable

* command
    * Allows the docker to override the command
* entrypoint
    * Runs directly the entrypoint instruction without shell

base triggers (ONBUILD)

```bash
# show images available locally
docker images

docker pull alpine
```


## Networks

```bash
docker network ls
```

* bridge
    * default network (*docker0*)
    * any container with this network cannot be accessed outside the docker host unless the port is mapped to the docker host with the `-d' parameter in `docker run`
* host
    * docker host uses it
    * bound to docker host directly, no need to specify the port mapping
    * less isolation and port may conflict (second container using the same port will fail to start)
    * `ifconfig` shows the container has the same net interface with the host
* none
    * just nothing
    * cannot access the container outside it
* overlay
    * docker swarm
    * let docker containers can discover other containers accross multiple hosts

We can create new network to enforce the network isolcation, and create isolated app containers on the same machine.

But with this, we cannot use the deprecated linking functionality.

## Data
We usually use docker as container for stateless service with not persitent data storage. 

Stateless service are scalable and portable. Stateful service like database can also run in docker cotnainers as well, but they need other considerations.

* persistent state cannot be stored in container as containers can and will go. The data inside the container will be lost
* We shall use **data volumes**.

Docker file sytems: UniFS, unified file system. The file system is layered. Data volumes is directory that doesnt use UniFS and allows container to preseve the data. Volumes can also be mapped to the directory on the host machine.
 
Two ways to store data in containers:

* shared folder mapped between hosts and containers
* data volume containers - docker containers with no app but just volumes that's shared, it doesn't run, and it not a process.

### Multihost data sharing?


## Docker-Compose
Group and manage related docker containers.

ctrl+c to send signal to all the containers and stop the compose.

```bash
docker-compose up # -d
# ctrl + c
docker-compose stop
docker-compose rm
```

#### ELK stack
powerful debugging and monitoring framework

#### Redis + Web
Simple, sharable, reproduceable dev environment.


## Docker Swarm

* Run containers across multiple docker hosts
* PROD environment


## Kitematic
DockerHub desktop version.

## Log driver
We could use logstash, splunk, AWS log driver or other basic log drivers for docker logging.

## Private registry
We can easily setup the private docker private registry, it supports multiple storage backend, like volumne containers in the host machine, Amazon S3 or Azure blob storage.

It works great for CD pipleline.

## Commit running container

https://github.com/dqminh/docker-flatten

I am in a similar situation. I am thinking about not using a dedicated data volume container instead committing regularly to have some kind of incremental backup. Beside the incremental backup the big benefit is for a team developing approach. As newcomer you can simply docker pull a database image already containing all the data you need to run, debug and develop.

So what I do right now is to pause before commit:
	
	docker pause happy_feynman; docker commit happy_feynman odev:`date +%s`

As far as I can tell I have no problems right now. But this is a developing machine so no I have no experience on heavy load servers.


## Docker machine

```bash
docker-machine create -d virtualbox --virtualbox-memory 4096 default


docker-machine stop
VBoxManage modifyvm default --cpus 2
VBoxManage modifyvm default --memory 4096
docker-machine start
```

Docker Machine maintainer here. I don't think adjusting the config.json manually will work.

Your two options are to either create the machine with --virtualbox-memory set, or to adjust the VM's memory in the VirtualBox GUI ("Preferences > Memory" for that VM I think). Make sure the machine is powered off and there should be a little slider that works.

That's true, in order to have docker-machine inspect report the accurate amount, you do have to edit config.json (that's where inspect gets its information)

## Scheduling and supervision

## Monitoring and logging

## Service discovery

## Continuous Delivery

## Security

How to store sensitive data
Rely heavily on environment variables or mounts to configure.

## Q & A

* docker start vs docker restart?
* ctrl+c to gracefuly shutdown the container
* 
