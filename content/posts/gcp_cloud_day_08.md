---
title: "GCP Cloud : Day 08 - GCP Advanced Networking & Enterprise Connectivity Patterns"
date: 2026-08-20
categories: ["gcp", "cloud"]
draft: false
---

In enterprise cloud architectures, networking is the backbone for security, performance, scalability, and hybrid integration. Building upon foundational VPC concepts, this guide dives deep into advanced routing, security policies, private access mechanisms, hybrid interconnectivity, and battle-tested enterprise architectures.

---

## 1. Network Security & Firewall Architecture

GCP provides a distributed, software-defined firewall system implemented at the hypervisor level (Andromeda). There is no single appliance bottleneck—every VM packet is filtered before leaving or entering the virtual NIC.

### 1.1 Firewall Hierarchy & Types

GCP has evolved from legacy VPC firewall rules to a unified **Hierarchical Firewall Policy** model:

{{< mermaid >}}
flowchart TD
    Org[Organization Firewall Policy] -->|Inherited down| Folder[Folder Firewall Policy]
    Folder -->|Inherited down| GlobalFP[Global / Regional Network Firewall Policy]
    GlobalFP -->|Applied to VPC| VPC[VPC Firewall Rules - Legacy]
    VPC --> VM[Compute Engine / GKE Nodes]
{{< /mermaid >}}

| Firewall Policy Level | Scope | Managed By | Use Case |
| :--- | :--- | :--- | :--- |
| **Organization Policy** | All projects in Org | SecOps / Central IT | Enforce mandatory rules (e.g., Block port 22/3389 from `0.0.0.0/0`) |
| **Folder Policy** | Projects in a Folder | Environment Admins | Enforce environment rules (e.g., Prod vs Non-Prod restrictions) |
| **Global Network Policy** | Across all regions in VPC | Network Team | VPC-wide consistent policies, ingress/egress filtering |
| **Regional Network Policy**| Specific Region in VPC | Region / App Team | Region-specific compliance or micro-segmentation |
| **VPC Firewall Rules** | Single VPC | Project Owners | Traditional VPC-level rules (target tags / service accounts) |

---

### 1.2 Target Filtering: Network Tags vs Service Accounts vs Secure Tags

In GCP, security grouping and micro-segmentation are achieved through three primary mechanisms:

{{< mermaid >}}
flowchart LR
    subgraph Network_Tags["Network Tags (Strings)"]
        T1[web-server] --> T2[Easy to set, but project-scoped & no IAM controls]
    end
    subgraph Service_Accounts["Service Accounts (Identities)"]
        S1[sa-backend@...] --> S2[IAM-controlled, secure micro-segmentation]
    end
    subgraph Secure_Tags["Secure Tags (Resource Manager)"]
        ST1[env: prod] --> ST2[Org-level governance, fine-grained RBAC]
    end
{{< /mermaid >}}

| Feature | Network Tags | Service Accounts | Secure Tags (Resource Manager) |
| :--- | :--- | :--- | :--- |
| **Type** | Plain text string (`web`, `db`) | IAM Service Account email | Key-Value pairs bound at Org/Project |
| **Access Control** | Anyone with `compute.instances.update` | Restricted via `iam.serviceAccountUser` | Restricted via `resourcemanager.tagAdmin` |
| **Security Level** | Low (developers can add tags) | High (identity-based authorization) | Enterprise Grade (governed centrally) |
| **Evaluation** | Match target tag to source tag | Match target SA to source SA | Match secure tag bindings |

> [!TIP]
> **Production Best Practice:** Never use plain **Network Tags** for critical security boundaries in production. Use **Service Accounts** or **Secure Tags** to prevent privilege escalation by VM administrators.

---

## 2. GCP Routing & Route Hierarchy

GCP VPC routing is global, distributed, and software-defined.

{{< mermaid >}}
flowchart TD
    Packet[Incoming / Outgoing Packet] --> LPM[1. Longest Prefix Match - Most Specific CIDR wins]
    LPM --> Priority[2. Priority - Lower value = Higher precedence]
    Priority --> RouteType[3. Route Type Tie-breaker]
    
    RouteType --> Subnet[Subnet Route - Cannot be overridden]
    RouteType --> Dynamic[Dynamic / BGP Route]
    RouteType --> Static[Custom Static Route]
{{< /mermaid >}}

### 2.1 Types of Routes

1. **Subnet Routes (System-Generated):**
   - Automatically created for every subnet CIDR in the VPC.
   - Highest priority; **cannot be overridden** by custom static or dynamic routes.
2. **Default Route (`0.0.0.0/0`):**
   - Points to the `default-internet-gateway`. Enables outbound internet access for VMs with public IPs.
3. **Custom Static Routes:**
   - User-defined next hops: Gateway, VM Instance IP, Internal Load Balancer (ILB), or VPN Tunnel.
4. **Policy-Based Routes (PBR):**
   - Route traffic based on attributes other than destination IP (e.g., protocol, source IP, destination port). Commonly used to send specific egress traffic to Next-Generation Firewalls (NGFW).
5. **Dynamic Routes (BGP):**
   - Learned automatically via Cloud Router running BGP over Cloud VPN or Cloud Interconnect.

---

## 3. Private Google Access (PGA)

Allows VM instances that **only have private internal IP addresses** (no external/public IP) to securely access Google APIs and Services (e.g., Cloud Storage, BigQuery, Secret Manager) without crossing the public internet.

{{< mermaid >}}
sequenceDiagram
    autonumber
    participant VM as Private VM (10.0.1.5 - No Public IP)
    participant VPC as VPC Subnet (PGA Enabled)
    participant DNS as Cloud DNS / Virtual IP
    participant API as Google Cloud APIs (Cloud Storage / BigQuery)

    VM->>VPC: Request to storage.googleapis.com
    VPC->>DNS: Resolve DNS (returns VIP: 199.36.153.8/30)
    DNS-->>VPC: Private Route via Andromeda Hypervisor
    VPC->>API: Secure internal access to Google API
{{< /mermaid >}}

### 3.1 PGA Requirements & DNS Endpoints

- **Subnet Level:** Enable `privateIpGoogleAccess = true` on the subnet.
- **Route Requirement:** A default route (`0.0.0.0/0`) or custom route to `default-internet-gateway` is required (traffic stays on Google's internal backbone, never traverses public internet).
- **DNS Domains:**

| DNS Domain | IP Range | Description |
| :--- | :--- | :--- |
| **Default** | Public Google VIPs | Default behavior if no custom DNS configured. |
| **`private.googleapis.com`** | `199.36.153.8/30` | Supports all Google APIs; reachable from on-premises via VPN/Interconnect. |
| **`restricted.googleapis.com`** | `199.36.153.4/30` | Supports only **VPC Service Controls (VPC-SC)** supported services. Blocks data exfiltration risks. |

---

## 4. Cloud NAT (Network Address Translation)

Cloud NAT is a managed, software-defined, distributed NAT solution. It does **not** rely on proxy VMs or single points of failure.

{{< mermaid >}}
flowchart LR
    subgraph Private_VPC["Private Subnet (No External IPs)"]
        VM1["VM 1 (10.0.1.2)"]
        VM2["VM 2 (10.0.1.3)"]
    end

    subgraph GCP_Managed["Cloud NAT (Software Defined)"]
        NAT["Cloud NAT Gateway + Cloud Router"]
    end

    subgraph Internet["Public Internet"]
        ExtAPI["External API / Patch Repo / GitHub"]
    end

    VM1 -->|Outbound Egress Only| NAT
    VM2 -->|Outbound Egress Only| NAT
    NAT -->|Translates to NAT Public IP| ExtAPI
    ExtAPI -.->|Direct Inbound Denied| NAT
{{< /mermaid >}}

### 4.1 Cloud NAT Flavors

1. **Public Cloud NAT:**
   - Translates internal IPs to external Google-managed or user-reserved static public IPs.
   - **Outbound-only:** Allows private VMs to download updates, pull container images, or call third-party APIs.
   - **Inbound connections are strictly blocked.**
2. **Private Cloud NAT (Inter-VPC / Hybrid NAT):**
   - Translates internal RFC 1918 IPs to other internal RFC 1918 IPs.
   - Solves **overlapping IP address space** during M&A (Mergers & Acquisitions) or cross-VPC communication.

> [!IMPORTANT]
> **Port Exhaustion Mitigation:** Configure minimum ports allocated per VM instance (default is 64) and enable dynamic port allocation to avoid packet drops under heavy concurrent egress traffic.

---

## 5. Private Service Connect (PSC) vs Private Services Access (PSA)

When connecting VPCs to Managed Services (Cloud SQL, Memorystore, Vertex AI, or Third-Party SaaS), Google provides two main technologies:

{{< mermaid >}}
flowchart TD
    subgraph PSA_Model["1. Private Services Access (PSA) - Peering Based"]
        VPC1["Consumer VPC"] <-->|VPC Network Peering| ProducerVPC["Google Managed Service VPC"]
        Note1["Requires reserved /24 non-overlapping CIDR block. Transitive routing not supported."]
    end

    subgraph PSC_Model["2. Private Service Connect (PSC) - Endpoint / VIP Based"]
        VPC2["Consumer VPC"] -->|10.0.1.50 Private Forwarding Rule| PSC_EP["PSC Endpoint / ILB"]
        PSC_EP -.->|Unidirectional NAT Encapsulation| ProducerService["Service Producer Project"]
        Note2["No IP overlap issues. No VPC Peering limits. Granular RBAC."]
    end
{{< /mermaid >}}

### 5.1 Detailed Comparison: PGA vs PSA vs PSC

| Feature | Private Google Access (PGA) | Private Services Access (PSA) | Private Service Connect (PSC) |
| :--- | :--- | :--- | :--- |
| **Architecture** | Hypervisor routing to Google APIs | VPC Network Peering to Service VPC | Private Forwarding Rule (ILB) / Endpoint |
| **Supported Services** | Google APIs (GCS, BigQuery, etc.) | Cloud SQL, Memorystore, Redis | Google APIs, Managed Services, Custom SaaS, 3rd Party |
| **IP Overlap Risk** | None | High (requires allocated non-overlapping range) | **Zero (consumer picks any IP in its own subnet)** |
| **VPC Peering Limits** | None | Consumes VPC Peering slots (Max 25 peerings) | **Does not use VPC Peering** |
| **Directionality** | Outbound to Google APIs | Bidirectional Peering | **Strictly Unidirectional (Consumer -> Producer)** |
| **Current Best Practice** | Standard for Google APIs | Legacy for Cloud SQL/Redis | **Recommended for all modern architectures** |

---

## 6. Hybrid Connectivity: Cloud VPN (Classic vs HA VPN)

Cloud VPN securely connects your on-premises network or other cloud providers (AWS, Azure) to your GCP VPC via an IPsec VPN tunnel.

{{< mermaid >}}
flowchart LR
    subgraph GCP["Google Cloud Platform"]
        CR["Cloud Router (BGP ASN 65001)"]
        HAVPN["Cloud HA-VPN Gateway"]
        CR --- HAVPN
        HAVPN -->|Tunnel 0: Active| GW0["Interface 0"]
        HAVPN -->|Tunnel 1: Active/Backup| GW1["Interface 1"]
    end

    subgraph OnPrem["On-Premises Datacenter / AWS / Azure"]
        Peer0["Peer VPN Device 1 / Interface 0"]
        Peer1["Peer VPN Device 2 / Interface 1"]
    end

    GW0 <-->|IPsec + BGP| Peer0
    GW1 <-->|IPsec + BGP| Peer1
{{< /mermaid >}}

### 6.1 Classic VPN vs HA VPN

| Feature | Classic VPN (Deprecated for new builds) | HA VPN (High Availability VPN) |
| :--- | :--- | :--- |
| **SLA** | 99.9% availability | **99.99% availability** (when configured with 2 tunnels) |
| **Gateway Interfaces** | Single external IP | **Two external IPs (Interface 0 & Interface 1)** |
| **Routing Protocol** | Static routing or Dynamic (BGP) | **Dynamic BGP Only (Cloud Router required)** |
| **IKE Protocols** | IKEv1 and IKEv2 | **IKEv2 strictly required** |
| **IPv6 Support** | No | Yes (Dual-stack IPv4/IPv6 supported) |

> [!NOTE]
> To qualify for the **99.99% SLA**, you must configure two tunnels: either to two separate peer VPN gateways, or to a single peer gateway with two separate public IP interfaces in different availability zones.

---

## 7. Hybrid Connectivity: Cloud Interconnect

For high-bandwidth, mission-critical enterprise workloads, Cloud Interconnect provides enterprise-grade, low-latency, private physical connections that bypass the public internet entirely.

{{< mermaid >}}
flowchart TD
    subgraph GCP_DC["Google Cloud Global Edge"]
        VPC["Customer VPC"]
        CR["Cloud Router"]
        VLAN1["VLAN Attachment 1"]
        VLAN2["VLAN Attachment 2"]
        VPC --- CR
        CR --- VLAN1 & VLAN2
    end

    subgraph Colocation["Colocation Facility (Equinix / CoreSite)"]
        DED1["Google Edge Router 1"]
        DED2["Google Edge Router 2"]
        VLAN1 --- DED1
        VLAN2 --- DED2
    end

    subgraph OnPrem["Customer On-Premises Router"]
        CustRouter1["Customer Router 1"]
        CustRouter2["Customer Router 2"]
        DED1 <-->|Cross Connect 10G/100G| CustRouter1
        DED2 <-->|Cross Connect 10G/100G| CustRouter2
    end
{{< /mermaid >}}

### 7.1 Interconnect Options Comparison

| Option | Bandwidth | Connection Point | Encryption | Ideal For |
| :--- | :--- | :--- | :--- | :--- |
| **Dedicated Interconnect** | 10 Gbps or 100 Gbps circuits | Direct physical cross-connect at Google Colocation facility | Unencrypted by default (can add MACsec or HA-VPN over Interconnect) | Large enterprises with massive data transfers & strict low-latency needs |
| **Partner Interconnect** | 50 Mbps up to 50 Gbps | Connect via supported service provider (Equinix, Megaport, Tata, AT&T) | Unencrypted by default (can add HA-VPN over Interconnect) | Organizations not present in a Google Colocation facility or needing <10G |
| **Cross-Cloud Interconnect** | 10 Gbps or 100 Gbps | Direct physical link between GCP and AWS / Azure / OCI | Optional MACsec | Multi-cloud enterprise architectures without third-party network brokers |

### 7.2 Interconnect SLA Topologies

- **99.9% SLA (Production):** Requires 2 VLAN attachments in 1 Metropolitan area across 2 separate Edge Routers (EADs).
- **99.99% SLA (Mission Critical):** Requires **4 VLAN attachments** across **2 separate Metropolitan areas** (Dual-region / Dual-location).

---

## 8. Real-World Enterprise Connectivity Patterns

### Pattern 1: Hub-and-Spoke with Centralized Next-Gen Firewall (NGFW) Inspection

For regulated industries (Banking, Healthcare, Defense), all East-West (Spoke-to-Spoke) and North-South (Internet Ingress/Egress) traffic must pass through centralized firewall appliances (Palo Alto, Fortinet, Check Point).

{{< mermaid >}}
flowchart TD
    subgraph Spoke1["Spoke 1: Prod App VPC"]
        AppVM["App Workload (10.10.1.0/24)"]
    end

    subgraph Spoke2["Spoke 2: Analytics VPC"]
        AnalyticsVM["Data Workload (10.20.1.0/24)"]
    end

    subgraph HubVPC["Hub / Transit VPC (Security Core)"]
        ILB_Untrust["Internal Load Balancer (ILB Next Hop)"]
        NGFW1["Firewall Appliance Active"]
        NGFW2["Firewall Appliance Standby"]
        ILB_Untrust --> NGFW1 & NGFW2
    end

    AppVM -->|Route: Next Hop ILB| ILB_Untrust
    NGFW1 -->|Deep Packet Inspection| AnalyticsVM
    NGFW1 -->|Inspected Outbound| CloudNAT[Cloud NAT / Internet Egress]
{{< /mermaid >}}

### Pattern 2: Shared VPC vs VPC Network Peering vs Network Connectivity Center (NCC)

Enterprise multi-project design options:

{{< mermaid >}}
flowchart LR
    subgraph Shared_VPC["Pattern A: Shared VPC"]
        HostProj["Host Project: Central Network Team"]
        HostProj --- Subnet1["Subnet: Dev Team"]
        HostProj --- Subnet2["Subnet: Prod Team"]
    end

    subgraph VPC_Peering["Pattern B: VPC Peering"]
        VPC_A["VPC A"] <-->|No Transitive Routing| VPC_B["VPC B"]
        VPC_B <-->|No Transitive Routing| VPC_C["VPC C"]
    end

    subgraph NCC_Hub["Pattern C: Network Connectivity Center (NCC)"]
        NCCHub((NCC Hub))
        SpokeVPN["HA VPN Spoke"] --- NCCHub
        SpokeIC["Interconnect Spoke"] --- NCCHub
        SpokeVPC["VPC Spoke"] --- NCCHub
    end
{{< /mermaid >}}

| Dimension | Shared VPC | VPC Network Peering | Network Connectivity Center (NCC) |
| :--- | :--- | :--- | :--- |
| **Model** | Single centralized VPC; subnets shared across service projects | Connects two independent VPCs privately | Centralized Transit Hub for WAN, Multi-cloud & VPCs |
| **Administration** | Centralized Network Admins; Decentralized Project IAM | Decentralized or per-project | Centralized orchestration & dynamic route exchange |
| **Transitive Routing** | N/A (Single VPC routing plane) | **Not supported** ($A \leftrightarrow B \leftrightarrow C \implies A \nleftrightarrow C$) | **Supported via BGP dynamic route exchange** |
| **Best For** | Standard organizational structure within single Org | Connecting two independent systems or third-party SaaS | Complex global WAN, hybrid multicloud, and transit routing |

---

### Pattern 3: Hybrid DNS Resolution Architecture

Enterprise DNS requires bidirectional resolution between on-premises Active Directory / BIND DNS and GCP Cloud DNS.

{{< mermaid >}}
flowchart LR
    subgraph GCP_VPC["GCP VPC"]
        VM["GCP Workload (app.gcp.internal)"]
        CloudDNS["Cloud DNS Private Zone"]
        InboundFP["Cloud DNS Inbound Forwarding Policy (10.0.0.100 VIP)"]
        OutboundFWD["Cloud DNS Forwarding Zone (*.corp.local)"]
        
        VM --> CloudDNS
        CloudDNS --> OutboundFWD
    end

    subgraph Hybrid_Pipe["Cloud VPN / Interconnect"]
        Pipe[Encrypted Tunnel / BGP]
    end

    subgraph OnPrem_DC["On-Premises Datacenter"]
        OnPremVM["On-Prem Server"]
        AD_DNS["Enterprise DNS / AD (172.16.1.10)"]
        
        OnPremVM --> AD_DNS
    end

    OutboundFWD -->|Forward queries for *.corp.local| Pipe --> AD_DNS
    AD_DNS -->|Forward queries for *.gcp.internal| Pipe --> InboundFP
{{< /mermaid >}}

- **Inbound DNS Query Flow (On-Prem $\to$ GCP):** Create an Inbound Server Policy in Cloud DNS. GCP provides a private entry point IP in the VPC. On-prem DNS server forwards `*.gcp.internal` queries to this IP.
- **Outbound DNS Query Flow (GCP $\to$ On-Prem):** Create a DNS Forwarding Zone in Cloud DNS for `*.corp.local`. Point destination to on-premises DNS server IPs (`172.16.1.10`).

---

## 9. Hands-On CLI Cheat Sheet

### Configure Cloud NAT
```bash
# 1. Create a Cloud Router in the region
gcloud compute routers create cr-nat-router \
    --network=prod-vpc \
    --region=us-central1

# 2. Create the Cloud NAT gateway
gcloud compute routers nats create nat-gw-us-central1 \
    --router=cr-nat-router \
    --region=us-central1 \
    --auto-allocate-nat-external-ips \
    --nat-all-subnet-ip-ranges \
    --enable-logging
```

### Enable Private Google Access on Subnet
```bash
gcloud compute networks subnets update prod-app-subnet \
    --region=us-central1 \
    --enable-private-ip-google-access
```

### Deploy High Availability (HA) VPN Gateway
```bash
# 1. Create HA VPN Gateway (allocates two public interfaces automatically)
gcloud compute vpn-gateways create ha-vpn-gw \
    --network=prod-vpc \
    --region=us-central1

# 2. Create Cloud Router for BGP route exchange
gcloud compute routers create cr-vpn-router \
    --network=prod-vpc \
    --region=us-central1 \
    --asn=65001
```

---

## 10. Summary & Key Architectural Takeaways

```
┌────────────────────────────────────────────────────────────────────────┐
│                      GCP ADVANCED NETWORKING MATRIX                    │
├───────────────────────┬────────────────────────────────────────────────┤
│ Requirement           │ Recommended Solution                           │
├───────────────────────┼────────────────────────────────────────────────┤
│ Private VM to GCS/BQ  │ Private Google Access (PGA)                    │
│ Private VM to Internet│ Cloud NAT (Outbound Only)                      │
│ Private VM to CloudSQL│ Private Service Connect (PSC)                  │
│ Hybrid 99.99% SLA VPN │ HA VPN (Dual Tunnels + BGP Cloud Router)       │
│ Hybrid 10G/100G Link  │ Dedicated / Partner Cloud Interconnect         │
│ Cross-Project Network │ Shared VPC (Host + Service Projects)          │
│ Multi-VPC Transit Hub │ Network Connectivity Center (NCC) / Transit Hub│
│ Centralized Security  │ Hierarchical Firewall Policies + NGFW Appliance│
└───────────────────────┴────────────────────────────────────────────────┘
```

---

## Day 08 Tasks & Hands-On Exercises

- [ ] **Task 1:** Create a custom VPC with a private subnet (no public IPs) and verify internet access is blocked.
- [ ] **Task 2:** Attach a Cloud NAT Gateway and verify that the private VM can successfully execute `curl -I https://google.com` and run `apt-get update`.
- [ ] **Task 3:** Enable Private Google Access (PGA) on the subnet and verify private access to Cloud Storage via `gsutil ls` without leaving the Google backbone.
- [ ] **Task 4:** Create a Private Service Connect (PSC) endpoint to access Google APIs or a managed service instance.
- [ ] **Task 5:** (Optional/Lab) Configure an HA VPN tunnel pair between two VPCs using Cloud Router and BGP session peering.

