---
title: "Kubernetes Part 1 : Architecture and Core Concepts (Foundation)"
date: 2026-07-18
categories: ["kubernetes", "cloud", "infrastructure"]
draft: false
---

## Topics 

- [What is Kubernetes](#what-is-kubernetes)  
- [Why Kubernetes](#why-kubernetes)  
- [Containers vs VM](#containers-vs-vm)  
- [Cluster Components](#cluster-components)  
    - [Control Plane](#control-plane)  
        - [API Server](#api-server)  
        - [etcd](#etcd)  
        - [Scheduler](#scheduler)  
        - [Controller Manager](#controller-manager)  
        - [Cloud Controller Manager](#cloud-controller-manager)    
    - [Worker Node](#worker-node)  
        - [Kubelet](#kubelet)  
        - [Kube Proxy](#kube-proxy)  
        - [Pods](#pods)     
        - [Container Runtime](#container-runtime)   
- [Kubernetes API](#kubernetes-api)  
    - [API Groups](#api-groups)  
    - [Rest Examples](#rest-Examples)
- [Desired State](#desired-state)    
- [Reconcilliation Loop](#reconcilliation-loop)  
    - [High Level Flow](#high-level-flow)   
- [Declarative vs Imperative](#declarative-vs-imperative)  
    - [Declarative](#declarative)  
    - [Imperative Workflow](#imperative-worflow)  
    - [Comparison](#Comparison)  

## Hands On 

<youtube video>

Topics Covered: 

- Install Minikube
- Install Kind
- Create Cluster
- Explore nodes
- kubectl basics


### What is Kubernetes     

- Kubernetes is an open-source platform used to automate the deployment, scaling and management of containerized applications.  
- It is like a traffic controller for containeried applications, which ensures that these applications are running effectively and reliably, by managing their deployment, scaling and update processes.
- Think of kubernetes as an operating system for a cluster of servers. Instead of managing containers on one machine, kubernetes manages thousands of containers across many machines.  


### Why Kubernetes

Suppose you have an e-commerce application with several services like Frontend (React), Backend (Django), PostgreSQL, Redis, RabbitMQ and Nginx. With docker compose you can run all these on a single server. This works well for development or small deployments. But in certain scenerio like when 100,000 users visit your site, or one server crashes or you need to scale only the backend or you need zero downtime during updates?, in that case docker compose won't solve the problem, and Kubernetes comes to the picture. 

### Containers vs VM

#### Virtual Machine  

A Virtual Machine virtualizes the hardware. Each VM includes its own operating system, kernel, libraries, and application. A Hypervisor (such as VMware, KVM, or Hyper-V) allows multiple VMs to run on the same physical server.

```  
                 Physical Server
+--------------------------------------------------+
| CPU | RAM | Disk | Network                       |
+--------------------------------------------------+
                     │
                     ▼
                Hypervisor
     (VMware, KVM, Hyper-V, Xen)
                     │
      ┌──────────────┼──────────────┐
      ▼              ▼              ▼
+-------------+ +-------------+ +-------------+
| VM 1        | | VM 2        | | VM 3        |
|-------------| |-------------| |-------------|
| Guest OS    | | Guest OS    | | Guest OS    |
| Libraries   | | Libraries   | | Libraries   |
| App         | | App         | | App         |
+-------------+ +-------------+ +-------------+
```  

Each VM has its own operating system and own kernel.

#### Containers  

A Container virtualizes the operating system, not the hardware. Containers share the host OS kernel while packaging only the application and its dependencies.  

```  
                 Physical Server
+--------------------------------------------------+
| CPU | RAM | Disk | Network                       |
+--------------------------------------------------+
                     │
                     ▼
              Host Operating System
                     │
                     ▼
       Container Runtime (containerd/Docker)
                     │
      ┌──────────────┼──────────────┐
      ▼              ▼              ▼
+-------------+ +-------------+ +-------------+
| Container 1 | | Container 2 | | Container 3 |
|-------------| |-------------| |-------------|
| App         | | App         | | App         |
| Libraries   | | Libraries   | | Libraries   |
+-------------+ +-------------+ +-------------+
```  

Notice that containers do not include a guest operating system.   

### Cluster Components


```  
                    Kubernetes Cluster

            +-----------------------------+
            |        Control Plane        |
            |-----------------------------|
            | API Server                  |
            | Scheduler                   |
            | Controller Manager          |
            | etcd                        |
            +-----------------------------+
                            |
                -----------------------------
                |                           |
        +-------------------+      +-------------------+
        | Worker Node 1     |      | Worker Node 2     |
        | kubelet           |      | kubelet           |
        | kube-proxy        |      | kube-proxy        |
        | Pods              |      | Pods              |
        +-------------------+      +-------------------+
```

- Control Plane: Manages the cluster.   
- Workder Node: Run your applications.   

### Control Plane

The control plane is the brain of Kubernetes.

It makes decisions like:

- Where should a Pod run?
- Is a Pod unhealthy?
- Should more Pods be created?
- Has a user deployed a new application?
- Should a failed Pod be recreated?

It doesn't usually run your application workloads; instead, it manages the cluster.

#### API Server

- The API server exposes the kubernetes api.  
- Handles all the requests and enables communications across different tools, libraries and services.   
- It is like front-end for the kubernetes control plane.   
- Admins/Users can interact with api server using kubectl cli or web dashboard.  

```  
kubectl apply -f deployment.yaml
           |
           v
    kube-apiserver
```  

- Accepts REST API requests
- Authenticates users
- Validates requests
- Stores cluster state in etcd

#### etcd

- It provides a reliable and consistent data store for managing the state of the entire cluster.  
- Basically etcd is responsible for storing and managing all of the important information about the kubernetes cluster, including configuration data, metadata, about kubernetes objects (such as pods, services and deployments) and other information such as the state of the kubernetes api server.    
- If etcd is lost and you have no backup, the cluster state is effectively lost.  

#### Scheduler

- The scheduler decides which worker node should run a Pod.  
- Factores taken into account for scheduling decisions include
    - Individual and collective resource requirements
    - Hadrware/software.policy constraints
    - Affinity and anti-affinity specifications
    - Data locality
    - Inner-workload interference and deadlines   

```  
Deployment
      |
      v
Need 3 Pods

Worker-1  CPU 95%
Worker-2  CPU 20%
Worker-3  CPU 35%

Scheduler chooses Worker-2
```   

#### Controller Manager

- It is basically a daemon process (background process) responsible for managing a specific set of kubernetes objects ensuring that the desired state of those objects is maintained.
- The controller manager runs controllers to administrator nodes and endpoints.
- These controllers includes :
    - **Node Controller** : Responsible for noticing and responding when nodes go down.
    - **Replication Controller** : Responsible for maintaining the correct number of pods for every replication controller object in the system.
    - **Endpoint Controller** : Populates the endpoints object (that is, joins services & pods).
    - **Service account and token Controller** : Create default accounts and API access tokens for new namespace.

For Example : 

```   
Desired State [3 Pods]
        |
        v
Current State [2 Pods]
        |
        v
Controller Action [Create 1 more Pod]
```   

#### Cloud Controller Manager   

- Used in cloud environments like GKE, EKS, AKS. 
- It manages cloud-specific resources such as:
    - Load Balancers
    - Persistent Disks
    - Node lifecycle
    - Routes

### Worker Node

Worker nodes actually run the applications.

#### Kubelet

- It is an agent that runs on ecah node in the cluster.
- It gets the configuration of a pod from the API server and ensures that the containers are working efficiently.

#### Kube Proxy   

- Handles networking between Services and Pods.  
- It acts as a load balancer and network proxy to perform service on a single worker node.  
- Runs on each node in the cluster.  
- A proxy service that runs on every node that makes service available to the external host.  

#### Pods  

- Pods are the basic building blocks of kubernetes applications and are used to deploy, scale and manage containerized applications.  
- A pod can contain one or more containers which share the same network namespace and can access the same storge resources. The containers in a pod are tightly coupled and are scheduled togather on the same code.  
- It encapsulates one or more containers, storage resources, and network resources, providing a high level of abstraction and isolation for applications running on the platform.   
- Each pod has its unique IP address and can communicate with other pods using this IP address.  

#### Container Runtime

kubernetes supported runtimes are docker, containerd, cri-o, rketlet.  

### Kubernetes API

The Kubernetes API is the central communication interface of a Kubernetes cluster. Every operation—whether performed by a user, an automation tool, or another Kubernetes component—goes through the kube-apiserver.  

```  
                    User / CI-CD / Applications
                               │
                kubectl / REST API / SDKs
                               │
                               ▼
                     +-------------------+
                     |  kube-apiserver   |
                     +-------------------+
                      │        │        │
          ┌───────────┘        │        └────────────┐
          ▼                    ▼                     ▼
      Scheduler        Controller Manager         etcd
          │
          ▼
      Worker Nodes
```   

Every Kubernetes component communicates with the API server instead of talking directly to each other.  

Kubernetes API request Flow: 

```  
User
 │
 │ kubectl apply deployment.yaml
 ▼
kube-apiserver
 │
 ├── Authenticate user
 ├── Authorize request (RBAC)
 ├── Run Admission Controllers
 ├── Validate object
 ▼
etcd
 │
 ▼
Scheduler notices new Deployment
 │
 ▼
Assigns Pod to Worker Node
 │
 ▼
API Server updates Pod assignment
 │
 ▼
kubelet watches API Server
 │
 ▼
Container Runtime starts the Pod
```    

#### API Groups  

Kubernetes organizes APIs into groups.

**Core API**  

```   
/api/v1
```  

__Resources :__

- Pods
- Services
- ConfigMaps
- Secrets
- PersistentVolumes
- Named API Groups

```   
/apis/apps/v1
```   

__Resources :__

- Deployments
- ReplicaSets
- StatefulSets
- DaemonSets

```   
/apis/batch/v1
```  

__Resources :__  

- Jobs
- CronJobs


```   
/apis/networking.k8s.io/v1
```  

__Resources :__  

- Ingress
- NetworkPolicy

#### Rest Examples 

Create a Pod

```    
POST /api/v1/namespaces/default/pods
```  

Get all Pods

```  
GET /api/v1/pods
```  

Delete a Pod

```   
DELETE /api/v1/namespaces/default/pods/nginx
```  

Get Deployments

```   
GET /apis/apps/v1/deployments
```  

### Desired State    

Desired state is the target configuration of the Kubernetes cluster defined by the user in YAML manifests. It describes what should exist, such as the number of replicas, the container image, or resource limits. Kubernetes controllers continuously compare the desired state stored in etcd with the actual state of the cluster. If they differ—for example, because a Pod crashes or a replica count changes—the controllers automatically take corrective actions to make the actual state match the desired state.  

Example :   

```   
apiVersion: apps/v1
kind: Deployment

metadata:
  name: nginx

spec:
  replicas: 3
```   

As per the above spec 3 nginx pods should be running. If the cluster might currently have 2 pods, then deployement controller notices the difference and make request to spin-up remaining pod, so thet the state achieved.    

### Reconcilliation Loop

The Reconciliation Loop is the core mechanism that makes Kubernetes self-healing. It is a continuous process where Kubernetes controllers compare the desired state of the cluster with the actual state and take actions to make them match.   

Think of it as Kubernetes repeatedly asking "Does the current cluster match what the user wants?". If the answer is No, Kubernetes fixes it automatically.

#### High-Level Flow  

```  
        User defines desired state
                 │
                 ▼
              API Server
                 │
                 ▼
                etcd
                 │
                 ▼
      Controller reads desired state
                 │
                 ▼
      Controller checks actual state
                 │
      ┌──────────┴──────────┐
      │                     │
   States Match        States Differ
      │                     │
      ▼                     ▼
 Wait for changes     Create/Delete/Update Resources
      │                     │
      └──────────┬──────────┘
                 ▼
        Check Again (Loop)
```  

This continuous checking is the reconciliation loop.


### Declarative vs Imperative

The difference between Declarative and Imperative is one of the most common Kubernetes interview topics.

The easiest way to remember it is:

- **Declarative** : Tell Kubernetes what you want, and Kubernetes figures out how to achieve it. 
- **Imperative** : Tell Kubernetes how to do something.

#### Declarative 

You simply say:

```  
I want to go to the airport.
```  

A navigation app determines the best route. You're specifying what you want, not how to get there.

**Example :**  

You define the desired state in a YAML file.   

```yaml  
apiVersion: apps/v1
kind: Deployment

metadata:
  name: nginx

spec:
  replicas: 3

  selector:
    matchLabels:
      app: nginx

  template:
    metadata:
      labels:
        app: nginx

    spec:
      containers:
      - name: nginx
        image: nginx:1.25
```  

Apply it:  

```bash     
kubectl apply -f deployment.yaml
```  

You never tell Kubernetes:

```   
Create Pod 1
Create Pod 2
Create Pod 3
```  

You simply declare:

```  
I want 3 replicas running the nginx:1.25 image.
```  

Kubernetes does the rest.

**Declarative Workflow :**   

```  
deployment.yaml
       │
       ▼
kubectl apply
       │
       ▼
Desired State Stored
       │
       ▼
Controllers Ensure Desired State
```  

#### Imperative Workflow 

You're giving step-by-step directions:

```   
Walk 100 meters, turn left, then turn right, then take the stairs.
```  

You're specifying how to reach the destination. You execute commands directly.  

```  
kubectl run nginx --image=nginx
```   

Kubernetes immediately creates a Pod.  

Scale manually:

```   
kubectl scale deployment nginx --replicas=5
```  

Expose a service:

```   
kubectl expose deployment nginx --port=80
```  

Delete:

```   
kubectl delete pod nginx
```  

Each command performs a specific action immediately.


#### Comparison  

|Feature|Imperative|Declarative|  
|---|---|---|  
|Focus|How to perform an action|What the desired end state is|   
|Uses YAML|Usually no|Yes|  
|Uses kubectl apply|No|Yes|  
|Good for|Quick testing, debugging|Production deployments|  
|Version Control|Difficult|Easy (Git)|   
|Repeatable|Less consistent|Highly repeatable|  
|Drift Detection|Manual|Controllers reconcile automatically|   

## Interview Questions   

- Explain Kubernetes architecture.]()   
- What happens when you run kubectl apply ?

> When I run `kubectl apply -f deployment.yaml`, `kubectl` reads the YAML file and sends it as an API request to the Kubernetes API Server. The API Server authenticates and authorizes the request, validates the resource, and runs admission controllers. If the request is valid, it stores the desired state in `etcd`. The Deployment Controller detects the new Deployment and creates or updates a ReplicaSet. The ReplicaSet ensures the required number of Pods exist. The Scheduler assigns unscheduled Pods to suitable worker nodes. The kubelet on the selected worker node receives the Pod assignment from the API Server and instructs the container runtime (such as `containerd`) to start the containers. Once the containers are running and pass their health checks, the Pod status is updated to Running in the API Server. 

- What is etcd ?

> `etcd` is a distributed key-value store that stores the desired state and configuration of a Kubernetes cluster. It is the backing database for the Kubernetes control plane. 

- Difference between kubelet and kube-proxy.   

|Feature|kubelet|kube-proxy|  
|---|---|---|
|Purpose|Manages Pods and containers|Manages networking for Services|    
|Runs On|Every Worker Node|Every Worker Node|  
|Talks To|kube-apiserver|kube-apiserver|
|Main Responsibility|Ensures Pods are running|Routes network traffic to Pods|   
|Uses|Container Runtime (containerd/CRI-O)|Linux networking (iptables, IPVS, or nftables depending on configuration)|   
|Creates Containers|✅ Yes|❌ No|   
|Load Balancing|❌ No|✅ Yes (for Services)|    












