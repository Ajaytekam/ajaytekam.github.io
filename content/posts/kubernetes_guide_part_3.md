---
title: "Kubernetes Part 3 : Networking and Service Discovery 
date: 2026-08-10
categories: ["kubernetes", "cloud", "infrastructure"]
draft: true
---

## Topics 

- [Cluster Networking](#cluster-networking)   
- [Pod Networking](#pod-networking)   
- [CNI](#cni)   
- [Popular CNIs](#Popular-cnis) 
    - [Calico](#calico)   
    - [Cilium](#cilium)   
    - [Flannel](#flannel)    
- [Sevices](#services)    
    - [ClusterIP](#clusterip)   
    - [NodePort](#nodeport)   
    - [LoadBalancer](#loadbalancer)  
    - [Externalname](#externalname)  
- [DNS](#dns)  
- [CoreDNS](#coredns)   
- [Ingress](#ingress)   
- [Ingress Controller](#ingress-controller)    
- [Gateway API](#gateway-api)   
- [Network Policies](#network-policies)  
- [Service Mesh Basics](#Service-mesh-basics)   
    - [Istio](#istio)  
    - [Linkered](#linkered)

## Hands-On  

- Deploy Service
- Access via NodePort
- Install Nginx Ingress
- Network policy  

## Interview Questions 

- How does one pod talk to another?
- Difference
    - ClusterIP
    - NodePort
    - Ingress 
    - LoadBalancer


## Cluster Networking   

## Pod Networking
