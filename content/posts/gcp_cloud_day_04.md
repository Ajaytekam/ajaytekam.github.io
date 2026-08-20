---
title: "GCP Cloud : Day 04 - gcloud Auth Commands"
date: 2026-08-20
categories: ["gcp", "cloud"]
draft: false
---

### gcloud cli notes

* Authorize with your user login 

```
gcloud auth login 
```

it will redirect to a browser 

* Authorize without browser for serverless login 

```
gcloud auth login --no-launch-browser
```

This will print a url, which you have to open in a browser and provide your credentials and then it will generate a uid, copy it and put it in the prompt. 

> Also add something with regards to default login


* Authorize with Service Account

```
gcloud auth activate-service-account landingzone-automation@landingzone-473412.iam.gserviceaccount.com --key-file=/Users/ajaykt/landingzone-473412-4b24e5c26289.json
```

* list all the authorize accounts 

```
gcloud auth list 
```

It outputs like 

```
                          Credentialed Accounts
ACTIVE  ACCOUNT
        ajaytekam@cloudii.shop
        akshayt@cloudii.shop
        dvkantb@cloudii.shop
*       landingzone-automation@landingzone-473412.iam.gserviceaccount.com
```

Shows that the `landingzone-automation` service account is selected. 

* Change the account 
```
gcloud config set account `ACCOUNT_NAME`
```

example 

```
gcloud config set account akshayt@cloudii.shop
```

* Setup the working project for service account 

```
gcloud config set project PROJECT_ID
```

* Revoke the authorization for an account 

when you logged-in to a single account 

```
gcloud auth revoke 
```

when you logged-in to multiple accounts 

```
gcloud auth revoke dvkantb@cloudii.shop
```
