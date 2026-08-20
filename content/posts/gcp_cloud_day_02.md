---
title: "GCP Cloud : Day 02 - GCP IAM (Identity and Access Management)"  
date: 2026-08-20
categories: ["gcp", "cloud"]
draft: false
---

- Used to **grant granular access** to GCP resources.  
- Ensures the **principle of least privilege** (give only the permissions needed, nothing more).  

### Identities (Who can access)

Identities are **principals** that access resources.

- **Google Accounts** → Individual users ( `ajaykt@evilcorp.com`).
- **Service Accounts** → Identities for applications, services, or VMs. ( `service-automation@landingzone-project.iam.gserviceaccount.com` )
- **Google Groups** → Manage permissions for groups of users. (`landingzone-admins@evilcorp.com`)
- **Cloud Identity / Workspace Domain** → Organization-level accounts.
- **AllAuthenticatedUsers** → Any Google account (not recommended).
- **AllUsers** → Anyone on the internet (public).

### Roles (What they can do)

Roles = Collection of permissions.

> Note: you can't grant a permission to the user directly instead, you grant them a role.  

- **Primitive Roles** (also known as Basic roles, avoid in production):
    - `roles/owner` → Full control
    - `roles/editor` → Read + Write
    - `roles/viewer` → Read-only
- **Predefined Roles**
    - Managed by Google, fine-grained (e.g., `roles/storage.objectViewer`, `roles/bigquery.admin`).
    - Best practice for most use cases.
- **Custom Roles**
    - Created by you with a chosen set of permissions.
    - Useful for least-privilege principle.

> Basic roles include thousands of permission across all Google Cloud Services. In production environments, do not grant basic roles unless there is no alternative, instead grant the most limited predefined roles or custom roles that meet your needs.

### Custom Roles

* Custom roles are user defined roles with a specific set of permissions tailored to the specific needs/use-cases. This gives **least privilege** access instead of granting broad roles.
* Custom roles must include only valid permissions supported by services. The supported permissions list can be found [here](https://cloud.google.com/iam/docs/custom-roles-permissions-support).
* Lifecycle Stages of Custom Roles: Helps with governance of custom roles, teams will know which roles are experimental vs production. You can move roles forward or backward between stages (ALPHA → BETA → GA or GA → DEPRECATED). Default lifecycle will be set to alpha while creating the roles, unless you specify. 
    * ALPHA:
		* Initial creation stage.
		* The role is not ready for production.
		* Only IAM admins who created it should test it.
		* Useful when you’re experimenting with permissions.
	 * BETA:
		 * More stable than ALPHA, but **still experimental**.
		 * May be used in limited environments like **pre-prod** or **staging**.
		 * Signals that the role is **still being tested** before rollout.
	* GA (Generally Available):
		 * Production-ready.
		 * Fully supported and available for assignment across users, groups, and service accounts.
		 * This is the stage you set once you’re confident the role is correct and stable.
	* DEPRECATED :
		- Role is **phased out**.
		- Can no longer be assigned to new members.
		- Existing assignments still work, but you should migrate users to a different role.
		- Useful when permissions in the role are no longer relevant (e.g., service API retired).

### Policy (How access is granted)

- IAM Policy = **bindings of identity + role + resource**
- Example:
    - `user:ajaykt@evilcorp.com` → `roles/storage.objectViewer` → `project:analytics-prod`
- Policies can be attached at **any level**: Organization, Folder, Project, or specific Resource.
- Policies are **hierarchical and inherited**:
    - Assign at Org → applies to all Folders/Projects/Resources
    - Assign at Folder → applies to its Projects/Resources
    - Assign at Project → applies to all resources in that project
    - Can override/inherit at lower levels
> Note: Policies are always global in definition, but scoped to where they're attached in the hierarchy. 

### Service Accounts

- **Special type of identity for apps/services**
- Used by:
    - VMs (Compute Engine default service account)
    - Cloud Functions, Cloud Run, GKE workloads
- Types:
    - **User-managed service accounts** (created by you)
    - **Default service accounts** (auto-created by GCP, should often be replaced with least privilege SA)
- Service Accounts are project-scoped resources, not global, but they act like principals globally once created, you can grant this service account IAM roles in other projects or even at the organization level. Example: a service account from Project-A can be given permissions in Project-B (crossproject access). Note: to do the same create a Service Account and grant permission in org level. 

👉 Best practice: **Workload Identity Federation** for hybrid/multi-cloud (instead of long-lived keys).

### IAM Best Practices (Exam-Important)

- Use **principle of least privilege** → assign predefined/custom roles instead of primitive roles.
- Use **groups** instead of individual users for easier management.
- Avoid using **service account keys** (JSON files) → use Workload Identity Federation.
- Apply **Org Policies** to restrict risky actions (e.g., restrict VM external IPs, enforce CMEK).
- Monitor IAM changes via **Cloud Audit Logs**.

```
{
  "bindings": [
    {
      "role": "roles/storage.objectViewer",
      "members": [
        "user:alice@company.com",
        "group:dev-team@company.com",
        "serviceAccount:app-sa@project-id.iam.gserviceaccount.com"
      ]
    }
  ]
}
```

### IAM / Policy inheritance (Org → Folder → Project → Resource)

{{< mermaid >}}
flowchart TD
  %% Structure
  A[Organization: company.com]
  A --> F1[Folder: Finance]
  A --> F2[Folder: Engineering]

  F1 --> P1[Project: finance-prod]
  F1 --> P2[Project: finance-dev]

  F2 --> P3[Project: eng-prod]
  F2 --> P4[Project: eng-dev]

  P1 --> R1[Resource: BigQuery dataset]
  P2 --> R2[Resource: Cloud SQL]
  P3 --> R3[Resource: GKE cluster]
  P4 --> R4[Resource: VM instance]

  %% Policy inheritance (dashed arrows with labels)
  A -.->|Org policies / Org IAM| F1
  A -.->|Org policies / Org IAM| F2

  F1 -.->|Folder IAM / Policies| P1
  F1 -.->|Folder IAM / Policies| P2
  F2 -.->|Folder IAM / Policies| P3
  F2 -.->|Folder IAM / Policies| P4

  P1 -.->|Project IAM| R1
  P2 -.->|Project IAM| R2
  P3 -.->|Project IAM| R3
  P4 -.->|Project IAM| R4

  %% Example binding notes (simple nodes with line breaks)
  N1["Org binding:<br>role=orgViewer<br>members=security-team@company.com"]
  N2["Folder binding:<br>role=storage.viewer<br>members=group:finance@company.com"]
  N3["Project binding:<br>role=compute.admin<br>members=user:ops@company.com"]
  N4["Resource binding:<br>role=bigquery.dataEditor<br>members=serviceAccount:etl-sa@project.iam.gserviceaccount.com"]

  A --- N1
  F1 --- N2
  P3 --- N3
  R1 --- N4

  classDef note fill:#f9f,stroke:#333,stroke-width:1px;
  class N1,N2,N3,N4 note
{{< /mermaid >}}

### GCP Identities with Scope 

| Principal                    | Scope  |
| ---------------------------- |---|
| Users                        | Global |
| Groups                       | Global |
| Service Accounts             | Project scoped resorces, but once created, but, once created, you can grant this service account IAM roles in other projects or even at the organization|
| Cloud Identity               | Global |
| AllUsers/Authenticated Users | Global |

> Note: Principals are always global.


Task: 
    - CLI Command Example:
        - Assign roles to users/service-accounts/groups 
        - Create custom roles 
        - Use gcloud auth with SA
    - Terraform
        - Write modules for roles assignmment to iam grops
        - Write modules to create custom roles  
        - Modules to create Service Account 
        - Example to use these modules
 

