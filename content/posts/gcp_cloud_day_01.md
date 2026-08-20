---
title: "GCP Cloud : Day 01 - GCP Resource Hierarchy (Organization, Folders, Projects)"    
date: 2026-08-20
categories: ["gcp", "cloud"]
draft: false
---

1. Organization:
     - The **top-level container** for all GCP resources.
     - Usually mapped to a company’s **domain** in Google Workspace or Cloud Identity (e.g., `example.com`).
     * You only have **one Org node** per domain.
     * Policies applied here **flow down to all folders, projects, and resources**.****
     * Example: `Org = company.com`
2. Folders:
	* ***Optional layer** used to group projects.
	* Can represent **business units, departments, or environments** (e.g., Finance, R&D, Prod, Dev).
	- Supports **nested folders** (hierarchies within business units).
	- Useful for **delegating admin roles** or **enforcing org policies** at a department level.
	- Example: 
		- Org --> Folder: `Finance`
		- Org --> Folder: `Engineering`
3. Projects:
	- The **base unit of organization** in GCP.
	- **Required** for any resource (you cannot create a VM, bucket, DB without a project).
	- Contains:
	    - **Resources** (VMs, buckets, databases, etc.)
	    - **APIs & Services** enabled
	    - **Billing association**
	    - **IAM policies** specific to the project
	- Projects are identified by:
	    - **Project Name** (human-readable)
	    - **Project ID** (unique, immutable)
	    - **Project Number** (unique, system-generated)
	👉 Example:
		- Folder: `Engineering` --> Project: `eng-prod`
		- Folder: `Engineering` --> Project: `eng-dev`	
4. **Resources**
	- The actual **services you create and use**.
	- Examples:
	    - Compute Engine VM
	    - Cloud Storage bucket
	    - BigQuery dataset
	    - GKE cluster
	- Resources inherit IAM policies & org policies **from the project, folder, and organization**.
	👉 Example:
		- Project: `eng-prod` → Resource: `VM instance`
		- Project: `finance-prod` → Resource: `BigQuery dataset`

{{< mermaid >}}
flowchart TD
    A[Organization: company.com] --> B[Folder: Finance]
    A --> C[Folder: Engineering]

    B --> D[Project: finance-prod]
    B --> E[Project: finance-dev]

    C --> F[Project: eng-prod]
    C --> G[Project: eng-dev]

    D --> H[Resource: BigQuery dataset]
    E --> I[Resource: Cloud SQL]
    F --> J[Resource: GKE cluster]
    G --> K[Resource: VM instance]
{{< /mermaid >}}

 Task:  
    - Create User 
    - CLI Command
        - Create Admin Group
        - Create Service Account and generate API key
        - Commands to create folders/projects 
    - Write terraform code/modules for the same
