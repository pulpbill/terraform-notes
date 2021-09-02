# terraform-notes

# This is a [WIP]

Sharing 101 notes that I'll be using at work. Since TF docs are quite clear and there are plenty of examples, I'll wrote basic concepts and resources if you want to get started with Terraform.

## Installation:

### Ubuntu/Debian:

-Ensure that your system is up to date, and you have the gnupg, software-properties-common, and curl packages installed. You will use these packages to verify HashiCorp's GPG signature, and install HashiCorp's Debian package repository:

```
sudo apt-get update && sudo apt-get install -y gnupg software-properties-common curl
```

-Add the HashiCorp GPG key:

```
curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -
```

-Add the official HashiCorp Linux repository:

```
sudo apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
```

-Update to add the repository, and install the Terraform CLI (**if you have and old version of TF, this will updated it**):

```
sudo apt-get update && sudo apt-get install terraform
```

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

-**States**: *.tfstate* Keep track of resources created by your configuration (modules) and maps them to real-world resources. You should not directly interact with state files.
When you execute *terraform plan* a *.terraform.state* file will automatically be created. It will then store your changes (commands), like: plan - apply - destroy - show. It stores  metadata used to know what you have planned (either local or in TF cloud) and what you actually have deployed in whatever provider you've chosen.

*Note: You can move and switch states, but it's an advanced feature and out of the scope for this 101*.

-**Input Variables**:

-**Output Values**:

-**Local Values**:

-**Lock file**: At present, the dependency lock file tracks only provider dependencies.Terraform automatically creates or updates the dependency lock file (*.terraform.lock.hcl*) each time you run the *terraform init* command. You should include this file in your version control repository so that you can discuss potential changes to your external dependencies via code review.

## Commands:

-**terraform init**: When you create a new configuration — or check out an existing configuration from version control — you need to initialize the directory with terraform init. Initializing a configuration directory downloads and installs the providers defined in the configuration in a hidden directory called *.terraform*, which in this case is the **aws provider**. Also, creates a **lock file** named: *.terraform.lock.hcl* which specifies the exact provider versions used.

```
terraform init
```

### Differences between variable files:

-**.tf**:

  
-**.tfvars**:


---
**Sources**:

https://learn.hashicorp.com/terraform

https://registry.terraform.io/browse/providers

https://learn.hashicorp.com/tutorials/terraform/aws-build?in=terraform/aws-get-started
