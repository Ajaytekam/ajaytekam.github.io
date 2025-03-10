---   
title: Building Docker Images with Dockerfile
author: ajaytekam   
date: 2023-08-06 20:05:00 +0530   
img_path: /assets/posts/20230806/ 
categories: [DevOps, Docker, microservices]    
tags: [DevOps, docker, containerization, microservices]  
image:
  path: dockerfile.png   
---    

## Building Docker image with Dockerfile   

Dockerfile Instructions :  

* `FROM` : Specify Base image.   
* `LABELS` : Adds metadata to an image.     
* `RUN` : Executes commands in a new layer and commit the results.  
* `COPY` : Copy files from host file system into a container image.  
* `ADD` : Copy files from host to container, but it can also support unpacking of tar archieve files. You can also copy remote files into container.    
* `CMD` : Runs binaries/commands on docker run.   
* `ENTRYPOINT` : Allows to configure a container that will run as an executable. Means the command is an entrypoint, user can pass the argument to that entrypoint command. In entrypoint the user has to control argument, means user can provide argument during the container creation/initiation and in CMD you have to hardcode arguments.       
* `VOLUME` : Creates a mount point and marks it as holding externally mounted volumes.   
* `EXPOSE` : Container listens on the specified network ports at runtime.  
* `ENV` : Sets the environment variable.  
* `USER` : Sets the username (or UID).   
* `WORKDIR` : Sets the working directory.    
* `ARG` : Defines a variable that users can pass at build-time.    
* `ONBUILD` : Adds to the image a trigger instruction to be executed at a later time.    

An example of Dockerfile  

```bash    
FROM ubuntu:latest
LABEL "Author"="Ajay"
LABEL "Project"="Company-Website"
ENV DEBIAN_FRONTEND=noninteractive
RUN apt update && apt install wget unzip -y
RUN apt install apache2 -y
CMD ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]
EXPOSE 80
WORKDIR /var/www/html
VOLUME /var/log/apache2
RUN wget https://github.com/securebitlabs/securebitlabs.github.io/archive/refs/heads/main.zip -O main.zip && unzip main.zip && mv securebitlabs.github.io-main/* . &&  rm -rf securebitlabs.github.io-main main.zip
```   

Command :

```bash    
$ docker image build -t myserver:v1 .
```   

where 'myserver:v1' represents the name of newly created docker image also make sure it needs to be in small letters only and just after that '.' represents the localtion of 'Dockerfile' which is in this case the current directory.  

Now building a container from image 

```bash      
docker container run -d --rm --name MySrv -p 8888:80 myserver:v1
```   

Now you can access the website at `localhost:8888`   

__Push into Dockerhub__  

* Login to dockerhub `docker login` and provide username and password.  
* Now push the image `docker push <username>/<imagename>:<version>`  

Example :

```bash   
docker push ajaytekam/myserver:v1 
```  

## ENTRYPOINT and CMD uses  

* `CMD` : Executes given command at the start of docker container.  

Dockerfile 

```bash  
FROM ubuntu:latest
RUN apt update -y && apt install iputils-ping -y
CMD ["ping", "-c4", "google.com"]
```

Usecases :

```bash  
$ docker build -t cmdtest:v1 .
$ docker run --rm cmdtest:v1

PING google.com (142.250.199.174) 56(84) bytes of data.
64 bytes from bom07s37-in-f14.1e100.net (142.250.199.174): icmp_seq=1 ttl=50 time=1.22 ms
64 bytes from bom07s37-in-f14.1e100.net (142.250.199.174): icmp_seq=2 ttl=50 time=1.23 ms
64 bytes from bom07s37-in-f14.1e100.net (142.250.199.174): icmp_seq=3 ttl=50 time=1.24 ms
64 bytes from bom07s37-in-f14.1e100.net (142.250.199.174): icmp_seq=4 ttl=50 time=1.22 ms

--- google.com ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3005ms
rtt min/avg/max/mdev = 1.216/1.227/1.241/0.009 ms
```   

* `ENTRYPOINT` : The command is an entrypoint, means you can pass the argument to the entrypoint command. In entrypoint the user have to control arguments and in cmd you have to hardcode the command argument.  

Dockerfile 

```bash  
FROM ubuntu:latest
RUN apt update -y && apt install iputils-ping -y
ENTRYPOINT ["ping"]
```

Usecases : 

```bash  
$ docker build -t entrytest:v1 .
$ docker run --rm entrytest:v1 -c4 facebook.com

PING facebook.com (157.240.16.35) 56(84) bytes of data.
64 bytes from edge-star-mini-shv-01-bom1.facebook.com (157.240.16.35): icmp_seq=1 ttl=48 time=0.685 ms
64 bytes from edge-star-mini-shv-01-bom1.facebook.com (157.240.16.35): icmp_seq=2 ttl=48 time=0.747 ms
64 bytes from edge-star-mini-shv-01-bom1.facebook.com (157.240.16.35): icmp_seq=3 ttl=48 time=0.772 ms
64 bytes from edge-star-mini-shv-01-bom1.facebook.com (157.240.16.35): icmp_seq=4 ttl=48 time=0.787 ms

--- facebook.com ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3005ms
rtt min/avg/max/mdev = 0.685/0.747/0.787/0.038 ms
```  
 
* `ENTRYPOINT with default argument` : You can also provide default argument with entrypoint using cmd. 

Dockerfile 

```bash     
FROM ubuntu:latest
RUN apt update -y && apt install iputils-ping -y
ENTRYPOINT ["ping"]
CMD ["-c4", "google.com"]
```  

Usecases :  

```bash  
$ docker build -t entrytest:v3 .
$ docker run --rm entrytest:v3   

PING google.com (142.250.66.14) 56(84) bytes of data.
64 bytes from bom07s35-in-f14.1e100.net (142.250.66.14): icmp_seq=1 ttl=50 time=1.60 ms
64 bytes from bom07s35-in-f14.1e100.net (142.250.66.14): icmp_seq=2 ttl=50 time=1.68 ms
64 bytes from bom07s35-in-f14.1e100.net (142.250.66.14): icmp_seq=3 ttl=50 time=1.66 ms
64 bytes from bom07s35-in-f14.1e100.net (142.250.66.14): icmp_seq=4 ttl=50 time=1.64 ms

--- google.com ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3005ms
rtt min/avg/max/mdev = 1.595/1.644/1.679/0.031 ms
```

As we can see without passing argument it will ping cmd, now we can also pass the argument  

```bash 
$ docker run --rm entrytest:v3 -c4 facebook.com

PING facebook.com (157.240.16.35) 56(84) bytes of data.
64 bytes from edge-star-mini-shv-01-bom1.facebook.com (157.240.16.35): icmp_seq=1 ttl=48 time=0.715 ms
64 bytes from edge-star-mini-shv-01-bom1.facebook.com (157.240.16.35): icmp_seq=2 ttl=48 time=0.738 ms
64 bytes from edge-star-mini-shv-01-bom1.facebook.com (157.240.16.35): icmp_seq=3 ttl=48 time=0.749 ms
64 bytes from edge-star-mini-shv-01-bom1.facebook.com (157.240.16.35): icmp_seq=4 ttl=48 time=0.759 ms

--- facebook.com ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3005ms
rtt min/avg/max/mdev = 0.715/0.740/0.759/0.016 ms
```  

## Multistage Docker 

* Multiple-builds uses FROM statements in Dockerfile.  
* Each FROM instruction can use a different base, and each of them begins a new stage of the build.   
* You can selectively copy artifacts from one stage to another, leaving behind everything you don’t want in the final image.  

```   
FROM golang:1.16
WORKDIR /go/src/github.com/alexellis/href-counter/
RUN go get -d -v golang.org/x/net/html  
COPY app.go ./
RUN CGO_ENABLED=0 go build -a -installsuffix cgo -o app .

FROM alpine:latest  
RUN apk --no-cache add ca-certificates
WORKDIR /root/
COPY --from=0 /go/src/github.com/alexellis/href-counter/app ./
CMD ["./app"]
```  


For more details look at here [Multistage-Dockerfile](https://docs.docker.com/build/building/multi-stage/)
