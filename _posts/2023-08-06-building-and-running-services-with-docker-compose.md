---   
title: Building and Running Services with Docker Compose
author: ajaytekam   
date: 2023-08-06 20:10:00 +0530   
img_path: /assets/posts/20230806/ 
categories: [DevOps, Docker, microservices]    
tags: [devOps, docker, containerization, microservices]  
image:
  path: docker-compose.png
---    

## Docker Compose  

* docker-compsoe is used for defining and running multi-container Docker applications.    
* docker-compose uses `docker-compose.yaml` file to configure application’s services, and after with a single command, you create and start all the services from your configuration.  
* docker-compose can be used in all environment, for example in production, staging, development, testing, as well as CI workflows.  
* Some of features are : 
  * Start, stop, and rebuild services  
  * View the status of running services  
  * Stream the log output of running services  
  * Run a one-off command on a service  

## Docker Compose Directive :  

There are alot of configuration (key-value pairs and directives) in docker-compose file. The top Levels are :   

```   
Version
Services
Network
Volumes
```  

* Now lets look at the example of docker-compose.yml file 

```yml    
version: "3.8"
services:
    client:
        build:
            context: ./client
        ports:
            - "4200:4200"
        container_name: client
        depends_on:
            [api, webapi]

    api:
        build:
            context: ./nodeapi
        ports:
            - "5000:5000"
        container_name: api
        depends_on:
            - nginx
        depends_on:
            [emongo]

    webapi:
        build:
            context: ./javaapi
        ports:
            - "9000:9000"
        restart: always
        container_name: webapi
        depends_on:
            [emartdb]

    nginx:
        restart: always
        image: nginx:latest
        container_name: nginx
        volumes:
            - "./nginx/default.conf:/etc/nginx/conf.d/default.conf"
        ports:
            - "80:80"
        command: ['nginx-debug', '-g', 'daemon off;']
        depends_on:
            [client]

    emongo:
       image: mongo:4
       container_name: emongo
       environment:
         - MONGO_INITDB_DATABASE=epoc
       ports:
         - "27017:27017"

    emartdb:
      image: mysql:5.7
      container_name: emartdb
      ports:
         - "3306:3306"
      environment:
        - MYSQL_ROOT_PASSWORD=emartdbpass     
        - MYSQL_DATABASE=books
```  

Another example : 

```yml   
version: '3'
services:
  app:
    image: node:latest
    container_name: app_main
    restart: always
    command: sh -c "yarn install && yarn start"
    ports:
      - 8000:8000
    working_dir: /app
    volumes:
      - ./coderepo:/app
    environment:
      MYSQL_HOST: localhost
      MYSQL_USER: root
      MYSQL_PASSWORD: 
      MYSQL_DB: test
  mongo:
    image: mongo
    container_name: app_mongo
    restart: always
    ports:
      - 27017:27017
    volumes:
      - mongodb:/data/db
volumes:
  mongodb:
```

Now lets see the details about various options/configurations 

*  `version` : Refers to the docker-compose version. The latest version is 3.8 (as of now).   

```yml  
version: "3.8"
```

* `services` : Defines the services that we need to run

```yml  
services: 
```

* `service_name` : it is a custom name for one of your containers, for example app, mongo etc.  

```yml  
version: "3.8"
services: 
  service_one:
    service_declaration....
    ...
    ...
  service_two:
    service_declaration....
    ...
    ...
```  

* `image` : The image which we have to pull. Here we are using node:latest and mongo.  

```yml  
version: "3.8"
services: 
  service_one:
    image: node:latest
```

* `container_name` : Name for each container.   

```yml  
version: "3.8"
services: 
  service_one:
    image: node:latest
    container_name: service_one
```

often the container name would be similer to the service name. 

* `restart` : starts/restarts a service container.  

```yml  
version: "3.8"
services: 
  service_one:
    image: node:latest
    container_name: service_one
    restart: always
```

* `port` : Defines the custom port to run the container.  

```yml  
version: "3.8"
services: 
  service_one:
    image: node:latest
    container_name: service_one
    restart: always
    ports:
      - 8000:8000
```

* `working_dir` : The current working directory for the service container.   

```yml  
version: "3.8"
services: 
  service_one:
    image: node:latest
    container_name: service_one
    restart: always
    ports:
      - 8000:8000
    working_dir: /app
```

* `environment` : Defines the environment variables, such as DB credentials, and so on.  

```yml  
version: "3.8"
services: 
  service_one:
    image: node:latest
    container_name: service_one
    restart: always
    ports:
      - 8000:8000
    working_dir: /app
    environment:
      - MYSQL_ROOT_PASSWORD=emartdbpass     
      - MYSQL_DATABASE=books
```  

* `command` : Run the command at the container startup. Command provided here will overide the dockerfile command as well as docker image default command. 

```yml  
version: "3.8"
services: 
  service_one:
    image: node:latest
    container_name: service_one
    restart: always
    ports:
      - 8000:8000
    working_dir: /app
    environment:
      - MYSQL_ROOT_PASSWORD=emartdbpass     
      - MYSQL_DATABASE=books
    command: sh -c "yarn install && yarn start"
```  

* `volumes` : Used to mount a docker volume or a host file directory into docker container.   

Mounting a host directory into docker container 

```yml  
version: "3.8"
services: 
  service_one:
    image: node:latest
    container_name: service_one
    restart: always
    ports:
      - 8000:8000
    volumes:
      - ./coderepo:/app
```

Mounting a docker volume into docker container 

```yml  
version: "3.8"
services: 
  service_one:
    image: node:latest
    container_name: service_one
    restart: always
    ports:
      - 8000:8000
    volumes:
      - ~/mongo:/data/db
volumes:
  mongodb:
```

Also at the last line you need to define the mongodb volume, if you are going to mount the volume.  

* `build` : Used to build a Docker image from a Dockerfile and then use that image to start a container.  
  * `context` : specifies the build context, which is the directory where the build process is executed.  
  * `dockerfile` : specifies the name of the Dockerfile to use for the build process.

```yml     
version: '3.8'
services:
  my_service:
    build: 
      context: .
      dockerfile: Dockerfile
    image: my_image
```

* `depends_on` : specify the order in which services are started. Specifically, it defines the dependencies between services.

```yml  
services:
  web:
    build: .
    ports:
      - "8000:8000"
    depends_on:
      - db
  db:
    image: postgres
```

Another example : 

```yml  
services:
  web:
    build: .
    ports:
      - "8000:8000"
    depends_on:
      [db, webapi]
  db:
    image: postgres
  webapi:
    image: mywebserver
```

* `networks` : Used to define custom networks for your Docker containers. You can create custom networks using the networks keyword and then specify which networks each service should use using the networks tag in the service definition.  

```yml  
version: '3.8'
networks:
  my_network:
services:
  my_service:
    image: my_image
    networks:
      - my_network
```

At above example, we define a custom network named my_network using the networks keyword.

## Docker Compose Commands 

* Build the docker images and containers 

```bash    
$ docker-compose build

* `docker-commpose` command : Builds and starts containers defined in a Compose file. Creates the containers and links them as specified in the file,  and then starts them in the foreground, so you can see their logs in the terminal. If a container doesn't exist, it will be built first.   

```bash   
$ docker-compose up  
```  

* Start the containers in background  

```bash    
$ docker-compose up -d  
```  

* Stop the containers  

```bash    
$ docker-compose down  
```  

* Start the aleady build/deployed containers 

```bash   
$ docker-compose start  
```

Above command starts containers that have already been created using docker-compose up, but are currently stopped. If a container has never been created, this command will do nothing. 

* List the running containers  

```bash  
$ docker-compose ps  
```   

* Stop the running containers   

```bash  
$ docker-compose stop  
```  

* Check the logs of running containers   

```bash   
$ docker-compose logs -f <container_name>
```  

* Receive real time events from containers  

```bash   
$ docker-compose events
```  

* Run bash shell onto containers 

```bash   
$ docker-compose exec db bash
```

[Detailed article on docker-compose](https://linuxhint.com/beginners_guide_docker_compose/)  
