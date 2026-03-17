---
title: "Azure Cloud : Day 01 - Azure global Infrastructure"
date: 2026-03-15
categories: ["azure", "cloud"]
draft: false
---

### Azure Regions: 

- A set of data-centers deployed within a defined, low-latency perimeter, providing global, high-availability infrastructure for hosting applications and data. 
- A region contains one or more, often multiple, physically separated datacenters.
- Regions are connected by a dedicated, high-capacity, low-latency network. 
- Azure has 70+ regions. globally as of 2026. 
- Common azure region examples: 
	-  **America:** East US, West US, Central US, Brazil South, Canada Central, Mexico Central.
	- **Europe:** North Europe, West Europe, Germany West Central, France Central, UK South, Sweden Central.
	- **Asia Pacific:** East Asia, Southeast Asia, Japan East, Korea Central, Australia East, Central India, Malaysia West.
	- **Middle East & Africa:** UAE North, Qatar Central, South Africa North.

### Availability Zones

 Within a region, Availability Zones offer separate physical datacenters with their own power, cooling, and networking to protect against local failures.

![Azure Region](/images/azure_cloud_day_1_to_20_series/azure_cloud_01_1.png)

### Paired Regions

To ensure high availability and disaster recovery, Azure pairs most regions with another region within the same geography (e.g., East US and West US) to provide redundancy. For example: 

| Primary Region | Paired (Secondary) Region |
| -------------- | ------------------------- |
| East Us        | West US                   |
| Central India  | South India               |
| Noth Europe    | West Europe               |
Benefits:
- Data replication
- Updates rolled out one region at a time
- Disaster recovery planning

![Azure Paired Region](/images/azure_cloud_day_1_to_20_series/azure_cloud_01_2.png)

### Edge Zones

- An **Edge Zone** is a **localized extension of an Azure region that brings Azure services closer to users**.
- Instead of processing requests in a faraway region, workloads can run **near the edge of the network**. This reduces the network distance between users and applications. 

```
User Device --> Edge Zone (Near city) --> Azure Region
```

### Availability Set, Fault Domain and Update Domain

- The servers in a datacenter are divided into multiple physical and logical groups. The physical grouping is called fault domain and the logical grouping is called update domain.
- ̌You can think of each rack of servers with it's own power supply and network switch as one fault domain. So, if there are 10 racks of server in a datacenter, it's like you have 10 different fault domains.
- An update domain is a group of servers that can be updated and rebooted at the same time. from time to time, server patches and software updates need to be applied. Some updates require servers to be rebooted.  Only one update domain is rebooted at a time. A rebooted update domain is then given 30 minutes to recover before maintenance is initiated on a different update domain.
- An availability set is a concept with in a datacenter and it is made up of multiple fault domains and update domains.

![Azure Availability Set](/images/azure_cloud_day_1_to_20_series/azure_cloud_01_3.jpg)

### Azure SLA Model 

Microsoft Azure SLA (Service Level Agreement) defines the guaranteed uptime and availability of a service. Azure provides different SLA models based on how you deploy resources. Below are the main Azure SLA models used in real architectures.

#### 1. Single instance SLA 

- This is the lowest availability model.
- A single VM or service instance
- No redundancy
- If the instance fails → application goes down
- Example : 

```
1 VM
1 Availability zone
```

- SLA is around 99.9 % uptime. 
- Architecture

{{< mermaid >}}
flowchart TD

A[User] --> B[Single VM]
{{< /mermaid >}}

#### 2. Availability Set SLA

- An Availability Set spreads VMs across different fault and update domains.
- Azure ensures VMs are distributed across:
	- **Fault Domains (FD)** → Different physical racks
	- **Update Domains (UD)** → Different maintenance groups
- Example 
```
VM1 --> Fault Doamin 1
VM2 --> Fault Doamin 2
```
* Typical SLA is 99.95 % uptime
* Architecture 

{{< mermaid >}}
flowchart TD
A[Load Balancer] -->|FD1/UD1| B(VM1)
A --> |FD2/UD2| C(VM2)
{{< /mermaid >}}

#### 3. Availability Zones SLA

- Availability Zones are physically separate datacenters inside a region.
- Example
```
VM in Zone 1
VM in Zone 2
VM in Zone 3
```
- Typical SLA is 99.99 % uptime. 
- Architecture 

{{< mermaid >}}
flowchart TD
A[Load Balancer] -->|Zone 1| B(VM)
A --> |Zone 2| C(VM)
A --> |Zone 3| D(VM)
{{< /mermaid >}}

#### 4. Multi-Region SLA 

- This is the highest availability model.
- Applications run in multiple Azure regions.
- Example 
```
Primary: East US
Secondary: West Europe
```
* Typical SLA is 99.999 % uptime.
* Architecture 

{{< mermaid >}}
flowchart TD
A[Global Load Balancer] -->|Region A| B(Primary)
A --> |Region B| C(Disaster Recovery)
{{< /mermaid >}} 
