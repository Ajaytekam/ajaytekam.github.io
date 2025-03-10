---   
title: Yaml file Basics for Devops and Kubernetes  
author: ajaytekam   
date: 2023-06-19 14:30:00 +0530   
img_path: /assets/posts/20230619/   
categories: [DevOps, Kubernetes]    
tags: [yaml]  
image:
  path: yaml.jpg
  alt: Yaml file Basics for Devops and Kubernetes  
---    

## Contents   

- [Basics](#basics)  
- [Lists in YAML](#lists-in-yaml)   
- [Dictonary in YAML](#dictonary-in-yaml)   
- [Nested YAML](#nested-yaml)    
- [Comments on yml](#comments-on-yml)  
- [Tripple hyphens in YML file](#tripple-hyphens-in-yml-file)  
- [A List of Dictonaries](#a-list-of-dictonaries)   
- [Some useful utilities](#some-useful-utilities)   

----------------

## Basics     

YAML file is a text-based file used for configuration files, data serialization, and exchange between different as a format for storing structured data.

### Example

```yml    
---
name: John Doe
age: 30
email: john@example.com
Sample_names:
- John
- Sam
- Dwayne
```

## Lists in YAML     

* Lists begin with a hyphen
* Dependent on whitespace/indentation

Json Example

```json   
{
"Sample_names" : [
    "John",
    "Sam",
    "Dwayne"
  ]
}
```

YAML Example

```yml   
---
Sample_names:
- John
- Sam
- Dwayne
```

## Dictonary in YAML    

example of a simple dictonary

JSON

```json
{
  "name": "John Doe",
  "age": 30,
  "email": "john@example.com"
}
```

YAML

```yml   
---
name: John Doe
age: 30
email: john@example.com
```

With nested Dictonary

JSON

```json
{
  "name": "John Doe",
  "age": 30,
  "contact": {
    "email": "john@example.com",
    "phone": "0987654321",
    "address": "milkey way, far behind the son"
  }
}
```

YML

```yml
---
name: John Doe
age: 30
contact:
  email: john@example.com
  phone: '0987654321'
  address: milkey way, far behind the son
```

In the second dictonary there is a space/indentation in dictonary items.  

## Nested yaml    

JSON

```json
{
  "name": "John Doe",
  "age": 30,
  "email": "john@example.com",
  "Sample_names" : [
    "John",
    "Sam",
    "Dwayne"
  ],
  "Dataset" : {
    "data1": 12,
    "data2": 13,
    "LoopDataset" : {
      "DS01" : 200,
      "DS02" : 300,
      "DS03" : [
      	"Hew01",
        "Hew02",
        "Hew03"
      ]
    }
	}
}
```

YAML

```yml
---
name: John Doe
age: 30
email: john@example.com
Sample_names:
- John
- Sam
- Dwayne
Dataset:
  data1: 12
  data2: 13
  LoopDataset:
    DS01: 200
    DS02: 300
    DS03:
    - Hew01
    - Hew02
    - Hew03
```  

## Comments on YML   

The `#` character is used to comment on yml document

```yml   
---
# user details
name: John Doe
age: 30
# contact data
contact:
  email: john@example.com   # some comments for email
  phone: "0987654321"
  address: milkey way, far behind the son

```   
 
## Tripple hyphens in YML file   

* The three hyphens `---` is a document separator.  
* It is used to separate multiple YAML documents within a single file.  
* Each document represents a separate YAML data structure, such as an object, a list, or a configuration.   
* It is commonly used when you want to group related data or configurations together, making it easier to manage and organize your YAML files.  
* When parsing YAML files, libraries and tools can process each document individually by recognizing the --- separator. This allows you to extract or manipulate specific documents within the file as separate entities.  

```yml 
---
# Document 1
person:
  name: John Doe
  age: 30

---
# Document 2
animal:
  species: dog
  breed: Labrador Retriever
```   

## A List of Dictonaries  

* A list with key-value pairs is often referred to as a list of dictionaries or a list of maps.
* It represents a sequence of dictionaries, where each dictionary contains key-value pairs.
* This structure allows you to have multiple dictionaries within a list, with each dictionary having its own set of keys and corresponding values.  

```yml    
- name: John Doe 
  age: 30  
  city: New York 
- name: Jane Smith  
  age: 25  
  city: London  
- name: Alex Johnson  
  age: 35   
  city: Paris   
```  

* At the above example we have a list of three dictionaries.   
* Each dictionary represents a person with keys (name, age, city) and their respective values.   
* The dash (-) is used to indicate the start of each dictionary within the list.    
* When parsing or interpreting this YAML structure, it would result in a list of dictionaries, allowing you to access the data by iterating over the list and accessing the key-value pairs within each dictionary.  

## Some useful utilities   

### `yq` command-line tool

A lightweight and portable command-line YAML processor.

Link : https://github.com/mikefarah/yq

### useful website

- [www.yamllint.com](https://www.yamllint.com/) : Check yml file for errors.
- [www.json2yaml.com](https://www.json2yaml.com/) : Convert json to yml.
