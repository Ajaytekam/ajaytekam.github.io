---   
title: Kubernetes Basics Notes for Beginners 
author: ajaytekam   
date: 2023-08-05 20:00:00 +0530   
img_path: /assets/posts/20230805/ 
categories: [DevOps, Kubernetes, microservices]    
tags: [DevOps, Kubernetes, microservices]  
image:
  path: kubernetes.png 
---    

## Contents 

- [Kubernetes Basics](#kubernetes-basics)    
    - [What is kubernetes](#what-is-kubernetes)   
    - [Container Orchastration](#container-orchastration)   
- [Kubernetes Architecture](#kubernetes-architecture)   
- [Kubernetes Services](#kubernetes-services)   
- [Setting up k8s cluster with minikube in ubuntu server](#setting-up-k8s-cluster)   
- [Basic cluster Commands](#basic-cluster-commands)    
- [k8s Commands](#k8s-commands)   
- [Debugging Commands](#debugging-commands)   
- [Namespcae Commands](#namespace-commands)   
- [Namespace with kubens](#namespace-with-kubens)   
- [Deploying a simple pod](#deploying-a-simple-pod)  
- [Pod manifest file](#pod-manifest-file)    
- [ReplicaSet Manifest file](#replicaset-manifest-file)  
- [Deployment manifest file](#deployment-manifest-file)   
- [Difference between kubectl create and apply command](#difference-between-kubectl-create-and-apply-command)    
- [K8s Services](#k8s-services)   
    - [NodePort](#nodeport)   
    - [ClusterIP](#clusterip)  
    - [LoadBalancer](#loadbalancer)   
    - [ExternalName](#externalname)  
- [ConfigMaps](#configmaps)   
- [Secrets](#secrets)   
- [Ingress Service](#ingress-service)   
- [Jobs](#jobs)  
- [Volumes](#volumes)  
- [DaemonSets](#daemonsets)  

## Kubernetes Basics   

### What is kubernetes   

* Kubernetes is an open-source platform used to automate the deployment, scaling and management of containerized applications.  
* It is like a traffic controller for containeried applications, which ensures that these applications are running effectively and reliably, by managing their deployment, scaling and update processes.    

### Container Orchastration  

* Container orchastration refers to the process of automating the deployment, scaling and management of containerized applications.   
* Container orchastration tools enable organizations to easily deploy, run and maintain multiple containerized applications and services across a cluster of services or hosts.  

## Kubernetes Architecture     

![](kubernetes1.png)   

### Kubernetes Master Node  

#### API Server :   

- The API server exposes the kubernetes api.   
- Handles all the requests and enables communications across different tools, libraries and services.    
- It is the front-end for the kubernetes control plane.  
- Admins/Users can interact with api server using kubectl cli or web dashboard.     

#### Scheduler :   

- Watches newly created pods that have no node assigned, and selects a node for them to run on.
- Factores taken into account for scheduling decisions include
    - Individual and collective resource requirements
    - Hadrware/software.policy constraints
    - Affinity and anti-affinity specifications
    - Data locality
    - Inner-workload interference and deadlines

#### Controller Manager :  

- It is basically a daemon process (background process) responsible for managing a specific set of kubernetes objects ensuring that the desired state of those objects is maintained.  
- The controller manager runs controllers to administrator nodes and endpoints.    
- These controllers includes :  
    - __Node Controller :__ Responsible for noticing and responding when nodes go down.     
    - __Replication Controller :__ Responsible for maintaining the correct number of pods for every replication controller object in the system.  
    - __Endpoint Controller :__ Populates the endpoints object (that is, joins services & pods). 
    - __Service account and token Controller :__ Create default accounts and API access tokens for new namespace.     


#### ETCD : 

- It provides a reliable and consistent data store for managing the state of the entire cluster.   
- Basically etcd is responsible for storing and managing all of the important information about the kubernetes cluster, including configuration data, metadata, about kubernetes objects (such as pods, services and deployments) and other information such as the state of the kubernetes api server.   

### Kubernetes Worker Node  

#### Pod :

- A pod is a fundamental unit in kubernetes that represents a single instance of a running process.   
- It encapsulates one or more containers, storage resources, and network resources, providing a high level of abstraction and isolation for applications running on the platform.  
- Pods are the basic building blocks of kubernetes applications and are used to deploy, scale and manage containerized applications.  
- Each pod has its unique IP address and can communicate with other pods using this IP address.  
- A pod can contain one or more containers which share the same network namespace and can access the same storge resources. The containers in a pod are tightly coupled and are scheduled togather on the same code.   

#### Kubelet :  

- It is an agent that runs on ecah node in the cluster.  
- It gets the configuration of a pod fro the API server and ensures that the containers are working efficiently.   

#### Kube-Proxy :

- It acts as a load balancer and network proxy to perform service on a single worker node.  
- Runs on each node in the cluster.   
- A proxy service that runs on every node that makes service available to the external host.  

#### Container Runtimes :

- kubernetes supported runtimes are docker, containerd, cri-o, rketlet.   

## Kubernetes Services   

- Service enable network connectivity and load balancing for pods.  
- Service is a way to expose an application running on a set of pods as a network service.   
- When you create a service, you specify which pod you want to target and how you want to access them. 
- Kubernetes then creates a virtualIP for the service, and assigns a DNS name to the VIP.   
- The VIP is used to access the pods and the DNS name allow you to access the service by name from within the cluster.  
- Lifecycle of pod and service are not connected.  

> Note: More detailed explanation for K8s service can be found [here](#k8s-services).   

## Setting up k8s cluster   
 
> Need to make a blogpost with it.  

## Basic Cluster Commands   

* Start the cluster 

```shell   
minikube start
```

* Check the status of minikube cluster

```shell   
minikube status 
```

* Stop the minikube cluster 

```shell   
minikube stop
```

* Executing commands inside minikube cluster node

```shell   
minikube ssh "command" 
```

For example listing running docker contaienr 

```shell  
minikube ssh "docker ps" 
```

## k8s Commands  

* Check the nodes 

```shell   
kubectl get nodes 
```

* Check different k8s components 

```shell   
kubectl get [nodes|pod|services|replicaset|deployment]
```  

To get more detailed information append `-o wide` option

```shell  
kubectl get [nodes|pod|services|replicaset|deployment] -o wide 
```

## Debugging Commands  

* Get details using describe 

```shell   
kubectl descibe [nodes|pod|services|replicaset|deployment] [components_name]  
```

* Get the logs

```shell    
kubectl logs <component_name>
```  

## Namespace Commands   

* Get details of namespaces    

```shell    
kubectl get namespaces   
```   

* Create namespace  

```shell     
kubectl create namespace <namespace-name>   
```   

* Delete namespace 

```shell    
kubectl delete namespace <namespace-name>
```

## Namespace with kubens   

In order to work on a particular namespace we can use kubens to select a particular namespace to work with. Install latest version of kubctx and kubxns using below scripts  

```shell  
#!/bin/bash   

VERSION=`curl -I https://github.com/ahmetb/kubectx/releases/latest | head -10 | grep location | awk -F "/" '{print $NF}' | tr -d '\r'`  
wget "https://github.com/ahmetb/kubectx/releases/download/${VERSION}/kubectx_${VERSION}_linux_x86_64.tar.gz" -O kubectx.tar.gz  
wget "https://github.com/ahmetb/kubectx/releases/download/${VERSION}/kubens_${VERSION}_linux_x86_64.tar.gz" -O kubens.tar.gz  
tar -xvf kubectx.tar.gz  
tar -xvf kubens.tar.gz  
sudo mv kubectx /usr/local/bin/  
sudo mv kubens /usr/local/bin/  
rm LICENSE kubens.tar.gz kubectx.tar.gz  
```   

Now run kubens and select the namespace yuu want to work with.  

```shell      
$ kubens  
 
  default  
  kube-node-lease  
  kube-public  
  kube-system  
> my-namespace  

✔ Active namespace is "my-namespace".   
```  

You can use up-down arrow key select one.     

* Also note that when you delete the namespace then all the components inside that namespace is automatically gets deleted.   

```shell  
$ kubectl create ns kubekart  
$ kubens kubekart  
$ kubectl run nginx --image=nginx:latest  
$ kubectl run nginx1 --image=nginx:latest  
$ kubsns default   
$ kubectl delete ns kubekart   
```   

## Deploying a Simple Pod     

* Deploying nginx pod using commandline   

```shell   
kubectl run nginx --image=nginx 
```

getting the details of deployed pod 

```shell   
kubectl get pods 
// or 
kubectl get pods -o wide 
```  

Get more detials with describe command 

```shell   
kubectl describe pods 
```

Checking logs of pod 

```shell   
kubectl logs nginx
```

## Pod manifest file  

Example of Pod Manifest file  

```yml  
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
  namespace: my-namespace
  labels:
      app: myapp
      type: front-end
spec:
  containers:
    - name: my-container
      image: nginx:latest
      ports:
        - containerPort: 80
```  

There are four main section in the manifest file  

* `apiVersion` : Specifies the Kubernetes API version to use. In this example, we're using the v1 version.  
* `kind` : Defines the type of Kubernetes resource. In this case, it's a Pod.  
* `metadata` : Contains metadata about the pod, such as its name, labels, namespace.  
    * `name` : name of the pod. 
    * `namespace` : namespace in which the pod will be created.    
* `spec` : Describes the desired state of the pod.  
    * `containers` : Defines the containers to run within the pod.
      - `name` : Specifies a name for the container. Note that it only specify the name of the container running on top of docker runtime, not the name of the pod itself.   
      - `image` : Specifies the container image to use.
      - `ports` : Defines the ports to expose on the container.


* Creating the pod 

```shell   
kubectl create -f pod-definition.yml 
// or 
kubectl apply -f pod-definition.yml 
```  
 
## ReplicaSet Manifest file  

* A replicaset ensures that a specified number of identical pod replicas are running at all times. It is primarily used for scaling and ensuring high availability of applications.  
* It continuously monitors the running pods and takes action to create or delete pods as needed to match the desired state specified in the ReplicaSet configuration.  

```yml   
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: my-replicaset
  namespace: test-ns
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
        - name: my-container
          image: nginx:latest
          ports:
            - containerPort: 80
```

* `apiVersion` : Specifies the Kubernetes API version to use. In this example, we're using apps/v1 version for ReplicaSet.    
* `kind` : Defines the type of Kubernetes resource. In this case, it's a ReplicaSet.   
* `metadata` : Contains metadata about the ReplicaSet, such as its name.   
* `spec` : Describes the desired state of the ReplicaSet.   
    * `replicas` : Specifies the desired number of pod replicas to maintain. In this example, we want 3 replicas.    
    * `selector` : Specifies the labels used to select the pods that are part of the ReplicaSet.  
        - `matchLabels` : Used to determine which pods are considered part of the ReplicaSet and should be managed by it. The labels must match with the pods/deployments labels.   
    * `template` : Defines the pod template used to create the pod replicas. The content of the template is basically what we have seen in the pod manifest files. Example
        - `metadata` : Contains labels that are applied to the pods created by the ReplicaSet.   
        - `spec` : Describes the specifications for the pod replicas, including the container images, ports, and other configurations.   

* Commands for replicasets : 

```shell   
// creating replicaset 
kubectl apply -f replicaset-mf.yml  

// or 
kubectl create -f replicaset-mf.yml  

// get the replicaset 
kubectl get replicaset
// or 
kubectl get rs  
// get more details  
kubectl get rs -o wide  

// delete replicaset 
kubectl delete -f replicaset-mf.yml  
```

* Updating number of replicas 

by updating the number of replicas number in manifest files  

```yml         
spec:
  replicas: 6
```

```shell   
kubectl apply -f replicaset-mf.yml    
// Or 
kubectl replace -f replicaset-mf.yml    
```   

Or by updating the replicaset with commandline  

```shell   
kubectl scale -replicas=6 -f replicaset-mf.yml    
```

Also remember that you only update the replicaset on current cluster, but in manifest file the replicas number will remain same as previous.   

## Replicaset replicas 

The replicas basically 

```yml  
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app   
```    

At above basically the replicaset will run three pods, but if there is a pod already running with the app label `my-app`, which is defined inside `selector > matchLabels > app` then it will not going to start the pod if there is three pods running and if somhow pods gets deleted/stoped then only it will create new pod using the template defined in replicaset manifest files.  

## Deployment manifest file   

* A deployment is a higher-level objects that manages the lifecycle of a set of pods.    
* Deployments can create, scale and update replicas of a pod template, ensuring that the desired number of pods are running and that they are up-to-date with the latest configuration.    

Example of deployment file 

```yml  
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp-deployment
  namespace: test-ns
  labels:
      app: nginx
      type: front-end
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
        type: front-end
    spec:
      containers:
        - name: nginx
          image: nginx:latest
          ports:
            - containerPort: 80
```  

* Commands 

```shell   
// create deployment file  
kubectl create -f deployment-mf.yml 
// or 
kubectl apply -f deployment-mf.yml   

// get the deployments 
kubectl get deployments 

// Changing the base docker image/version with commandline without editing the manifest file  
kubectl set image deployment/myapp-deployment nginx=nginx:1.9.1   

// check the current status of rollout  
kubectl rollout status deployment/myapp-deployment  

// check the history of rollout  
kubectl rollout history deployment/myapp-deployment  
```   

* The `kubectl rollout` and `kubectl rollout undo` command  

`rollout` : When you create new deployment, then it is known as rollout version 1, and in the future when the application version or container version is updated then the deplyment is updated and new rollout is created named version 2.   
`Deployment Stratagies` : 

First change the docker image version with below command, which basically rollout a new revison of deployment 

```shell   
kubectl set image deployment/myapp-deployment nginx=nginx:1.9.1   
```

Now check the status of the rollout process

```shell   
kubectl rollout status deployment/myapp-deployment  
```  

Now after successfull rollout, the rew revision (basically replicaset) will created, you can check the revision with command 

```shell 
kubectl rollout history deployment/myapp-deplyment  
```  

It will shows the numbered revision, to check the current revision use descirbe option 

```shell  
kuebctl desribe deployment/myapp-deployment  
```  

At `Annotations` you can see the revision number.   

In order to rollback to just previous revision run command 

```shell   
kubectl rollout undo deployment/myapp-deployment 
```  

Now to undo the rollback provide the appropriate revision 

```shell    
kubectl rollout undo deployment/myapp-deployment --to-revision=1
```  

* `--record` option :  

The record option save the command used to create/update the deployment within the revision history. Example  

```shell 
kubectl apply -f deployment-mf.yml --record

kubectl set image deployment/myapp-deployment nginx=nginx:1.9.1  --record 
```  

Now if you look at the `kubectl rollout history` command then `CHANGE-CAUSE` column shows the command which is reponsible for that particular rollout.  


## Difference between kubectl create and apply command    

* `kubectl create` command creates a new resource in the cluster. If the resource already exists, it will return an error. This command is useful when you want to ensure that a resource does not already exist before creating it.   
* `kubectl apply` command creates or updates resources in the cluster based on the configuration specified in the manifest file. If the resource already exists, kubectl apply will update it to match the desired state defined in the manifest.   

## K8s Services      

* Service is a way to expose an application running on a set of pods as a network service.    
* Service enable network connectivity and load balancing for pods.    

Types of Services :    

### NodePort 
    
* Opens a specific port on all nodes and forwards any traffic sent to this port to the service.      
* Nodeport service exposes an application running inside a cluster to the outside world by mapping a specific port on each worker node to the service.  

![](nodeport.png)    

* targetport is port number where service is running on pod.    
* port is port number for service object, Note: If you don't provide the targetport then it will be assume as same as port.   
* Nodeport ranges between between 30000 and 32767 on each worker node, if you don't provide the nodeport then any empty port between the range will assign to the nodeport service.  
* The NodePort service is accessible from outside the cluster using the IP address of any worker node along with the assigned port number `http://nodeIP:nodePort`.   
* Traffic received on that specific port of any worker node is forwarded to the corresponding service and target pod(s) running inside the cluster.   
* Kubernetes takes care of load balancing the traffic across the available pods for that service.    

Example 

```yml  
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
  namespace: test-ns
spec:
  type: NodePort
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
      nodePort: 30008
```   

* `apiVersion` and `kind` specify the Kubernetes API version and the resource type, which is Service in this case.   
* `metadata` section contains metadata about the service, including the name of the service (my-nodeport-service in this example).   
* `spec` section defines the specifications for the service.    
* `type` is set to `NodePort`, indicating that this service should be exposed on a port on each Kubernetes node.   
* `ports` lists the ports to be exposed by the service. In this case, we expose port 80 on all nodes, which will be forwarded to port 80 on the selected pods. `nodePort` specifies the port number on each node that will be used to access the service. In this case, it's set to 30008.
* `selector` specifies the labels used to select the pods that should be included in this service. In this example, pods with the label `app: nginx` will be included.   

```shell           
// create the service 
kubectl create -f nodeport-svc-mf.yml 

// check the service 
kubectl get svc/service -n test-ns 

// more detailed info 
kubectl get svc -o wide -n test-ns
```

Accessing the nginx site on the system ifself.  

```shell   
// get the url in minikube 
minikube service myapp-service --url -n test-ns  

// now you can access the nginx page form the host machine 
curl http://192.168.49.2:30008   
// or you can use w3m 
w3m http://192.168.49.2:30008   
```  

**A small trick to access nodePort website from outside of the Host System :** So in my case i am using ubuntu server in virtualbox to run minikube k8s cluster, so in order to access the website through browser i wrote a small shell script to port forward the srvice into my main host machine (widows) 

Shll script: portfwd.sh  

```shell 
#!/bin/bash

CheckIfNo() {
    if ! [[ "$1" =~ ^[0-9]+$ ]]
    then
        echo "Error: $1 is not a number.!!"
        exit
    fi
}

# check if ncat is installed or not
ret=`which ncat`
if [ -z "$ret" ]
then
    echo "Error: 'ncat' is not installed.!!"
    exit
fi

# take input
read -p "Enter IP Addess [127.0.0.1] :> " ip_addr
read -p "Enter Service port no. to Forword :> " service_port
read -p "Enter External Port to Forword :> " extr_port

if [ -z "$ip_addr" ]
then
    ip_addr=127.0.0.1
fi

# check the entered port number
CheckIfNo $service_port
CheckIfNo $extr_port

# Command
# echo ncat -l -p $extr_port -c "ncat $ip_addr $service_port"
ncat -lv -p $extr_port -c "ncat $ip_addr $service_port"
```

**Usage :**  

First get the url 

```shell   
minikube service myapp-service --url -n test-ns   
// output   
http://192.168.49.2:30008  
```  

Now run the portfwd.sh script 

```shell   
./portfwd.sh
Enter IP Addess [127.0.0.1] :> 192.168.49.2
Enter Service port no. to Forword :> 30008
Enter External Port to Forword :> 9090

Ncat: Version 7.80 ( https://nmap.org/ncat )
Ncat: Listening on :::9090
Ncat: Listening on 0.0.0.0:9090
```

Now you will be able to access the nginx web server by using your virtualbox ubuntu server ip address, which is in my case `http://192.168.1.16:9090`.   

### ClusterIP  

* Default k8s service which exposes an internal IP address, reachable only within the cluster, for communication between different components within the cluster.    
* It basically allows the other services or pods within the cluster to communicate with the targeted pods using the internal IP address provided by the service.   

Example manifest file    

```yml         
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
  namespace: test-ns
spec:
  type: ClusterIP
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
```    

```shell   
// create the service 
kubectl create -f nodeport-svc-mf.yml 

// check the service 
kubectl get svc/service -n test-ns 

// more detailed info 
kubectl get svc -o wide -n test-ns
```

### LoadBalancer     

* Expose services to the internet using a cloud provider's loadbalancer.    
* When you create a LoadBalancer service in Kubernetes, the underlying cloud provider provisions a load balancer, such as an Elastic Load Balancer (ELB) in AWS or a Load Balancer in Azure, to distribute traffic to the pods.     

Manifest file example 

```yml         
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
  namespace: test-ns 
spec:
  type: LoadBalancer
  selector:
    app: nginx 
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
```  

### ExternalName 

* an ExternalName service is a type of service that provides a way to access external services located outside the cluster. 
* It is used when you want to create a service that maps to a DNS name rather than a set of pods or endpoints within the cluster.
* ExternalName services are often used to access external dependencies or services that are running outside the Kubernetes cluster, such as databases or legacy systems.   
* By creating an ExternalName service, you can provide a consistent and stable DNS name to refer to the external service, regardless of its location or IP address changes.

Example 

```yml      
apiVersion: v1
kind: Service
metadata:
  name: my-service
  namespace: myapp-service
spec:
  type: ExternalName
  externalName: example.com
```

## ConfigMaps 

* Configmap is an object that allows you to store and manage configuration data such as environment variables, service port numebrs etc. Example of the configmaps    
* A ConfigMap object basically store non-confidential data in key-value pairs.  

Example of configMaps 

```yml  
apiVersion: v1   
kind: ConfigMap   
metadata:  
  name: myconfigmap  
  namespace: test-ns   
data:   
  database_url: jdbc:mysql://mysql.example.com:3306/mydb  
  api_key: ABCDEFG123456789   
  max_connections: "10"    
```   

```shell     
// creating config files    
kubectl create -f configmap-mf.yml    
```   

Accessing the configmap data 

```yml         
apiVersion: apps/v1
kind: Deployment
metadata:
  name: appserver
  labels:
    app: appsrv
spec:
  selector:
    matchLabels:
      app: appsrv  
  replicas: 1
  template:
    metadata:
      labels:
        app: appsrv
    spec:
      containers:
        - name: myappsrv
          image: test/appserver
          env:
            - name: DATABASE_URL
              valueFrom:
                configMapKeyRef:
                  name: myconfigmap 
                  key: database_url 
            - name: APIKEY_VALUE 
              valueFrom:
                configMapKeyRef:
                  name: myconfigmap 
                  key: apikey
            - name: MAX_CONNECTION 
              valueFrom:
                configMapKeyRef:
                  name: myconfigmap 
                  key: max_connections 
```

> Note: Configmap can also store file-like data, look at the official documentation for that https://kubernetes.io/docs/concepts/configuration/configmap/   

## Secrets  

Secrets is an object that allows you to store sensitive informatioon such as database credentials, TLS certificate, passwords, api keys etc. Example of Secrets : 

```yml    
apiVersion: v1
kind: Secret
metadata:
  name: app-secret
  namespace: test-ns
type: Opaque 
data:  
  db-pass: dnByb2RicGFzcw==   
  rmq-pass: Z3Vlc3Q=  
```  

* `data` : This section defines the sensitive information you want to store securely. It can include key-value pairs where the values are base64-encoded. Common use cases include storing passwords, API keys, or certificates.   
* `type` : This field can have different values depending on the type of Secrets you want to create. The most commonly used types are:  
    - `Opaque` : This is the default type if the type field is not specified. It is a generic type that allows you to store arbitrary key-value pairs as Secrets. The values are stored as base64-encoded strings.   
    - `kubernetes.io/basic-auth` : This type is used for storing username and password credentials. It requires two keys: username and password.    
    - `kubernetes.io/ssh-auth` : This type is used for storing SSH keys. It requires two keys: ssh-privatekey and ssh-publickey.   
    - `kubernetes.io/tls` : This type is used for storing TLS certificates and keys. It requires the following keys: tls.key (private key), tls.crt (certificate), and optionally ca.crt (CA certificate).    


* Creating secret 

```shell  
kubectl create -f secret-mf.yml 
```

* Example of using secrets file with deployment object :  

```yml      
apiVersion: apps/v1
kind: Deployment
metadata:
  name: vprodb
  labels:
    app: vprodb
spec:
  selector:
    matchLabels:
      app: vprodb
  replicas: 1
  template:
    metadata:
      labels:
        app: vprodb
    spec:
      containers:
        - name: vprodb
          image: mysql:latest
          volumeMounts:
            - mountPath: /var/lib/mysql
              name: vpro-db-data
          ports: 
            - name: vprodb-port
              containerPort: 3306
          env:
            - name: MYSQL_ROOT_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: app-secret
                  key: db-pass
```    

As you can see at the env section the `MYSQL_ROOT_PASSWORD` environment variable is using the db-pass from app-secret manifest file.    

Similarly  

```yml  
apiVersion: apps/v1
kind: Deployment
metadata:
  name: vpromq01
  labels:
    app: vpromq01
spec:
  selector:
    matchLabels:
      app: vpromq01
  replicas: 1
  template:
    metadata:
      labels:
        app: vpromq01
    spec:
      containers:
        - name: vpromq01
          image: rabbitmq
          ports:
            - name: vpromq01-port
              containerPort: 15672
          env:
            - name: RABBITMQ_DEFAULT_PASS
              valueFrom:
                secretKeyRef:
                  name: app-secret
                  key: rmq-pass
```

Similarly for the rabbitmq `RABBITMQ_DEFAULT_PASS` environment variable is using rmq-pass.   

* Deleting secret 

```
kubectl delete -f secret-mf.yml  
```  
 
## Ingress Service   

* Ingress service is an API object that serves as an entry point for external traffic into cluster.  
* It acts as a reverse proxy that manages incoming traffic and forwards it to the appropriate services.  
* It allows you to define rules for how incoming requests should be routed to specific services within the cluster based on the requested hostname and URL path. 
* It allows you to consolidate and manage external access to multiple services using a single Ingress resource.  
* Provide load balancing, SSL termination and name-based virtual hosting.     

__Ingress Resource__   

The Ingress resource provides a way to expose HTTP and HTTPS routes from outside the cluster to services within the cluster without having to create individual LoadBalancer or NodePort services for each service.  

Example of ingress resource configuration file 

```yml    
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: vpro-ingress
  annotations:
    nginx.ingress.kubernetes.io/use-regex: "true"
spec:
  ingressClassName: nginx
  rules:
  - host: vprofile.aliencloud.tech
    http:
      paths:
      - path: /app1
        pathType: Prefix
        backend:
          service:
            name: app1-service
            port:
              number: 80
      - path: /app2
        pathType: Prefix
        backend:
          service:
            name: app2-service
            port:
              number: 80
```   

* `apiVersion : networking.k8s.io/v1` : Specifies the API version for the Ingress resource. In this case, it's using the "networking.k8s.io/v1" version, which is the API version for Kubernetes networking resources.  
* `kind: Ingress` : Specifies the kind of the resource, which is "Ingress" in this case, indicating that this YAML represents an Ingress resource.  
* `metadata` : Contains metadata for the Ingress resource, including its name and annotations. Annotations are used to add additional information or configuration to the Ingress resource.
    - `name: vpro-ingress` : Sets the name of the Ingress resource to "vpro-ingress".  
    - `annotations: This section allows you to specify annotations for the Ingress resource. In this case, it includes an annotation to configure the Nginx Ingress controller.
        - `nginx.ingress.kubernetes.io/use-regex: "true"` : This annotation instructs the Nginx Ingress controller to use regular expression matching for the specified paths. Regular expression matching can be more flexible than the default path matching methods.
* `spec` : Defines the specification of the Ingress resource.  
    - `ingressClassName: nginx` : Specifies the class name of the Ingress controller to be used for this Ingress resource. In this case, it indicates that the Nginx Ingress controller should handle this Ingress.
    - `rules` : Contains the routing rules for incoming traffic based on the requested hostname and URL paths.  
        - `host: vprofile.aliencloud.tech` : This rule indicates that incoming requests with the hostname "vprofile.aliencloud.tech" should be handled by this Ingress resource.
        - `http` : The rule applies to HTTP traffic.  
            - `paths` : Contains a list of path-based routing rules.  
                - `path: /app1` : Incoming requests with the path "/app1" will be matched by this rule.
                - `pathType: Prefix` : Specifies the type of path matching, in this case, "Prefix" means that any path starting with "/app1" will match.    
                - `backend` : Defines the backend service that should receive the matched requests.   
                    - `service` : Specifies the name of the Kubernetes service that should receive the traffic.   
                        - `name: app1-service` : The name of the Kubernetes service to handle the traffic is "app1-service".   
                        - `port` : Specifies the port number of the service to which the traffic should be forwarded.   
                            - `number: 80` : The port number is set to 80.


__Ingress Controller :__   

* Ingress controller is responsible for implementing the rules defined in the Ingress resources and managing the external access to the services within the cluster.  
* To use Ingress resources, your Kubernetes cluster must have an Ingress controller installed and properly configured.  
* Popular Ingress controllers include Nginx Ingress Controller, Traefik, HAProxy Ingress, and others.  

## Jobs  

* A kubernetes job is an object that creates a set of pods and waits for them to terminate.  
* A job is a higher level abstraction over a pod that ensures thata specified number of pods successfully completed.   
* A job creates one or more pods to complete a task and ensures that a specified number of them successfully terminate if a pod fails or is terminated for some version, kubernetes automatically creates a replacement pod to complete the job. Once all pods have successfully completed, the job is considered finished.  
* Jobs are commonly used for tasks that need to run only once or on a schedule, such as batch processing jobs, data analytics, backups and terminate tasks.   

Key characteristics of a Kubernetes Job:  

* `Pod Template` : Like other Kubernetes controllers, a Job uses a Pod template to create Pods that run the desired workload. The Pod runs the actual task or process defined by the Job.

* `Parallelism` : Jobs can create multiple Pods in parallel to complete tasks more quickly. The number of Pods running in parallel is determined by the "parallelism" parameter, which can be specified when defining the Job.

* `Completions` : The "completions" parameter specifies the number of successful completions that the Job should achieve before considering the task as successfully finished.

* `Deadline` : The "activeDeadlineSeconds" parameter defines the maximum amount of time a Job should run, regardless of whether the desired number of completions has been reached.

* `Backoff Limit` : The "backoffLimit" parameter controls the number of retries the Job should attempt in case of a failure. If the number of retries exceeds this limit, the Job is considered to have failed.  
Once a Job is created, Kubernetes ensures that the desired number of Pods specified in the Job's template is running. If any Pod fails, Kubernetes automatically recreates a new Pod to replace the failed one, respecting the "backoffLimit" to avoid endless retries.

Example YAML representation of a Kubernetes Job:

```yml     
apiVersion: batch/v1
kind: Job
metadata:
  name: my-job
spec:
  completions: 1
  parallelism: 1
  template:
    spec:
      containers:
      - name: my-job-container
        image: my-container-image:tag
        command: ["my-task-executable"]
      restartPolicy: Never

```       

Explanation :  


* The Job "my-job" is defined to run a single Pod with a container named "my-job-container."    
* The container runs the executable "my-task-executable" from the container image "my-container-image:tag." 
* The Job is set to have a single successful completion (completions: 1) and a parallelism of 1 (only one Pod running at a time). 
* The "restartPolicy" is set to "Never" to ensure that the Pod is not automatically restarted if it fails.  

## Volumes 

* Volumes mount external filesystem storage inside your pods.  
* volumes are used to provide persistent storage to containers running within Pods.   
* Volumes decouple the storage from the Pod's lifecycle, which means that the data stored in a volume can survive the container's termination or restart.   
* Volumes can and shared between your pods. This allows kubernetes to run stateful applications where data must be preserved after a pod gets terminated or rescheduled.     
* There are different types of volumes available in Kubernetes, and each type has its specific use cases. 

## DaemonSets 

* DaemonSets are used to relianly run a copy of a pod on each of the nodes in your cluster.      
* When a new node joins, it will automatically start an instance of the pod.  
* You can optionally restrict daemonset pods to only runnig on a specific nodes in more advanced situations.  
* DaemonSets are particularly useful for running system-level daemons or agents that need to be available on each node in the cluster, such as logging agents, monitoring agents, and networking daemons.  

__Example of Daemonsets :__   

```yml  
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: my-daemonset
spec:
  selector:
    matchLabels:
      app: my-daemon-app
  template:
    metadata:
      labels:
        app: my-daemon-app
    spec:
      containers:
      - name: my-daemon-container
        image: my-container-image:tag
```  

* In this example, we create a DaemonSet named `my-daemonset` that selects nodes based on the label `app: my-daemon-app`. 
* The DaemonSet runs one Pod on each selected node, using the specified container image `my-container-image:tag`.  
* Once this DaemonSet is created, Kubernetes ensures that there is one Pod running on each node with the label `app: my-daemon-app`, and it automatically creates and deletes Pods as nodes are added or removed from the cluster. 
* This ensures that the specified container (my-daemon-container) is running on all nodes where the label `app: my-daemon-app` is present.   
