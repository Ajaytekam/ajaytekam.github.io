---   
title: Terraform Notes for Absolute Beginners  
author: ajaytekam   
date: 2023-10-15 14:53:00 +0530   
img_path: /assets/posts/20231015/ 
categories: [Terraform, DevOps, Automation]
tags: [IaC]
image:
  path: terraform.png 
---    

## Contents 

- [What is Terraform](#what-is-terraform)  
- [Terraform Providers](#terraform-providers)    
- [Provider Versioning](#provider-versioning)    
- [Terraform Resources](#terraform-resources)  
- [Terraform State](#terraform-state)   
- [Terraform Configuration Basics](#terraform-configuration-basics)  
    - [Attributes](#attributes)  
    - [Variables](#variables)  
    - [Simple Variables : String, Number, Boolean](#simple-variables--string-number-boolean)  
    - [Collection Variables : List, Map, Set](#collection-variables--list-map-set)  
    - [TFVAR File](#tfvar-file)  
    - [Environment Variables](#environment-variables)  
    - [Interactive Input](#interactive-input)   
    - [Output variables](#output-variables)   
    - [Conditional Expression](#conditional-expression)  
    - [Dynamic Blocks](#dynamic-blocks)  
    - [Local Values](#local-values)  
    - [Terraform Functions](#terraform-functions)  
    - [Tainted Resources](#tainted-resources)   
    - [Splat Expression](#splat-expression)  
- [Terraform Provisioners](#terraform-provisioners)  
- [Terraform Backend](#terraform-backend)   
- [Using S3 Bucket as Terraform Backend](#using-s3-bucket-as-terraform-backend)   
- [Terraform State Lock](#terraform-state-lock)  
- [Using AWS DynamoDB for State Lock](#using-aws-dynamodb-for-state-lock)   
- [Null Resource](#null-resource)   
- [Terraform Modules](#terraform-modules)  
- [Terraform Remote Module Sources](#terraform-remote-module-sources)    
- [Import Resources](#import-resources)   
- [Remote State Management](#remote-state-management)   
- [Terraform Workspaces](#terraform-workspaces)  
- [Securing Sensitive Information](#securing-sensitive-information)  
- [Create Resources in multiple Regions](#create-resources-in-multiple-regions)   
- [Create Resources in multiple Accounts](#create-resources-in-multiple-accounts)    


## What is Terraform  

__What is Infrastructure as Code ?__  

- Infrastructure as a code allows you to build, change and manage your infrastructure through codig instead of manual processes.   
- The configuration files are created according to your infrastructure specifications and these configurations can be edited and distributed securely within an organization.  
- Infrastructure as Code is the managing and provisioning of infrastructure through code instead of through manual processes.  

__Terraform :__  

- Terraform is an infrastructure as Code tool that lets you build, edit and version the infrastructure as efficient manner.   
- It covers both low level and high level components such as compute instances, memory, networking as well as DNS records, saas services and so on.   
- It is capable of managing both third party services and unique in-house solutions.  
- Example AWS, Azure, GCP, Linode, IBM Cloud, VMWare, Oracle Cloud Infrastructure, vSphare.   
- It is an infrastructure provisioning tool.   

__Features of Terraform :__  

- Automate infrastructure provision.  
- Define infrastructure state.  
- Terraform uses DSL (Domain Specific Language) which is similar to json, it is known as HCL (HashiCorp Configuration Language).  
- Maintain infrastructure change history using vcs (git).  

## Terraform Resources      

- A resource represents a single item that exists within a popular infrastructure provider, such as an amazon web services, ec2 instances, gcp resource has attributes.  
 - Terraform uses this information to create, update or delete the resources as needed to bring the infrastructure into the desired state.  

Syntax :  

```bash  
resource "resource_name" "alias" {
    attribute = value
    attribute = value
    ...
}
```  

Example :  

```bash  
resource "aws_instance" "web01" {
  ami           = "ami-12345678"
  instance_type = "t2.micro"
}

resource "aws_eip" "example" {
  instance = aws_instance.example.id
} 

```
## Terraform Providers 

- These are plugins that terraform uses to interact with cloud service providers.   
- Each provider implements the logic for creating, updating and deleting resources in a specific cloud environment.  

Example of AWS Provider 

```bash    
terraform {
  required_providers {
    aws = {
      source = "hashicorp/aws"
      version = "5.16.1"
    }
  }
}

provider "aws" {
  # Configuration options
}
```

Where  
- `source` defines who maintains the provider, in this case hashicorp maintains the aws provider.  
- `version` defines the specific provider version.  

There are also third party providers maintained by third party/respective companies, for example github provider.  

```bash   
terraform {
  required_providers {
    github = {
      source = "integrations/github"
      version = "5.35.0"
    }
  }
}

provider "github" {
  # Configuration options
}
``` 

### Provider Versioning 

- Provider versioning refers to the practice of specifying the version of a Terraform provider to use in your Terraform configurations.   
- By specifying the provider version in your configuration, you can ensure that your infrastructure code is compatible with a known version of the provider. 
- `Provider Version Constraint` 
    - Defines the acceptable range of provider versions that your configuration can use.   
    - You can specify the version constraint using various operators like `>`, `<`, >=`, `<=`, `~>`, `~<`, `~=`, etc., along with the desired version number or version constraint range. 

Example :  

```bash   
terraform {
  required_providers {
    github = {
      source = "integrations/github"
      version = "5.35.0"
    }
  }
}
```

Then after running `terraform init` command. 

* Downgrading the provider version 

Suppose you have to downgrade the version to `< 5.35.0` then you can change the version to 

```bash  
terraform {
  required_providers {
    github = {
      source = "integrations/github"
      version = "< 5.35.0"
    }
  }
}
```

Now if you run `terraform init` then it will through error, because of terraform lock file, so you have to delete that first.  

> Terraform `lock file` (typically named `terraform.lock.hcl`) records the exact versions of providers used in your configuration. This ensures that subsequent runs of Terraform use the same provider versions, providing consistency across your infrastructure.  

After deleting the lock file you will be able to downgrade the provider version. In my case it installed provider version 3.34.0   

* Upgrading the provider version : 

Now we want to install provier version `> 3.34.0`, 

```bash 
terraform {
  required_providers {
    github = {
      source = "integrations/github"
      version = "> 5.34.0"
    }
  }
}
```

Now we can do that either by deleteing th lock file or just adding the flag `--upgrade`.  

```bash  
terraform init --upgrade
```

__Multiple Version Constraints :__   


```bash   
terraform {
  required_providers {
    github = {
      source = "integrations/github"
      version = "> 5.0.0, < 5.35.0"
    }
  }
}
```  

## Terraform State 

- Terraform store the resource state in a file and each resource is mapped to a state file.   
- State file contains the current state of infrastructure managed by terraform. 
- It tracks all the resources and their dependencies and keeps a record of any changes made to the infrastructure.   
- The state is stored on terraform.tfstate file in json format.  

__State Change :__  

- After provisioning of the infrastructure, if some changes occurs on ifractructure then in that case there is no sink between the infrastructure and terraform state. So in this situation you can run `terraform refresh` command to make sink between terraform state and infrastructure, or you can also use `terraform plan` command. Now lets see an example.  

## Terraform Backend   

- Terraform backend is a configuration setting that determines where and how Terraform stores its state files and manages the state of your infrastructure.   
- The state file is a critical component of Terraform as it keeps track of the current state of your resources and is used to plan and apply changes to that infrastructure.  

__Types of Terraform Backends :__    

- __Local Backend :__  
    - Default backend for Terraform.   
    - The state file is stored on the local filesystem of the machine where Terraform is executed.   
    - Suitable for single-user, single-environment scenarios.  
    - Not recommended for collaborative or production use.  
- __Remote Backends :__  
    - AWS S3 backends    
    - Azure Blob Storage Backend   
    - Google Cloud Storage Backend   
    - Terraform Cloud Backend  

## Terraform Configuration Basics  

### Attributes  

- Attributes are values associated with resources that can be queried or referenced within the configuration.  
- Resources represent infrastructure components such as virtual machines, networks, databases, and more.   
- Attributes allow you to extract information from these resources or use their properties in other parts of your Terraform configuration.    
- Attributes are used primarily in the context of data retrieval and resource interpolation.  

__Data Retrival :__  

You can use attributes to fetch information about existing resources or data sources. This is typically done using the `data` block in your Terraform configuration. For example, you can use the aws_instance data source to fetch details about an EC2 instance which is already provisioned.  

```bash  
data "aws_instance" "example" {
  instance_id = "i-0123456789abcdef0" # Replace with your instance ID
}

output "instance_private_ip" {
  value = data.aws_instance.example.private_ip
}
```  

Now if you run `terraform plan` or `terraform apply` then the output block is basically going to print out the private ip of ec2 instance.  

__Resource Interpolation :__  

You can also use attributes to interpolate values from one resource into another. For example, if you want to associate an Elastic IP address with an EC2 instance:  

```bash  
resource "aws_instance" "example" {
  ami           = "ami-12345678"
  instance_type = "t2.micro"
}

resource "aws_eip" "example" {
  instance = aws_instance.example.id
} 
```   

You can find the available attributes for each resource or data source in the official Terraform documentation or by running the `terraform show` command after applying your configuration.  

### Variables   

- Variables are used to define the values which are used in the configuration.    
- Variables are defined in the variables block in your Terraform configuration file, where you can give a name and a default value.  
- Structure of variable 

```bash
variable "VARIABLE_NAME" {
  description = "Description of variable"
  type        = number|string|boolean|list|map
  default     = "VARIBALE_VALUE"
}
```   

- How to Use: 
    - Variable can be stored on any file with `.tf` extension, example `myvar.tf`.  
    - Variable can be accessed by using the keyword `var`, for example `var.VARIABLE_NAME`.  
- You can also override the default variable values or set new variable during runtime by providing `-var` flag, for example `terraform apply -var "variable_name=value"`.  
-  Variables can also be set using a separate file, called a variable file with `.tfvars`, using the `-var-file` flag. Example `terraform apply -var-file=variables.tfvars`.  

#### Simple Variables : String, Number, Boolean  

__String :__  Store string value.   

```bash
variable "instance_type" {
   description = "Instance type t2.micro"
   type        = string
   default     = "t2.micro"
}

variable "ami" {
   description = "Ubuntu AMI ID"
   type        = string
   default     = "ami-0767046d1677be5a0"
}
```  

Usage:  

```bash
resource "aws_instance" "myec2" {
    ami           = var.ami
    instance_type = var.instance_type
}
```

__Number :__ Store numbers.  

```bash
variable "instance_count" {
  description = "EC2 instance count"
  type        = number
  default     = 2
}  
```  

Usage: 

```bash
resource "aws_instance" "myec2" {
    ami           = var.ami
    instance_type = var.instance_type
    count         = var.instance_count
}
```  

__Boolean :__ Store boolean value.  

```bash  
variable "instance_type" {
   description = "Instance type t2.micro"
   type        = string
   default     = "t2.micro"
}

variable "ami" {
   description = "Ubuntu AMI ID"
   type        = string
   default     = "ami-0767046d1677be5a0"
}

variable "enable_public_ip" {
  description = "Enable public IP address"
  type        = bool
  default     = true
}
```  

Usage :  

```bash   
resource "aws_instance" "myec2" {
    ami                         = var.ami
    instance_type               = var.instance_type
    count                       = var.instance_count
    associate_public_ip_address = enable_public_ip
}
```  

#### Collection Variables : List, Map, Set   

__List :__ 

- List container more then one element.  

```bash
variable "user_names" {
  description = "IAM usernames"
  type        = list(string)
  default     = ["user1", "user2", "user3s"]
}
```  

Usage :  

```bash
resource "aws_iam_user" "example" {
  count = length(var.user_names)
  name  = var.user_names[count.index]
}
``` 

The above resource going to create three iam users by looping through list. 

__Map :__ 

- Store key-value pairs. 
- Each key is unique within the map, and each key is associated with a single value.  
- Maps enforce uniqueness based on keys. 
- Example   

```bash
variable "AMIS" {
  description = "project name and environment"
  type        = map(string)
  default     = {
    ubuntu       = "ami-0767046d1677be5a0",
    amazon_linux = "ami-63497344477bfea67",
    centos       = "ami-83743900232323b44",
    redhat       = "ami-076047438677b3409"
  }
}
```

Usage :  

```bash  
resource "aws_instance" "myec2" {
    ami                         = var.AMIS["amazon_linux"]
    instance_type               = var.instance_type
}
```  

__Set :__  

- Store a collection of distinct, unordered values. 
- Represent a unique list of items without any specific order.  
- Sets enforce uniqueness based on values.  
- Example of Set:  

```bash   
variable "allowed_ports" {
  type = set(number)
  default = [80, 443, 22]
}
```  

- `element()` function to access specific elements within a set.  
- It takes two arguments: the set you want to access and the index (position) of the element you want to retrieve.  

```bash  
variable "allowed_ports" {
  type    = set(number)
  default = [80, 443, 22]
}

# Access the first element (index 0) in the set
locals {
  first_port = element(var.allowed_ports, 0)
}

output "first_port" {
  value = local.first_port
}
```  

- Another way to access set values is by converting the set to a list and then accessing elements by index. You can use the tolist() function to convert the set to a list.   
- Example:  

```bash   
variable "allowed_ports" {
  type    = set(number)
  default = [80, 443, 22]
}

# Convert the set to a list
locals {
  allowed_ports_list = tolist(var.allowed_ports)
}

# Access the first element (index 0) in the list
locals {
  first_port = local.allowed_ports_list[0]
}

output "first_port" {
  value = local.first_port
}
```  

> Note: Keep in mind that sets are unordered, so the order of elements within a set is not guaranteed. If you need a specific order for your values, you should consider using a list or map instead of a set.  


### TFVAR File   

- A `.tfvars` file in Terraform is a file that is used to store variable values for your Terraform configurations. 
- These variable values can then be passed into your Terraform modules or configurations when you run Terraform commands like terraform apply or terraform plan. 
- `.tfvars` files are a common way to manage and provide input values for your Terraform code in a structured and reusable manner.
- A typical tfvars file should contain the variables that you want to pass to Terraform. 
- Each variable should be in the form of `variable_name = value`. For example  

```bash  
instance_count = 3
instance_type  = "t2.micro"
```  

- When running Terraform commands, you can provide these variable values by using the `-var-file` flag and specifying the path to the `.tfvars` file: 

```bash 
terraform apply -var-file=variables.tfvars
```  

__Note :__ You have to define the variable in the terraform configuration, it doesn't mean that you don't have to define the variable if you are using tfvars file. for example in the above case you have to define two empty variables like 

```bash  
variable "instance_count" {
    type = number
} 

variable "instance_type" {
    type = string
}
```  

__Use Cases :__  

- Using `.tfvars` files is beneficial because it separates your configuration data from your Terraform code. 
- This separation allows you to manage different configurations for various environments (e.g., development, staging, production) and makes it easier to reuse variable values across different Terraform executions.  
- Another use case that if you have multiple iam user account for different apps then you can basically supply credentials/region with the .tfvars file  

Example :  

File: provider.tf  
```bash  
terraform {
  required_providers {
    aws = {
      source = "hashicorp/aws"
      version = "5.16.1"
    }
  }
}

provider "aws" {
    region = var.REGION
    access_key = var.ACCESS_KEY
    secret_key = var.SECRET_KEY
}
```

File: vars.tf  
```bash  
variable "REGION" {
    type = string
}

variable "ACCESS_KEY" {
    type = string
}

variable "SECRET_KEY" {
    type = string
}
```  

File: user1.tfvars  
```bash 
ACCESS_KEY = "heldsnkdskjbe83fdsj"
SECRET_KEY = "heldsnkdskjbe83fdsj"
REGION     = "ap-south-1"
```  

Command 

```
terraform apply -var-file=user1.tfvars  
``` 

- You can also provide multiple variable files by using the -var-file flag multiple times  

```bash  
terraform apply -var-file=myvars-1.tfvars -var-file=myvars-2.tfvars
```  

__Auto-Loading tfvars file :__  

There are two ways to automatically load tfvar file 

1. __By changing the file extension to `.auto.tfvars`__ : Just change the file extension from `.tfvars` to `.auto.tfvars`. Example from `prod.tfvars` to `prod.auto.tfvars`.  
2. __By renaming file to `terraform.tfvars`__ : Just rename the file to `terraform.tfvars`.  

### Environment Variables 

* Terraform allows you to set variables via environment variables.  
* Environment variables must be prefixed with `TF_VAR_` followed by the variable name in uppercase. 
* For example, to set the `instance_count` variable:  

```bash  
export TF_VAR_instance_count=5  
```  

When you run Terraform commands, it will automatically use the values from the corresponding environment variables.  

### Interactive Input   

* If you don't specify variable values in any of the above ways, Terraform will prompt you for input values when you run a command that requires them.  
* This can be useful for ad-hoc configurations or when you want to provide values interactively.   

For example:  

File: vars.tf  
```bash 
variable "instance_type" {
   description = "Instance type t2.micro"
   type        = string
   default     = "t2.micro"
}

variable "ami" {
   description = "Ubuntu AMI ID"
   type        = string
   default     = "ami-0767046d1677be5a0"
}

# empty variable without value
variable "instance_count" {}
```

File: ec2.tf  
```bash  
resource "aws_instance" "myec2" {
    ami                         = var.ami
    instance_type               = var.instance_type
    count                       = var.instance_count
}
```  

Now if you run terraform plan or apply then it will ask you to manually enter the number of instances.   

### Output Variables   

- Extract information about the resources that were created by Terraform.  
- Reference the values of resources after Terraform has finished running.   
- Defined in the outputs block in the Terraform configuration file.  

```bash  
output "ec2i_public_ip" {
    value = aws_instance.ec2i.public_ip
}
```  

- In this example, the output variable is named "ec2i_public_ip" and its value is set to the public IP of an EC2 instance named "ec2i" that is defined in the Terraform configuration. 

__`terraform output` command :__ 

- Access the value of an output variable. 
- You can prints the values of all output variable with `terraform output` command. 
- Or you can also print the value of a particular variable with `terraform output <output_variable_name>`   

__Using output variables as a reference :__   

- In addition to being able to reference output variables from the command line, you can also reference them in other parts of the Terraform configuration files using output function. For example:  

```
resource "aws_security_group_rule" "example" {
   ...
cidr_blocks = [output.instance_ip]
}
```

### Conditional Expression   

__`?:` ternary conditional operator :__   

```bash  
condition ? consequent : alternative  
```  

Example : 

```
count = var.create_instance ? 2 : 0
```  

If the variable `create_instance` is true then value 2 is assigned to otherwise value 0 is assigned.  

Code Example : 

File: main.tf  
```bash  
resource "aws_instance" "example" {
  ami           = var.ami
  instance_type = var.instance_type
  count         = var.create_instance ? 2 : 0
}
```

File: vars.tf  
```bash  
variable "ami" {
  type = string
}

variable "instance_type" {
  type = string
}

variable "create_instance" {
  type = bool
}
```  

File: vars.auto.tfvars   
```bash  
ami             = "ami-02bb7d8191b50f4bb"
instance_type   = "t2.micro"
create_instance = true
```  

If you run `terraform plan` command then you can see two instances are going to created. and if you change the create_instance to false then no instances will be created.  

Another Example: 

```bash 
??? from here until ???END lines may have been inserted/deleted
instance_type = var.env == "prod" ? "t2.micro" : "t2.nano"
```  

If variable env consists the value "prod" then the instance type will be t2.micro or it will be t2.nano  

Code Example : 

File: main.tf  
```bash  
resource "aws_instance" "example" {
  ami           = var.ami
  instance_type = var.env == "prod" ? "t2.micro" : "t2.nano"
  count         = var.create_instance ? 2 : 0
}
```

File: vars.tf  
```bash  
variable "ami" {
  type = string
}

variable "env" {
  type = string
}

variable "create_instance" {
  type = bool
}
```  

File: vars.auto.tfvars   
```bash  
ami             = "ami-02bb7d8191b50f4bb"
env             = "prod"
create_instance = true
```  

If you run `terraform plan` command then you can see two instance type will be `t2.micro`.    

__foreach Loop :__ Used for conditionally create multiple instances of a resource. Such use cases like if you want to create multiple ec2 instances.   
File: main.tf  
```bash  
resource "aws_instance" "example" {
  ami           = var.ami
  for_each = var.create_instance ? toset(["instance1", "instance2"]) : toset([])
  instance_type = var.env == "prod" ? "t2.micro" : "t2.nano"
}
```

File: vars.tf  
```bash  
variable "ami" {
  type = string
}

variable "env" {
  type = string
}

variable "create_instance" {
  type = bool
}
```  

File: vars.auto.tfvars   
```bash  
ami             = "ami-02bb7d8191b50f4bb"
env             = "prod"
create_instance = true
```  

- Now if you run `terraform plan` command then it will show you two instances instance1 and instance2 are getting created.  
- Also note that don't use count attribute with the for_each statement, it will going to create error.  

## Dynamic Blocks  

- You can use dynamic blocks to conditionally include or exclude blocks of configurations within a resource.   
- In the below example we define a variable ingress_rules which conatains the map of ingress rules configs as a list.  

File: vars.tf  
```bash    
variable "ingress_rules" {
  type = list(object({
    from_port   = number
    to_port     = number
    protocol    = string
    cidr_blocks = list(string)
  }))
  default = [
    {
      from_port   = 80
      to_port     = 80
      protocol    = "tcp"
      cidr_blocks = ["0.0.0.0/0"]
    },
    {
      from_port   = 22
      to_port     = 22
      protocol    = "tcp"
      cidr_blocks = ["10.0.0.0/16"]
    },
    {
      from_port   = 443
      to_port     = 443
      protocol    = "tcp"
      cidr_blocks = ["0.0.0.0/0"]
    },
  ]
}
```   

Now by using dynamic block with foreach loop we can implement all the above rules in our security group.  

File: main.tf  
```bash 
resource "aws_security_group" "mySg001" {
  name        = "mySg001"
  description = "Security Group created by for loop"

  // Use a for expression to create multiple ingress rules
  dynamic "ingress" {
    for_each = var.ingress_rules

    content {
      from_port   = ingress.value.from_port
      to_port     = ingress.value.to_port
      protocol    = ingress.value.protocol
      cidr_blocks = ingress.value.cidr_blocks
    }
  }

  egress {
    from_port        = 0
    to_port          = 0
    protocol         = "-1"
    cidr_blocks      = ["0.0.0.0/0"]
    ipv6_cidr_blocks = ["::/0"]
  }

}
```  

__Another example of Dynamic Blocks :__  

File: vars.tf  
```bash  
variable "ports" {
  type    = list(number)
  default = [22, 80, 443, 8080]
}
```

File: main.tf
```bash   
resource "aws_security_group" "mySg001" {
  name        = "mySg001"
  description = "Security Group created by for loop"


  // Use a for expression to create multiple ingress rules
  dynamic "ingress" {
    for_each = var.ports
    iterator = port
    // iterator basically iterate through all the port number
    content {
      from_port        = port.value
      to_port          = port.value
      protocol         = "tcp"
      cidr_blocks      = ["0.0.0.0/0"]
      ipv6_cidr_blocks = ["::/0"]
    }
  }

  egress {
    from_port        = 0
    to_port          = 0
    protocol         = "-1"
    cidr_blocks      = ["0.0.0.0/0"]
    ipv6_cidr_blocks = ["::/0"]
  }

}
```  

### Local Values 

- Local values allow users to define and store intermediate or derived values within your configuration.  
- These values are not associated with any specific resource or variable, but rather serve as a way to compute and reuse values that may be used multiple times within your Terraform configuration.  
- Local values are defined using keyword `local`  
- Example:  

```bash   
locals {
  my_variable = "Some value"
  calculated_value = var.some_variable * 2
  combined_string = "${local.my_variable} - ${var.another_variable}"
  common_tags = {
    Project = "WebApp"
    Service = "BackEnd"
    Env     = "Prod"
  }
}
```  

Example Use :  

File: main.tf  
```bash  
provider "aws" {
  region = "ap-south-1"
}

# defining local values
locals {
  common_tags = {
    Project = "WebApp"
    Service = "BackEnd"
    Env     = "Prod"
  }
}

resource "aws_instance" "ec2" {
  ami           = "ami-02bb7d8191b50f4bb"
  instance_type = "t2.micro"
  tags          = local.common_tags
}

resource "aws_ebs_volume" "ebs" {
  availability_zone = "ap-south-1a"
  size              = 10
  tags              = local.common_tags
}
```   

### Terraform Functions    

- Terraform functions are built-in functions that you can call from within expressions to transform and combine values.  
- For example :  
    - lookup() : Perform lookup on the map.  
    - toset() : Convert list datatype into set.   
    - formatdate() : format date.   

You can find the full list of functions here: https://developer.hashicorp.com/terraform/language/functions   

Example:  

```bash  
provider "aws" {
  region = "ap-south-1"
}

locals {
  time = formatdate("D MM YYYY hh:mm ZZZ", timestamp())
}

variable "base_image" {
  type    = string
  default = "amazon_linux"
}

variable "ami" {
  type        = map(string)
  description = "AMI values for different Images"
  default = {
    ubuntu       = "ami-0f5ee92e2d63afc18"
    centos       = "ami-0763cf792771fe1bd"
    amazon_linux = "ami-02bb7d8191b50f4bb"
  }
}

resource "aws_instance" "ec2" {
  ami           = lookup(var.ami, var.base_image)
  instance_type = "t2.micro"
}

resource "aws_ebs_volume" "ebs" {
  availability_zone = "ap-south-1a"
  size              = 10
}

output "timestamp" {
    value = local.time
}
```  

- At the above code functions used are : 
    - timestamp(): gives the current time stamp. 
    - formatdate(): Format date on the given format.  
    - lookup(): Search for a particular key in a map and returns the value. Syntax would be : `lookup(name_of_the_map_to_perform_search, key_to_search)` 

## Tainted Resources      

- A "tainted" state refers to the state of a resource that Terraform marks as tainted when it detects that the resource's real-world state doesn't match the expected state defined in your Terraform configuration.  
- When a resource is tainted, Terraform considers it to be in an unknown or compromised state, and it usually signals that Terraform should take corrective action during the next run, means terraform is going to destroy and re-create that particular resource.   

__Tainting Resources :__  

- You can manually taint a resource using the terraform taint command.   
- This can be useful when you want to trigger the recreation or modification of a resource, even if Terraform doesn't detect any changes to its configuration.  
- Command:   

```bash   
// first create the resource 
terraform apply 

// then list all the resource 
terraform state list 

// taint the reosurce 
terraform taint <resource_name>
``` 

- When a resource is tainted, Terraform treats it as needing to be recreated or modified to match the configuration.  
- You can use `terraform state list` to see the tainted resources.  
- To get the details of a particular resource run `terraform state list -state-out=newstate.tfstate` command.  

## Splat Expression  

- A "splat expression" is a way to reference and work with multiple elements or attributes of a data structure like a list or map.  
- It's allows you to select and manipulate multiple items from a collection in a concise way.   
- The `*` character is used as the splat operator in Terraform. 
- Examples 
    - List Splatting: Reference all elements of a list `aws_instance.example.*.private_ip`.   
    - Map Splatting: Reference all attributes of a map ``.   
    - Indexed Splatting: Instead of Listing of element via index `aws_instance.example[0].public_ip` you can use splat `aws_instance.example.*.public_ip`  

Some use cases :  

- Example 1 : List public ip of all created instances.   

```bash  
resource "aws_instance" "example" {
  ami           = "ami-0123456789abcdef0"
  instance_type = "t2.micro"
  count         = 3
}

output "public_ips" {
  value = aws_instance.example.*.public_ip
}
```  

Example 2 : Create 2 iam users and print their names as output.  

```bash  
resource "aws_iam_user" "userCr" {
    name  = "iamuser.${count.index}"  
    count = 2
    path  = "/system/"
} 

output "arns" {
    value = aws_iam_user.userCr[*].name
}
```  

## Terraform Provisioners     

- Terraform Provisioners are used to performing certain custom actions and tasks either on the local machine or on the remote machine. Like execute commands or scripts on local or remote machine, uploading files on remote machine etc.  
- There are two types of provisioners : 
    - Generic Provisioners : file, local-exec, remote-exec
    - Vendor Provisioners  : chef, habitat, puppet, salt-masterless

### `local-exec` Provisioner   

- Runs commands on the machine where Terraform is executed, which is often your local development machine.  
- Local-Exec provisioners are commonly used for tasks like running scripts or executing command-line tools on a remote resource.  
- Example : 

```bash 
provisioner "local-exec" {
    command = "touch 'hello world' > hello.txt"
}
```  

- Where the argument `command` contains the command which is going to be execute on local machine.  
- Other optional arguments are  
    - `working_dir` : Specify the working directory where command will get executed. 
    - `interpreter` : Specify which interpreter (python, bash, PowerShell, perl etc.) you are going use to execute the command.
    - `environment` : Used for passing environment variables alongside the command argument.   

```bash  
provisioner `local-exec` {
    command     = "print('hello world')"
    interpreter = ["python3", "-c"]
    working_dir = "./testdir"
}
```  

Example : 

```bash 
provider "aws" {
  region = "ap-south-1"
}


resource "aws_instance" "ec2" {
  ami           = "ami-02bb7d8191b50f4bb"
  instance_type = "t2.micro"

  provisioner "local-exec" {
    command     = "print('Hello world From Python')"
    interpreter = ["python3", "-c"]
  }

}
```  

__Multiline Command :__   

```bash  
provisioner "local-exec" {
    working_dir = "./testDir"
    command     = <<-EOT
        touch file.txt
        echo "Hello world" >> file.txt
        echo "This is second line" >> file.txt
        echo "This is third line" >> file.txt
    EOT
}
```  

__With Environment variable :__  

```bash 
provider "aws" {
  region = "ap-south-1"
}

resource "aws_instance" "ec2" {
  ami           = "ami-02bb7d8191b50f4bb"
  instance_type = "t2.micro"

  provisioner "local-exec" {
    command     = <<-EOT
        echo "Secret Key: $SOME_KEY" 
        echo "Secret Key: $SOME_SECRET"
        echo "App NAme  : $APPNAME"
    EOT

    environment = {
        SOME_KEY    = "snldldmsfdfjjdslfndsf0833"
        SOME_SECRET = "jnsdsfdsdjjjdkwe3234234d2"
        APPNAME     = "SomeRandomCOMMANDLINEApp"    
    }

  }

}
```  

### `remote-exec` Provisioner  

- Run commands or scripts on the remote resource itself.  
- They are often used to perform configuration tasks directly on the resource, such as installing software or configuring services.  
- Arguments   
    - `inline`  : Specify the command or commands you want to run on the remote resource. You can provide a single command as a string or multiple commands as a list of strings.   
    - `script`  : Provides the path to a local script file that should be executed on the remote resource.   
    - `connection` : Configure the SSH connection settings for connecting to the remote resource. You can set options such as the SSH user, host, private key file, and port. Also note that this is mendatory step   
    - In the example below we are going to deploy a static website using terraform provisioners.  

__Example of inline `remote-exec` Provisioner :__  

File: main.tf  
```bash  
resource "aws_instance" "web01" {
  ami                    = var.ami
  instance_type          = "t2.micro"
  key_name               = var.keyname
  vpc_security_group_ids = [aws_security_group.webSrv_SG.id]

  provisioner "remote-exec" {
    inline = [
      "sudo apt update -y",
      "sudo apt install apache2-bin apache2 unzip wget -y",
      "sudo systemctl enable apache2",
      "sudo systemctl start apache2",
      "wget https://www.free-css.com/assets/files/free-css-templates/download/page295/carint.zip -O /tmp/file.zip",
      "cd /tmp",
      "unzip file.zip ",
      "sudo mv carint-html/* /var/www/html/",
      "sudo chown -R www-data:www-data /var/www/html/*",
      "rm -rf file.zip carint-html",
    ]
  }

  connection {
    type        = "ssh"
    user        = "ubuntu"
    private_key = file("${var.keyname}.pem")
    host        = self.public_ip
    timeout     = "4m"
  }
}

output "webserver_Public_IP" {
  value = "http://${aws_instance.web01.public_ip}/"
}
```  

File: security-group.tf  
```bash  
resource "aws_security_group" "webSrv_SG" {
  name        = "webSrv_SG"
  description = "Security Group for Static web server"

  # outgoing traffic rules
  egress {
    # allows all the port
    from_port        = 0
    to_port          = 0
    protocol         = "-1"
    cidr_blocks      = ["0.0.0.0/0"]
    ipv6_cidr_blocks = ["::/0"]
  }

  # incomming traffic rules
  ingress {
    from_port        = 80
    to_port          = 80
    protocol         = "tcp"
    cidr_blocks      = ["0.0.0.0/0"]
    ipv6_cidr_blocks = ["::/0"]
  }

  ingress {
    from_port        = 22
    to_port          = 22
    protocol         = "tcp"
    cidr_blocks      = ["0.0.0.0/0"]
    ipv6_cidr_blocks = ["::/0"]
  }

}
```  

File: key-generate.tf  
```bash  
# generate the public-private key
resource "tls_private_key" "pk_generate" {
  algorithm = "RSA"
  rsa_bits  = "4096"
}

# store the generated private key in local disk 
resource "local_file" "web_srvKey_pem" {
  filename = "${var.keyname}.pem"
  content  = tls_private_key.pk_generate.private_key_pem
}

# create aws key-pair
resource "aws_key_pair" "websrvKey" {
  key_name   = "${var.keyname}"
  public_key = tls_private_key.pk_generate.public_key_openssh
}
```  

File: vars.tf  
```bash   
variable "ami" {
  type    = string
  default = "ami-0f5ee92e2d63afc18"
}

variable "keyname" {
  type    = string
  default = "websrvKey"
}
```   

The above code is going to deploy a static web sever and also going to print the public ip address of the ec2 instance.  

__Example of script `remote-exec` Provisioner :__  

File: main.tf  
```bash  
resource "aws_instance" "web01" {
  ami                    = var.ami
  instance_type          = "t2.micro"
  key_name               = var.keyname
  vpc_security_group_ids = [aws_security_group.webSrv_SG.id]

  provisioner "remote-exec" {
    script = "./scripts/webserver.sh"
  }

  connection {
    type        = "ssh"
    user        = "ubuntu"
    private_key = file("${var.keyname}.pem")
    host        = self.public_ip
    timeout     = "4m"
  }
}

output "webserver_Public_IP" {
  value = "http://${aws_instance.web01.public_ip}/"
}
```  

File: scripts/webserver.sh 
```bash   
#!/bin/bash   

sudo apt update -y
sudo apt install apache2 unzip wget -y
sudo systemctl enable apache2
sudo systemctl start apache2
wget https://www.free-css.com/assets/files/free-css-templates/download/page295/carint.zip -O /tmp/file.zip
cd /tmp
unzip file.zip 
sudo mv carint-html/* /var/www/html/
sudo chown -R www-data:www-data /var/www/html/*
rm -rf file.zip carint-html
```     

- Also note that put the `webserver.sh` file in `scripts` folder.  
- Put other files key-generate.tf, vars.tf and sec-group.tf in the same folder from previous example.   

### `file` Provisioner  

- The file provisioner is used to copy files or directories from the machine executing Terraform to the newly created resource.  
- The file provisioner supports both ssh and winrm type connections. 
- Example : 

File: main.tf  
```bash   
resource "aws_instance" "web01" {
  ami                    = var.ami
  instance_type          = "t2.micro"
  key_name               = var.keyname
  vpc_security_group_ids = [aws_security_group.webSrv_SG.id]

  provisioner "file" {
    source      = "./scripts/webserver.sh"
    destination = "/tmp/webserver.sh"
  }

  provisioner "remote-exec" {
    inline = [
      "sudo bash /tmp/webserver.sh",
    ]

  }

  connection {
    type        = "ssh"
    user        = "ubuntu"
    private_key = file("${var.keyname}.pem")
    host        = self.public_ip
    timeout     = "4m"
  }
}

output "webserver_Public_IP" {
  value = "http://${aws_instance.web01.public_ip}/"
}
```    

- You need all the files sec-group.tf, key-generate.tf, vars.tf and webserver.sh to run the above script.  
- Also put the webserver.sh file into scripts directory.   
 
### Provisioners Event Blocks 

* __on_failure :__ This block allows users to define actions to take in case of resource failures, such as rolling back changes, notifying stakeholders, or executing commands or scripts.  
    - `fail` : If provisoning of a particular resource/provisioner fails then it shows the error message duirng terraform execution and halt the further provision.  
    - `continue` : If provisoning of a particular resource/provisioner fails then it shows the error message duirng terraform execution and continue the provision.  
    - Example:  

```bash   
  provisioner "file" {
    on_failure  = continue 
    source      = "./script/webserver.sh"
    destination = "/tmp/webserver.sh"
  }

  provisioner "remote-exec" {
    on_failure  = continue  
    inline = [
      "sudo bash /tmp/webserver.sh",
    ]
  }
```  

* __when block :__
    - `when = create`  : Execute commands/scripts when resource gets created.  
    - `when = destroy` : Execute commands/scripts when resource gets destroyed.   

Example: 

```bash   
// during destroy event upload all website files 
// into ftp server
provisioner "remote-exec" {
when = destroy
inline = [
  "sudo apt update -y",
  "sudo apt install ftp -y",
  "sudo tar -cvf /tmp/backup.tar /var/www/html/",
  "curl -T /tmp/backup.tar -u 'dlpuser:rNrKYTX9g7z3RgJRmxWuGHbeu' ftp://ftp.dlptest.com/scripts.tar"
]
}
```

## Using S3 Bucket as Terraform Backend   

- To use S3 bucket as a backend first you have to create S3 bucket. Creating bucket with Commandline   

```bash     
aws s3api create-bucket --bucket <S3_BUCKET_NAME> --region <REGION> --create-bucket-configuration LocationConstraint=<REGION>   
```  

```bash    
aws s3api create-bucket --bucket ec2websrv-tfstate --region ap-south-1 --create-bucket-configuration LocationConstraint=ap-south-1   
```  

Where    

- Bucket name: `ec2websrv-tfstate`   
- Region: `ap-south-1`    

Terraform config file :    

```bash    
terraform {
  backend "s3" {
???END
    bucket = "ec2websrv-tfstate"
    // configure the bucket name 

    key = "terraform.tfstate"
    //configure the state file name 

    // Note: you can't use var.AWS_REGION variable here
    region = "ap-south-1"
  }
}
```  

Now after that you can run the terraform commands to provision the infrastructure  

```bash   
terraform init
terraform fmt 
terraform validate
terraform apply 
```  

Now after provisioning the infrastructure to some other person in the team needs to destroy the infrastructure use the below command so terraform is going utilize the state file from s3 bucket  

```bash      
terraform init -backend-config="bucket=S3-BUCKET-NAME" -backend-config="key=TERRAFORM-STATE-FILE-NAME" -backend-config="region=REGION"  
```    

Example    

```bash   
terraform init -backend-config="bucket=ec2websrv-tfstate" -backend-config="key=terraform.tfstate" -backend-config="region=ap-south-1"  
```  

Now the user can run `terraform destroy` command to destroy the infrastructure.   

## Terraform State Lock   

- When working in a collaborative environment and multiple automation processes are managing your infrastructure, it's essential to ensure that only one entity (user or process) can make changes to the state file at a time.   
- In this type of scenerio Terraform's State Locking mechanism prevents concurrent access to the same state file, which can help prevent conflicts and data corruption when multiple users or processes are working with Terraform simultaneously.  
- Terraform supports different backends for storing its state file, such as local, S3, Azure Blob Storage, Google Cloud Storage, and Terraform Cloud. The specific locking mechanism depends on the backend being used.  

__How Terraform State Lock Works :__  

- When user run a Terraform command example `terraform apply`, Terraform will first attempt to acquire a lock on the state file. 
- If it cannot acquire the lock because another process is already holding it, Terraform will wait until the lock is released or until a specified timeout period is reached.  
- For example, when using S3 as a backend for your state file, Terraform uses DynamoDB as a lock table to coordinate locking. This ensures that only one Terraform operation can write to the state file at a time.  

### Using AWS DynamoDB for State Lock 

Amazon DynamoDB, a NoSQL database service, is an excellent choice for state locking due to its high availability and scalability. It provides a locking mechanism that ensures only one Terraform operation can modify the state at a time.

__Steps :__  

- Create DynamoDB Database (Commandline example)

```bash  
aws dynamodb create-table --table-name terraform_locks --attribute-definitions AttributeName=LockID,AttributeType=S --key-schema AttributeName=LockID,KeyType=HASH --provisioned-throughput ReadCapacityUnits=5,WriteCapacityUnits=5
```  

where table name is `terraform_locks`.  

- Now we have to add the table name into the terraform backend configuration  

```bash   
terraform {
  backend "s3" {
    bucket = "ec2websrv-tfstate"
    // configure the bucket name 

    key = "terraform.tfstate"
    //configure the state file name 

    // Note: you can't use var.AWS_REGION variable here
    region = "ap-south-1"
    
    // terraform lock state 
    dynamodb_table = "terraform_locks"
    encrypt        = true
  }
}
```  

__Testing the State Locking :__   

Run terraform plan in one terminal. While thatΓÇÖs running, try running terraform apply in another terminal. You should see a message saying that the state is locked.    


## Null Resource   

- A "null_resource" is a special resource type that doesn't represent an actual infrastructure object but is used as a placeholder for running arbitrary code during Terraform's execution.  
- It is used when you need to trigger some action or run a script as part of your infrastructure deployment, even if there's no direct infrastructure resource associated with it.  
- When you execute `terraform apply` command the null_resource will always execute it once.  

```bash  
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "5.16.2"
    }
  }
}

provider "aws" {
  region = "ap-south-1"
}

resource "aws_instance" "webt" {
  ami           = "ami-0f5ee92e2d63afc18"
  instance_type = "t2.micro"
}

resource "null_resource" "local-prov" {

  provisioner "local-exec" {
    command = "echo Hello World"
  }

}
```   
 
When we apply the above configuration the provisioner inside null resource will only going to execute once.  

__triggers :__    

- Triggers are used to execute the null resource on when the state/value of a variable changes.   
- Triggers are used to determine when the code inside the null_resource block should run.  
- Triggers can be based on changes to other resources or external conditions.  

Example of trigger  

```bash   
resource "null_resource" "local-prov" {
  triggers = {
    id  = aws_instance.webt.id
  }

  provisioner "local-exec" {
    command = "echo Hello World"
  }
}
```    

The code is going to execute whenever the instance id of instance webt changes. 

__Execute null resource everytime using trigger :__  

You can use `timestamp()` function in trigger to execute null resource every time.  

```
resource "null_resource" "local-prov" {

  triggers = {
    id  = timestamp()
  } 

  provisioner "local-exec" {
    command = "echo Hello World"
  }
}
``` 

### Use cases of null resource :  

- 1. Null resource with local Provisioners : Look at the first example.   

```bash  
resource "null_resource" "local-prov" {
  triggers = {
    id  = aws_instance.webt.id
  }

  provisioner "local-exec" {
    command     = <<-EOT
      echo "This is local provisioners" 
      chmod +x some_script.sh
      ./some_script.sh
      chmod +x provision_support_env.sh
      ./provision_support_env.sh
    EOT
  }
}
```    

- 2. Null resource with remote provisioners  

```    
resource "null_resource" "local-prov" {
  triggers = {
    id  = aws_instance.webt.id
  }

  provisioner "remote-exec" {
    inline = [
        "apt update -y",  
        "apt install apache2 apache2-bin unzip wget -y",  
        "systemctl enable apache2",  
        "systemctl start apache2",  
        "wget https://www.free-css.com/assets/files/free-css-templates/download/page295/carint.zip -O /tmp/file.zip",  
        "cd /tmp", 
        "unzip file.zip", 
        "mv carint-html/* /var/www/html/",  
        "chown -R www-data:www-data /var/www/html/*",  
        "rm -rf file.zip carint-html",  
    ]
  }

  connection {
    type        = "ssh"
    user        = "ubuntu"
    private_key = file("${var.keyname}.pem")
    host        = self.public_ip
    timeout     = "4m"
  }

}
```  

## Terraform Modules   

- Modules are a collection of `.tf` files placed togather in a directory and can be referred from other `.tf` files.    
- Modules helps users to organize their terraform configuration so that they can be reusable.   
- Helps to keep your terraform code more clean and moduler.  

__Steps To Create a Module :__  

1. Create a folder and name it as your module. Example `ec2_module`, all the modules files resides inside this folder.  
2. Now write the configuration file for the resource that you want to create. For example suppose we want to create module to provision an ec2 instance with a sttaic web server. So in that case the files shoud be    

File: ec2.tf (contains ec2 instance configuration) 
```bash  
resource "aws_instance" "web01" {
  ami                    = var.ami
  instance_type          = var.web_instance_type
  key_name               = var.keyname
  vpc_security_group_ids = [aws_security_group.webSrv_SG.id]
  user_data              = <<-EOF
      #!/bin/bash
      sudo apt update -y
      sudo apt install apache2-bin apache2 unzip wget -y
      sudo systemctl enable apache2
      sudo systemctl start apache2
      wget https://www.free-css.com/assets/files/free-css-templates/download/page295/carint.zip -O /tmp/file.zip
      cd /tmp
      unzip file.zip 
      sudo mv carint-html/* /var/www/html/
      sudo chown -R www-data:www-data /var/www/html/*
      rm -rf file.zip carint-html
  EOF 
}
```  

File: sec-group.tf (contains security group configuration)  
```bash  
resource "aws_security_group" "webSrv_SG" {
  name        = "webSrv_SG"
  description = "Security Group for Static web server"

  # outgoing traffic rules
  egress {
    # allows all the port
    from_port        = 0
    to_port          = 0
    protocol         = "-1"
    cidr_blocks      = ["0.0.0.0/0"]
    ipv6_cidr_blocks = ["::/0"]
  }

  # incomming traffic rules
  ingress {
    from_port        = 80
    to_port          = 80
    protocol         = "tcp"
    cidr_blocks      = ["0.0.0.0/0"]
    ipv6_cidr_blocks = ["::/0"]
  }

  ingress {
    from_port        = 22
    to_port          = 22
    protocol         = "tcp"
    cidr_blocks      = ["0.0.0.0/0"]
    ipv6_cidr_blocks = ["::/0"]
  }
}
```   

File: key-generate.tf (contains ssh key generation configuration)  
```bash  
# generate the public-private key
resource "tls_private_key" "pk_generate" {
  algorithm = "RSA"
  rsa_bits  = "4096"
}

# store the generated private key in local disk 
resource "local_file" "web_srvKey_pem" {
  filename = "${var.keyname}.pem"
  content  = tls_private_key.pk_generate.private_key_pem
}

# create aws key-pair
resource "aws_key_pair" "websrvKey" {
  key_name   = var.keyname
  public_key = tls_private_key.pk_generate.public_key_openssh
}
```  

File: vars.tf 
```bash  
variable "ami" {
  type    = string
  default = "ami-0f5ee92e2d63afc18"
}

variable "keyname" {
  type    = string
  default = "websrvKey"
}

variable "web_instance_type" {
  type = string
}
```  

File: output.tf  
```bash   
output "srv_public_ip" {
  value = "http://${aws_instance.web01.public_ip}/"
} 
```  

Now we also nede to include `main.tf` file where we are going to define the appropriate version of terraform this module is comfortable with. Right now i am using terraform version 1.5.5, so the config would be 

File: main.tf  
```bash 
terraform {
  required_version = ">= 1.5.5"
}
```  

Now the structure of our module looks like  

```bash  
ec2_module/
Γö£ΓöÇΓöÇ README.md
Γö£ΓöÇΓöÇ ec2.tf
Γö£ΓöÇΓöÇ key-generate.tf
Γö£ΓöÇΓöÇ main.tf
Γö£ΓöÇΓöÇ output.tf
Γö£ΓöÇΓöÇ sec-group.tf
ΓööΓöÇΓöÇ vars.tf
```  

- I also added README.md file to explain what exactly this module do.  
- Also note that we define an empty variable variable "web_instance_type" in var.tf file which is used by ec2.tf config to determine instance type.  , so when we use this module then we have to provide the value for this variable.  
- Also the output block in output.tf does'nt going to execute itself, we also have to use/include that variable when we use the module.  

### Using the module   

- To use the module we have to define the module location in our main.tf file. which looks like    

```bash   
.
Γö£ΓöÇΓöÇ ec2_module   
ΓööΓöÇΓöÇ main.tf  
```  

- Code to include the module    

```bash   
module "module_name" {
  source               = "relative_path_of_module"
  module_variable_name1 = value1
  module_variable_name2 = value2
  ...
}
```   

- Give a name to the module    
- Provide the relative path of module folder, in our case it will be `.//ec2_module`    

```bash  
module "ec2_web_server" {
  source            = ".//ec2_module"
  web_instance_type = "t2.micro"
}
```  

- Where name of the module in `ec2_web_server` and we also provide the value for variable `web_instance_type` to `t2.micro`.  
- To use output variable we need to define the output block in main.tf file, the syntax to access module variable is  

```bash 
module.user_defined_module_name.variable_name
``` 

- Where `user_defined_module_name` means `ec2_web_server`  

```bash  
output "web_server_ip" {
  value = module.ec2_web_server.srv_public_ip
}
```  

Now the main.tf file looks like  

```bash   
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "5.16.2"
    }
  }
}

provider "aws" {
  region = "ap-south-1"
}

module "ec2_web_server" {
  source            = ".//ec2_module"
  web_instance_type = "t2.micro"
}

output "web_server_ip" {
  value = module.ec2_web_server.srv_public_ip
}
```  

Now run terraform commands to provision the infrastructure.  

## Terraform Remote Module Sources    

There are many module sources supported by terraform, some of them are:  

- Local paths   
- Terraform Registry   
- Github  
- Bit Bucket   
- S3 bucket   
- CGS Bucket    

__Local Paths :__  

Modules from Local paths    

```bash   
module "module_name" {
  source = "relative_path_of_module"
}
```   

Example : 

```bash   
// example 1
source = "../../modules/ec2_static_web"  

// example 2 
source = "./modules/ec2_static_web"  

// example 3
source = "../modules/ec2_static_web"   
```  

__Github Remote Modules :__  

- First push your module code into github repository     
- Copy the repository url and you can provide the url as source.  
- with public repositories  

If module code is in root repository 

```bash  
source = "git::https://github.com/Ajaytekam/TerraformModules.git"  
```  

If module code is inside subdirectory  

```bash  
source = "git::https://github.com/Ajaytekam/TerraformModules.git//subdirectory/<module_dir_name>"   
```  

Example :   

```bash   
source = "git::https://github.com/Ajaytekam/TerraformModules.git//modules/ec2_static_web"   
```   

Alternatively you can also use trimmed url 

```bash   
source = "github.com/Ajaytekam/TerraformModules"   

// or if modules are in subdirectory  
source = "github.com/Ajaytekam/TerraformModules//modules/ec2_static_web"     
```  

- With Private repositories   

- First create a token with limited access like only enable the `repo` scope on your github account.     
- Now there are mulltiple ways to get modules:    
- By putting token into github url like `source = "git::https://<TOKEN>@github.com/Ajaytekam/TerraformModules.git//modules/ec2_static_web"` in source. But this method is little bit insecure, because you have to hardcode the token value in terraform config file.   
- By configuring the git command line to store the token   

```bash  
git config --global url."https://ghp_jsfdskjfs23432432n432b43nmn455nb4@github.com/Ajaytekam".insteadOf "github.com/Ajaytekam"
```  

- Now whenever you pull something from private repo then it will going to replace github.com/Ajaytekam with token url, also if you use a particular repo for terraform module then you can also add the repo name like    

```bash  
git config --global url."https://ghp_jsfdskjfs23432432n432b43nmn455nb4@github.com/Ajaytekam/TerraformModules".insteadOf "github.com/Ajaytekam/TerraformModules"   
```  

__Referring a particular branch :__  

- To refer a particular branch add `?ref=branch_name` at the end of url. Example    

```bash  
source = "github.com/Ajaytekam/TerraformModules//modules/ec2_static_web?ref=main"     
```   

> Note: if you stored multiple modules in a repository in sub-directory structure like `modules/...multiple-modules` and you try to pull the only one module like `//modules/ec2_module`, in this case the terraform will download all of the modules in repository, but use only a particular module you have mentioned. This is the default behaviour.   

## Import Resources  

- Terraform allow users to import existing infrastructure.   
- To do that you have to first create the configuration files with ids (for example instance id of ec2 instances) and then letter by running `terraform import` command you will be able to import the configuration of infrastructure.  

__Example :__  

- Suppose you have a running ec2 instance, and you have some data/attributes related to that ec2 instance
- Now we have to wirte config file for that ec2 instance based on that attributes  

```bash 
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "5.16.1"
    }
  }
}

provider "aws" {
  region = "ap-south-1"
}

resource "aws_instance" "MyTest" {
    ami = "ami-0f5ee92e2d63afc18"
    instance_type = "t2.micro"
    subnet_id = "subnet-0a6cc2a752bc4fc82"
    vpc_security_group_ids = ["sg-0e0594cc32a3eab93"]
}
```   

- Based on the instance details we created the resource configuration.   
- Now we have to run the command `terraform import aws_instance.MyTest <instance_id>`  
- The command should be 

```bash  
terraform init

// importing the configurations  
terraform import aws_instance.MyTest i-0cfb4697909847c65
```   

- Now after importing you can list the imported resources  

```bash  
terraform state list  
```   

- Destroying the resources  

```bash  
terraform destroy --auto-approve  
```   

## Remote State Management         

Terraform can store states in various remote backends like amazon's Simple Storage Service (S3).  

Example: 

[Using S3 bucket as Terraform beckends](#using-s3-bucket-as-terraform-backend)  
[Terraform State Locking with DynamoDB](#terraform-state-lock)   

## Terraform Workspaces   

- Workspaces is like a virtual environment having a set of variables.  
- Workspaces are especially useful when you want to maintain separate configurations for different stages of your application's lifecycle, such as development, testing, and production.   
- Each workspace can have its own set of Terraform state files, allowing you to isolate and manage infrastructure changes independently.   
- There is always a `default` workspace created by terraform, so you always work in a `default` workspace of terraform.  

__workspace commands :__  

```bash    
Usage: terraform [global options] workspace

  new, list, show, select and delete Terraform workspaces.

Subcommands:
    delete    Delete a workspace
    list      List Workspaces
    new       Create a new workspace
    select    Select a workspace
    show      Show the name of the current workspace
```   

```bash  
// list workspaces     
terraform workspace list   

// create a new workspace   
terraform workspace new <workspace_name>   

// select a particular workspace  
terraform workspace select <workspace_name>    

// Show current workspace   
terraform workspace show   

// Delete a workspace  
terraform workspace delete <workspace_name>  
```   

__Usage of workspace :__ 

1. We can use var.workspace variable to select the appropriate instance type for ec2 instances using lookup function of a map, which determines which instance type to create on which workspace.  

```bash  
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
    }
  }
}

provider "aws" {
  region = "ap-south-1"
}

resource "aws_instance" "ec2" {
  ami           = "ami-02bb7d8191b50f4bb"
    instance_type = lookup(var.instances, terraform.workspace)  
}

variable "instances" {
    type = map(string)
    default = {
        default = "t2.small",
        dev = "t2.nano",
        stage = "t2.small",
        prod = "t2.micro"
    }
}
```  

- Now first create 3 workspaces like 

```bash   
terraform workspace new dev    
terraform workspace new stage  
terraform workspace new prod   
```  

- Change the workspace and run `terraform plan` command then you will see that based on the workspace the instance type will change.   

2. Another example of terraform workspace :  

```bash   
terraform {
  required_providers {
    aws = {
      source = "hashicorp/aws"
    }
  }
}

provider "aws" {
  region = "ap-south-1"
}

resource "aws_instance" "ec2" {
  ami           = "ami-02bb7d8191b50f4bb"
  instance_type = lookup(var.instances, terraform.workspace)
}

variable "instances" {
  type = map(string)
  default = {
    default = "t2.small",
    dev     = "t2.nano",
    stage   = "t2.small",
    prod    = "t2.micro"
  }
}
```   

__terraform.tfstate.d directory :__  

- Whenever you work with terraform workspace and when you create multiple workspaces then you will get one directory created for each workspace inside your terraform project.   
- So with each workspace, you end up with its own terraform.tfstate and terraform.tfstate.d file which will help you to separate and isolate the infrastructure behavior based on your configuration settings.    

## Securing Sensitive Information   

- Suppose we have local variable and if we use them, for example output variables, example   

```bash   
locals {
  db_password = {
    admin = "my-secret-passcode"
  }

  api_url = {
    url = "https://some_fucking_motherfucker_url"
  }
}

output "db_password" {
  value     = local.db_password
}


output "api_url_print" {
  value     = local.api_url
}
``` 

- Now if you run command `terraform apply --auto-approve` command then it will going to print out the passwords.   
- To hide the sensitive information we can use `sensitive = true` in output variable 

```bash  
locals {
  db_password = {
    admin = "my-secret-passcode"
  }

  api_url = {
    url = "https://some_fucking_motherfucker_url"
  }
}

output "db_password" {
  value     = local.db_password
  sensitive = true
}


output "api_url_print" {
  value     = local.api_url
  sensitive = true
}
```   

- You can also do the same thing with terraform variable 

```bash   
variable "password" {
   description = "Database password"
   type        = string
   sensitive   = true
} 
```   

## Create Resources in multiple Regions  

- To create same resource in multiple regions we have to use alias attribute of providers.    
- First we have to define multiple providers, with respective regions and have to give an alias name   

```bash  
provider "aws" {
  region = "us-east-1"
  alias  = "north_vergenia"
}

provider "aws" {
  region = "ap-south-1"
  alias  = "mumbai"
}
```   

- Now in resource definition we can use different provider using their alias

```bash  
resource "aws_eip" "res1" {
  provider = aws.north_vergenia
  domain   = "vpc"
}

resource "aws_eip" "res2" {
  provider = aws.mumbai
  domain   = "vpc"
}
```  

- For example for eip res1 we are using north-vergenia and for eip res2 we are using mumbai region.   
- Example: main.tf  

```bash   
provider "aws" {
  region = "us-east-1"
  alias  = "north_vergenia"
}

provider "aws" {
  region = "ap-south-1"
  alias  = "mumbai"
}

resource "aws_eip" "res1" {
  provider = aws.north_vergenia
  domain   = "vpc"
}

resource "aws_eip" "res2" {
  provider = aws.mumbai
  domain   = "vpc"
}

output "North-Vergenia-IP" {
  value = aws_eip.res1.public_ip
}

output "Mumbai-IP" {
  value = aws_eip.res2.public_ip
}
```  

## Create Resources in multiple Accounts   

Create the same resource in multiple user profile. Steps:  

- Create two users and add their credentials into aws credentials file (located in `~/.aws/credentials`)

```bash  
[user01]   
aws_access_key_id = ABCDEFGHIJKLMNOPQRS
aws_secret_access_key = aGRzc2Zkc2Zkc2ZzZGRkZGRkc2FkYXNmMj

[user02]  
aws_access_key_id = ABCDEFGHIJKLMNOPQRS
aws_secret_access_key = IxZWNob2hkc3NmZHNmZHNmc2RkZGRkZGZmCg
```   

- Now in the provider definition just add the user profile and set the alias as username for identification   

```bash   
provider "aws" {
  region  = "us-east-1"
  profile = "user01"
  alias   = "user01"
}

provider "aws" {
  region  = "ap-south-1"
  profile = "user02"
  alias   = "user02"
}
```     

- In the resource definition gives the provider details with alias.  

```bash    
resource "aws_eip" "res1" {
  provider = aws.user01
  domain   = "vpc"
}

resource "aws_eip" "res2" {
  provider = aws.user02
  domain   = "vpc"
}
```  

Example Code : 

```bash  
provider "aws" {
  region  = "us-east-1"
  profile = "user01"
  alias   = "user01"
}

provider "aws" {
  region  = "ap-south-1"
  profile = "user02"
  alias   = "user02"
}

resource "aws_eip" "res1" {
  provider = aws.user01
  domain   = "vpc"
}

resource "aws_eip" "res2" {
  provider = aws.user02
  domain   = "vpc"
}

output "North-Vergenia-IP" {
  value = aws_eip.res1.public_ip
}

output "Mumbai-IP" {
  value = aws_eip.res2.public_ip
}
```  
