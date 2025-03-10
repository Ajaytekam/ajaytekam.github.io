---   
title: Docker Containers Basic Notes for Beginners 
author: ajaytekam   
date: 2023-08-06 20:00:00 +0530   
img_path: /assets/posts/20230806/ 
categories: [DevOps, Docker, microservices]    
tags: [devops, docker, containerization, microservices]  
image:
  path: docker.png 
---    

## Contents 

- [Docker Introduction](#docker-introduction)     
    - [Docker](#docker)  
    - [Containers](#containers)   
    - [Containerization](#containerization)   
    - [Docker Architecture](#docker-architecture)   
    - [Docker Image](#docker-image)   
    - [Dockerfile](#dockerfile)
- [Basic command usages](#basic-command-usages)
    - [Show Information](#show-information)   
    - [Managing Containers](#managing-containers)   
    - [Managing Images](#managing-images)   
    - [Defference between image and container](#defference-between-image-and-container)  
    - [List images](#list-images)   
    - [List containers](#list-containers)  
    - [Pulling/Downloading Images from dockerhub](#pullingdownloading-images-from-dockerhub)   
    - [Starting the nginx server](#starting-the-nginx-server)    
    - [Creating container within image file directly](#creating-container-within-image-file-directly)    
    - [Running container Background](#running-container-background)    
    - [Automatically start container at startup](#automatically-start-container-at-startup)  
    - [Delete/Rename Container](#deleterename-container)    
    - [Deleteing all container at once](#deleteing-all-container-at-once)  
    - [Delete/Rename images](#deleterename-images)  
    - [Getting a Bash shell on running container](#getting-a-bash-shell-on-running-container)  
    - [Mapping local directory into running container's directory](#mapping-local-directory-into-running-containers-directory)  
    - [Pushing docker image into dockerhub](#pushing-docker-image-into-dockerhub)   
    - [Creating docker image from an Updated/Customized Container (known as commiting)](#creating-docker-image-from-an-updatedcustomized-container-known-as-commiting)  
    - [Trasnfering Images offline from one machine to another](#trasnfering-images-offline-from-one-machine-to-another)   
    - [Inspecting Docker Container Steps](#inspecting-docker-container-steps)   
    - [Some Docker Internals](#some-docker-internals)   
- [Docker Mounts](#docker-mounts)  
    - [Difference between docker mounts and volumes](#difference-between-docker-mounts-and-volumes)   
- [Docker Inspect and logs](#docker-inspect-and-logs)  
- [Docker Networking](#docker-networking)    

## Docker Introduction   

### Docker

Docker is an open-source containerization platform, used for  building, deploying, and running  applications, using lightweight, portable containers.   

### Containers   

A container is a standard unit of software bundled with dependencies so that applications can be deployed fast and reliably between different computing platforms.  

__Features of containers :__ 

* Docker containers consist of applications and all their dependencies.   
* They share the kernel and system resources with other containers and run as isolated systems in the host operating system.  
* The main aim of docker containers is to get rid of the infrastructure dependency while deploying and running applications. This means that any containerized application can run on any platform irrespective of the infrastructure being used beneath.  
* Applications are safer in containers and Docker provides the strongest default isolation capabilities in the industry.   
* Technically, they are just the runtime instances of docker images.

### Containerization 

* Containerization is a type of Virtualization which brings virtualization to the operating system level. 
* While Virtualization brings abstraction to the hardware, Containerization brings abstraction to the operating system. 

### Docker Architecture

Docker Architecture consists of a Docker Engine which is a client-server application with three major components:   

![](docker1.png)  

* Docker Daemon: A persistent background process that manages Docker images, containers, networks, and storage volumes. The Docker daemon constantly listens for Docker API requests and processes them.  
* Docker Engine REST API: An API used by applications to interact with the Docker daemon; it can be accessed by an HTTP client.  
* Docker CLI: A CLI client for interacting with the Docker daemon. It greatly simplifies how you manage container instances and is one of the key reasons why developers love using Docker.  

### Docker Image

* They are executable packages bundled with application code & dependencies, software packages etc. for the purpose of creating containers.   
* Docker images can be deployed to any docker environment and the containers can be spun up there to run the application.   

### DockerFile 

It is a text file that contains all commands which needs to be run to build an image.

## Docker Basic Commands 

### Basic command usages

* `docker images` : Lists images locally 
* `docker run` : command to create a new container 
* `docker ps` : Lists running container   
* `docker ps -a` : Lists all the containers 
* `docker exec` : executes commands on containers
* `docker start/stop/restart/rm` 
* `docker rmi` : Removes docker images
* `docker inspect` : Details of container and image

### Show information

```bash  
$ docker info
```
or
```bash  
$ docker info | less
```

### Managing Containers 

```bash    
$ docker container <command>
```

### Managing Images 

```bash  
$ docker image <command>
```

### Defference between image and container

> Docker Image is a set of files which has no state, whereas Docker Container is the instantiation of Docker Image. In other words, Docker Container is the run time instance of images.

or

> In other words by using an object-oriented programming analogy, the difference between a Docker image and a Docker container is the same as that of the difference between a class and an object. An object is the runtime instance of a class. Similarly, a container is the runtime instance of an image.


### List images 

```bash  
$ docker image ls
```

or 

```bash  
$ docker image ls -a
```

### List containers
 
List only running containers  

```bash  
$ docker container ls
```

or 
```bash   
$ docker ps
```

List all containers
```bash  
$ docker container ls -a
```

### Pulling/Downloading Images from dockerhub

> dockerhub is like github repository for docker images

```bash   
$ docker pull <image_name>
```

Example : we are going to download nginx image

```bash  
$ docker pull php
```

### starting the nginx server 

```bash   
$ docker run -it -p 80:80 nginx
```

The above command first create a container of nginx image and run it. The options are :

* run : to run the image
* -it : In interactive mode
* -p 80:80 : where first port 80 means the nginx serve on port 80 at local system and second port 80 containers port 80 in whcih nginx run. We can access nginx server on

```
http://localhost:80
```

To stop it press Ctrl + C and it will stop.

> Note : The above command will also create a container, now next time we can directly run the container by below command

```bash   
$ docker container <start|stop|pause|kill> <Container_Name or Container_ID>
```

> Note that at here we can not use 'run' command for already created container, (it is only used to run image file {which create container})


### Creating container within image file directly

```bash   
$ docker container run -it -p 80:80 --name Mynginx nginx
```

where '--name' followed with 'Mynginx' create a container named 'Mynginx' by using image 'nginx'

### Running container Background 

```bash  
$ docker container run -d -p 8080:80 --name BKnginx nginx
```

* where '-d' option means detach

it can be accessble at 

```
http://localhost:8080
```
Now to stop of pause container we can use the command :

```bash   
$ docker container stop|pause BKnginx
```

where BKnginx is nothing but name of the container.

### Automatically start container at startup 

Do it when creating container   

```bash       
$ docker container run -d -p 8080:80 --name BKnginx nginx --restart=always  
```  

Do it on a alreay created container   

```bash     
$ docker update --restart=always 0576df221c0b
```  

### Delete/Rename Container

```bash  
$ docker container rm <Container_ID/Container_Name>
```

```bash  
$ docker container rename <Container_ID/Container_Name>
```

to remove a running container use '-f' option

 
```bash   
$ docker container rm <Container_ID/Container_Name> -f
```

### Deleteing all container at once 

```bash  
$ docker rm $(docker ps -aq) -f
```

### Delete/Rename images 

```bash  
$ docker image rm <Image_ID/Image_Name>
```

```bash        
$ docker image rename <Image_ID/Image_Name>
```

### Getting a Bash shell on running container

```bash  
$ docker container exec -it <Container_NAME_or_ID> bash
```

### Mapping local directory into running container's directory 

for example the Document root directory of nginx server is '/usr/share/nginx/html'. Now we can map our local directory into nginx Document root directory. But we have to do that at the time of creation of container

```bash   
$ docker container run -d -p 8080:80 -v <local_directory>:<container_directory> <image_name_or_id>
```

Example

```bash  
$ docker container run -d -p 8080:80 -v $(pwd):/usr/share/nginx/html --name nginx-website nginx
```

Now if we create any file in current directory '$(pwd)', then we can access it with nginx server

For exmaple :

```
http://localhost:8080/test.html
```

### Pushing docker image into dockerhub

first login to your docker account by below command and give your username and password 

```bash  
$ docker login
```

then run below command :

```bash  
$ docker push <image_name>
```

### Creating docker image from an Updated/Customized Container (known as commiting) 

```bash
$ docker commit <Container_Name> [NEW_IMAGE_NAME[:TAG]]
```

Example :

```bash
$ docker commit ubuntu101 ajay/ubuntu-updated:version1
```

Above command will create a new image named 'ajay/ubuntu-updated:version1' and to create a container from that image use :

```bash 
$ docker run -it --name=UpdatedUbuntu ajay/ubuntu-updated:version1
```

Note we have to put the full name of image with tag otherwise docker will not recognize it.

More OPTIONS related to commit command can be found here : [LINK](https://docs.docker.com/engine/reference/commandline/commit/)

### Trasnfering Images offline from one machine to another 

Creating an Image file :

```bash
$ docker save -o image_file_name.docker ubuntu
```

After transfering the file offline from one machine to another, run below command on the destination machine :

```bash
$ docker load -i image_file_name.docker
```

### Inspecting Docker Container Steps 

Inspecting exposed ports of a docker container   

```bash
docker inspect --format="{{json .}}" Container_Name | jq '.Config.ExposedPorts'
```

### Some Docker Internals 

* The running containers internals can be found at `/var/lib/docker/containers/`     

## Docker Mounts 

Docker has two options for containers to  store files in the hosy machine 

* Bind Mounts : Stored anywhere on the host system, example :  

```  
-v HostDIR:DockerDIR
```  

```   
$ mkdir mysqlData   
$ docker pull mysql   
$ docker run --rm -it --name mydb -p3306:3306 -e MYSQL_ROOT_PASSWORD=dbaccess -v /home/centos/mysqlData:/var/lib/mysql mysql   
```  
* Volumes : 
  * Docker volumes are file systems mounted on Docker containers to preserve data generated by the running container. The default location of volume is `/var/lib/docker/volumes` on linux.  
  * Volumes are created on the host machine and managed by Docker. Containers can read and write data to the volume, and the data will persist even if the container is deleted or recreated.

__difference between docker mounts and volumes :__   

* Volumes are more portable and scalable than mounts, as they can be used to share data between containers running on different hosts or cloud providers. Volumes can also be backed up and managed more easily by Docker.   
* Docker mounts are simpler and faster to set up, but are less portable and scalable than volumes.  

```   
Usage:  docker volume COMMAND

Manage volumes

Commands:
  create      Create a volume
  inspect     Display detailed information on one or more volumes
  ls          List volumes
  prune       Remove all unused local volumes
  rm          Remove one or more volumes
```   

Example :  

```    
docker volume create MYSQL 
$ docker run --rm -it --name mydb -p3306:3306 -e MYSQL_ROOT_PASSWORD=dbaccess -v MYSQL:/var/lib/mysql mysql   
```    

Another way to mount volumes 

```
$ docker run --rm -it --name mydb -p3306:3306 -e MYSQL_ROOT_PASSWORD=dbaccess -v --mount source=MYSQL,target=/var/lib/mysql mysql   
```
inspect docker volume 

```   
$ docker volume inspect MYSQL

[
    {
        "CreatedAt": "2022-12-10T08:42:48Z",
        "Driver": "local",
        "Labels": {},
        "Mountpoint": "/var/lib/docker/volumes/MYSQL/_data",
        "Name": "MYSQL",
        "Options": {},
        "Scope": "local"
    }
]
```   

## Docker inspect and logs

`inspect`: Docker inspect command returns all the details about an image or a container.  

```    
docker inspect <image/container_name_or_id> 
```  

`logs` : Shows logs of a container.  

```   
docker logs containe_name__OR_id
```

### Some Docker Internals 

* The running containers internals can be found at `/var/lib/docker/containers/`     

## Docker Mounts 

Docker has two options for containers to  store files in the hosy machine 

* Bind Mounts : Stored anywhere on the host system, example :  

```bash   
-v HostDIR:DockerDIR
```  

```bash   
$ mkdir mysqlData   
$ docker pull mysql   
$ docker run --rm -it --name mydb -p3306:3306 -e MYSQL_ROOT_PASSWORD=dbaccess -v /home/centos/mysqlData:/var/lib/mysql mysql   
```  

* Volumes : 
  * Docker volumes are file systems mounted on Docker containers to preserve data generated by the running container. The default location of volume is `/var/lib/docker/volumes` on linux.  
  * Volumes are created on the host machine and managed by Docker. Containers can read and write data to the volume, and the data will persist even if the container is deleted or recreated.

```bash   
Usage:  docker volume COMMAND

Manage volumes

Commands:
  create      Create a volume
  inspect     Display detailed information on one or more volumes
  ls          List volumes
  prune       Remove all unused local volumes
  rm          Remove one or more volumes
```   

Example :  

```bash    
docker volume create MYSQL 
$ docker run --rm -it --name mydb -p3306:3306 -e MYSQL_ROOT_PASSWORD=dbaccess -v MYSQL:/var/lib/mysql mysql   
```    

Another way to mount volumes 

```bash   
$ docker run --rm -it --name mydb -p3306:3306 -e MYSQL_ROOT_PASSWORD=dbaccess -v --mount source=MYSQL,target=/var/lib/mysql mysql   
```  

inspect docker volume 

```bash     
$ docker volume inspect MYSQL

[
    {
        "CreatedAt": "2022-12-10T08:42:48Z",
        "Driver": "local",
        "Labels": {},
        "Mountpoint": "/var/lib/docker/volumes/MYSQL/_data",
        "Name": "MYSQL",
        "Options": {},
        "Scope": "local"
    }
]
```   

### Difference between docker mounts and volumes   

* Volumes are more portable and scalable than mounts, as they can be used to share data between containers running on different hosts or cloud providers. Volumes can also be backed up and managed more easily by Docker.   
* Docker mounts are simpler and faster to set up, but are less portable and scalable than volumes.  

## Docker inspect and logs

`inspect`: Docker inspect command returns all the details about an image or a container.  

```bash      
docker inspect <image/container_name_or_id> 
```  

`logs` : Shows logs of a container.  

```bash    
docker logs containe_name__OR_id
```  

## Docker networking   

* By default docker container used bridge network mode `docker0`

```bash   
docker0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.17.0.1  netmask 255.255.0.0  broadcast 172.17.255.255
        ether 02:42:2c:1f:cc:7e  txqueuelen 0  (Ethernet)
        RX packets 158  bytes 8943 (8.9 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 148  bytes 715325 (715.3 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

* List docker network 

```bash  
$ docker network ls 
```
 
You can also create another network to isolate containers from each other for example creating a new network driver for another container

Command: `docker network create <network-interface>`  


```bash   
$ docker network create secure-network 
// create a new container with the new network driver/interface
$ docker run --rm -d -p 8082:80 --name=webserver03 --network=secure-network nginx/v1   
```

Now if we check the ip of newly created container the it is different 

```bash   
$ docker exec -it webserver03 bash

root@98fd5aa4281b:/# ifconfig
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.18.0.2  netmask 255.255.0.0  broadcast 172.18.255.255
        ether 02:42:ac:12:00:02  txqueuelen 0  (Ethernet)
        RX packets 83  bytes 586507 (572.7 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 86  bytes 5756 (5.6 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```   

In the host system 

```bash   
br-bec48c6e1751: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.18.0.1  netmask 255.255.0.0  broadcast 172.18.255.255
        ether 02:42:d0:b6:85:b4  txqueuelen 0  (Ethernet)
        RX packets 86  bytes 4552 (4.5 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 83  bytes 586507 (586.5 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```   

The container created with `secure-network` (br-bec48c6e1751) network interface is not able to accessible from container with docker0 interface.   

More detailed video on docker networking : https://www.youtube.com/watch?v=OU6xOM0SE4o   
