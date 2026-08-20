---
title: "GCP Cloud : Day 03 - Organization Policies"
date: 2026-08-20
categories: ["gcp", "cloud"]
draft: false
---

Organization Policies are part of Google Cloud Resource Manager and are used to enforce governance rules across in organization, folders, or projects

* An Org Policy defines constraints that control what resources can be created and how they behave.
* They help enforce security, compliance, and governance across your Google Cloud hierarchy.
* They apply at different levels:
	* Organization (root) → affects everything inside.
	* Folder → affects all projects/folders inside it.
	* Project → affects only that project.
* Policies inherit down the hierarchy.
* ORG policies are managed centrally by Org Administrators. 
* Example: 
	* `constraints/iam.managed.disableServiceAccountKeyCreation` disable the Service Account key creation. 
	* `constraints/compute.disableSerialPortAccess` disable serial port access. 

Task:
    - Commandline
        - Enable/disable Org Policies on all Resource level
        - Set custom allow/deny policies
    - Terraform
        - Do the same in GCP
