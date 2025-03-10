---   
title: Setting up Development environment with virtualenv
author: ajaytekam   
date: 2023-06-28 03:25:00 +0530   
img_path: /assets/posts/20230628/   
categories: [DevOps, Python, Development]    
tags: [python, virtualenv]  
image:
  path: virtualenv.png   
  alt: Setting up Development environment with virtualenv
---    

Virtualenv is a Python module which helps to create isolated Python environments for our scripting experiments, which creates a folder with all necessary executable files and modules for a basic Python project.    

Virtual environments help to separate dependencies required for different projects, by working inside a virtual environment it also helps to keep our global site-packages directory clean.   

But before that we have to install pip3 package. Pip is a package management system used to install and manage software packages written in Python. pip3 is a python3 package manager.    

## Pip3 Installation  


```shell  
apt install python3-pip 
```  

Now install virtualenv  

```shell  
pip3 install virtualenv 
```    

## Usage  

* Create folder project where you can write your code  

```shell  
mkdir mycode
cd mycode  
```   

* Now create virtualenv environment files inside project folder  

```shell   
virtualenv name-of-virtual-environment
```  

```shell  
virtualenv venv  
```   

* To create a virtual environment with a particular python version, use below command  

```shell  
virtualenv -p python2.7 venv
```  

* Activate the virtual environment by  

```shell  
source venv/bin/activate  

// or 

. venv/bin/activate  
```  

Now you can install any dependencies and use it, which is isolated to this configuration only. To install new packages you can use `pip` or `pip3` etc on this environment. For example 

```shell  
pip3 install package_name
```  

* Exit from the virtualenvironment, type  

```shell   
deacticate
```  
