# terraform-notes

# This is a [WIP]

Sharing 101 notes that I'll be using at work. Since TF docs are quite clear and there are plenty of examples, I'll wrote basic concepts and resources if you want to get started with Terraform.

## FAQs:

-It's an orchestration tool to provision infrastructure (IaC: Infrastructure as code)

-Immutable based approach

-Agentless (it's client only, you don't need anything on the provider side, just the credentials in your machine and/or cloud if you use TF cloud)

-Declarative language

-You can store and versioning states

-It uses HCL (HashiCorp Configuration Language)

-Open Source License

## Components:

-**Providers**: Providers are a logical abstraction of an upstream API. They are responsible for understanding API interactions and exposing resources. You'll use and browse through documentation for the provider you're using in order to deploy whatever you need (it's the first step before writing your modules). 

-**Modules**: A module is a set of Terraform configuration files in a single directory. Even a simple configuration consisting of a single directory with one or more .tf files is a module. This could be just one quick main.tf or many directories where you isolate per environment the type of services or resources that you are using (ideally, also having .tf and .tfvars files for each environment).

-**Data sources**: Terraform uses data sources to fetch information from cloud provider APIs, such as disk image IDs, or information about the rest of your infrastructure through the outputs of other Terraform configurations. Data sources allow you to load data from APIs or other Terraform workspaces (you can even use it between workspaces on TF cloud and enterprise).

-**States**:

-**Input Variables**:

-**Output Values**:

-**Local Values**:

### Differences between variable files:

-**.tf**:

  
-**.tfvars**:


---
**Sources**:

https://learn.hashicorp.com/terraform

https://registry.terraform.io/browse/providers

