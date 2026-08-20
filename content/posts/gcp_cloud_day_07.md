---
title: "GCP Cloud : Day 07 - GCP Virtual Private Cloud (VPC)"
date: 2026-08-20
categories: ["gcp", "cloud"]
draft: false
---

- A VPC (Virtual Private Cloud) is a private virtual network inside GCP where you deploy resources like VM instances, GKE clusters, Load balancers, Cloud SQL, Serverless connectors etc. 
- It gives Isolation, Routing, Firewalling, Hybrid connectivity, Internal communication. 
- GCP VPC is a global resources. 

## Core Components of GCP VPC 

1. VPC Network 

VPC is a main logical container which holds all the networking related resources. A project can have multiple VPCs. 

```
Project
├── prod-vpc
├── dev-vpc
└── shared-services-vpc
``` 

| Property  | Value                   |
| --------- | ----------------------- |
| Scope     | Global                  |
| Isolation | Logical                 |
| CIDR      | Defined at subnet level |


### Types of VPC in GCP 

- Auto Mode VPC

Google creates subnets automatically in every region. Example:

```   
default-vpc
├── us-central1
├── asia-south1
├── europe-west1
```  

Good for testing, learning, not recommended for production.  

- Custom Mode VPC 

In custom VPC user define everything. Example 

```  
prod-vpc
├── app-subnet
├── db-subnet
└── management-subnet
```  

This type of VPC is best for production.   

2. Subnets

Subnets are where IP ranges are assigned.  

```
prod-vpc
├── us-central1-subnet → 10.10.0.0/24
├── asia-south1-subnet → 10.20.0.0/24
└── europe-west1-subnet → 10.30.0.0/24
```  

- VPC are Regional resources.  
- Can expand CIDR.  
- Multiple subnets in one VPC.   

3. Routes 

Routes decide how packets travel.

Types:

- System Routes (Automatic)

Created automatically:

```   
10.10.0.0/24 → local subnet
10.20.0.0/24 → local subnet 
0.0.0.0/0 → internet gateway
```  

- Custom Routes  

Used for VPN, NAT appliances, Custom next hops.   

4. Firewall Rules

Firewall Rules control inbound and outbound traffic. Rules apply at Network level, Target tags, Service accounts.   


| Property         | Value                                      |
| ---------------- | ------------------------------------------ |
| Scope            | VPC-level                                  |
| Stateful         | Yes                                        |
| Direction        | Ingress / Egress                           |
| Priority-based   | Yes                                        |
| Applied to       | VM instances (via tags/service accounts)   |
| Default behavior | Implied deny ingress, implied allow egress |

Types of Firewall Rules:  

- Ingress Rules (Incoming traffic) : Controls traffic coming into a VM.  

Example: Allow SSH from your office IP.

```   
Source: 203.0.113.10/32
Protocol: TCP
Port: 22
Action: Allow
``` 

Flow:

```  
Internet → VM
```  

- Egress Rules (Outgoing traffic) : Controls traffic leaving a VM.   

Example: Allow HTTPS to internet.  

```   
Destination: 0.0.0.0/0
Protocol: TCP
Port: 443
Action: Allow
```   

Flow:

```   
VM → Internet  
```  

