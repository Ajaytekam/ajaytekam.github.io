---
title: "GCP Cloud : Day 01 - Workload Identity Federation"
date: 2026-08-20
categories: ["gcp", "cloud"]
draft: false
---

Workload Identity Federation (WIF) is a mechanism that allows external workloads (GitHub Actions, GitLab CI/CD, Azure, AWS, on-premises servers, Kubernetes clusters, etc.) to access cloud resources without storing long-lived service account keys.

Traditionally, to allow an external application to access GCP, you need:

1. Create a Service Account.
2. Generate a JSON key.
3. Store the key in GitHub Secrets, Jenkins, or a VM.
4. Use the key for authentication.

Example:

```    
flowchart TD
    A(Github Actions) -->|uses service-account-key.json| B(Google Cloud APIs)
```  

But the above implementation comes with below risks: 

* Keys can be leaked.
* Keys must be rotated.
* Keys often live for years.
* Anyone with the key can impersonate the service account.

Now with workflow identity federation, the above risks can be avoided. 

* External system authenticates with its own identity.  
* GCP trusts that identity provider.  
* GCP exchanges the external token for short-lived credentials.  
* External workload accesses GCP resources.   

```   
flowchart TD
    A(Github Functions) -->|OIDC Token| B(Workload Identity Pool)
    B --> |Impersonate Service Account| C(Google Cloud APIs)
```   

## Components in GCP 

1. Workload Identity Pool

A logical container that holds trusted external identities.

Example:

```  
my-github-pool
``` 

2. Identity Provider

Defines where the identities come from.  

Examples:

- GitHub OIDC
- GitLab OIDC
- AWS IAM
- Azure AD
- Kubernetes OIDC

Example:

```  
github-provider
```  

3. Service Account

The identity that ultimately gets GCP permissions.

Example:

```  
terraform-deployer@myproject.iam.gserviceaccount.com
``` 

4. IAM Binding

Allows identities from the pool to impersonate the service account.

Example:

```   
roles/iam.workloadIdentityUser
```  

### Comparison 

| Feature              | Service Account Key | Workload Identity Federation |
|----------------------|---------------------|------------------------------|
| Long-lived secrets   | Yes                 | No                           |
| Key rotation needed  | Yes                 | No                           |
| Risk of key leakage  | High                | Low                          |
| Uses OIDC/SAML       | No                  | Yes                          |
| Temporary credentials| No                  | Yes                          |
| Recommended by GCP   | No                  | Yes                          |

Task: 
    - Create Workload Identity Federation for Github Actions
    - Impersonate an SA as WIF 
    - Terraform
        - Write modules for the same
        - Write a test example

