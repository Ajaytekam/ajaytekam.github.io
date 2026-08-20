---
title: "GCP Cloud : Day 06 - GCP Billing"  
date: 2026-08-20
categories: ["gcp", "cloud"]
draft: false
---

In GCP, billing is managed separately from resources. Resources live in Projects, but charges are accumulated in a Billing Account.

```
Organization
│
├── Folder
│   └── Project A
│
├── Folder
│   └── Project B
│
└── Billing Account
     ├── Project A
     └── Project B
```

> A project can only be linked to one billing account at a time, but a billing account can be linked to multiple projects.

- Multiple Projects, One Billing Account: Common in organizations. 

```
Billing Account
│
├── ecommerce-dev
├── ecommerce-stage
├── ecommerce-prod
└── shared-services
```

- Multiple Billing Accounts: large organizations often separate costs. 

```
Organization
│
├── Engineering Billing
│   ├── Project A
│   └── Project B
│
└── Marketing Billing
    ├── Project C
    └── Project D
```


### Billing Account 

A Billing Account is where Google charges for resource usage.

It contains:

- Payment method (credit card, bank account, invoicing)
- Billing reports
- Budgets and alerts
- Cost exports
- Payment history

Example:

```
Billing Account
Name: Production Billing
ID: 012345-6789AB-CDEF12
```

### Project Charges 

Every billable resource belongs to a project. Google calculates the cost of each resource and charges the linked billing account. The amount appears under the project's billing report.

```
Project: ecommerce-prod
├── VM Instances
├── Cloud SQL
├── Load Balancer
└── Storage Bucket
```

### Budgets and Alerts

You can set spending limits and notifications. Example:

```
Budget: ₹10,000/month

50% -> Email alert
80% -> Email alert
100% -> Email alert
```

Note:
    - Budgets do not automatically stop resources.
    - They only send alerts unless you create automation to act on them.

### Labels and Cost Allocation

Projects and resources can be tagged with labels. Example:

```
environment=production
team=backend
application=ecommerce
``` 

This allows billing reports like:

```
  Team	    Monthly Cost
Backend	      ₹25,000
Frontend	  ₹10,000
Data Team	  ₹15,000
```

### Billing Export to BigQuery

For detailed cost analysis, export billing data BigQuery. 

Example queries:

```
Cost by project
Cost by service
Cost by label
Daily spending trends
```

Many FinOps teams use BigQuery for custom dashboards.

### Key Rules 

- Key Rules to Remember
- Resources live in Projects.
- Projects are charged through a Billing Account.
- One Project → One Billing Account.
- One Billing Account → Many Projects.
- Organizations and Folders do not receive charges directly; costs roll up from Projects.
- Budgets alert you but do not automatically stop spending.
- Labels and BigQuery exports are essential for tracking costs in larger environments.
