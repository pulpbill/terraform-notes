# terraform-notes

Sharing 101 notes that I'll be using at work. Since TF docs are quite clear and there are plenty of examples, I'll write basic concepts and resources (kind of a cheatsheet) if you want to get started with Terraform.

> **Important: I assume in these notes that you already have AWS credentials / IAM roles in place and I uploaded a functional (as of 05/09/21) basic main.tf file with its lock.hcl and .gitignore that will spin up an AWS EC2 instance (applicable for free tier users):**

https://learn.hashicorp.com/tutorials/terraform/aws-build?in=terraform/aws-get-started - Getting started with AWS

https://registry.terraform.io/providers/hashicorp/aws/latest/docs - AWS as your provider
 
https://learn.hashicorp.com/tutorials/terraform/cloud-workspace-configure - If you use TF Cloud

## Installation:

### Ubuntu/Debian:

* Ensure that your system is up to date, and you have the gnupg, software-properties-common, and curl packages installed. You will use these packages to verify HashiCorp's GPG signature, and install HashiCorp's Debian package repository:

  ```
  sudo apt-get update && sudo apt-get install -y gnupg software-properties-common curl
  ```

* Add the HashiCorp GPG key:

  ```
  curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -
  ```

* Add the official HashiCorp Linux repository:

  ```
  sudo apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
  ```

* Update to add the repository, and install the Terraform CLI (**if you have and old version of TF, this will updated it**):

  ```
  sudo apt-get update && sudo apt-get install terraform
  ```

## FAQs:

* It's an orchestration tool to provision infrastructure (IaC: Infrastructure as code)

* Immutable based approach

* Agentless (it's client only, you don't need anything on the provider side, just the credentials in your machine and/or cloud if you use TF cloud)

* Declarative language

* You can store and versioning states

* It uses HCL (HashiCorp Configuration Language)

* Open Source License

## Components:

* **Providers**: Providers are a logical abstraction of an upstream API. They are responsible for understanding API interactions and exposing resources. You'll use and browse through documentation for the provider you're using in order to deploy whatever you need (it's the first step before writing your modules). 

* **Modules**: A module is a set of terraform configuration files in a single directory. Even a simple configuration consisting of a single directory with one or more .tf files is a module. This could be just one quick main.tf or many directories where you isolate per environment the type of services or resources that you are using (ideally, also having .tf and .tfvars files for each environment).

  > Note: Keep an eye on how to use/call nested modules.
 
* **Data sources**: terraform uses data sources to fetch information from cloud provider APIs, such as disk image IDs, or information about the rest of your infrastructure through the outputs of other terraform configurations. Data sources allow you to load data from APIs or other terraform workspaces (you can even use it between workspaces on TF cloud and enterprise).

* **States**: *.tfstate* Keep track of resources created by your configuration (modules) and maps them to real-world resources. You should not directly interact with state files.
When you execute *terraform plan* a *.terraform.state* file will automatically be created. It will then store your changes (commands), like: plan - apply - destroy - show. It stores  metadata used to know what you have planned (either local or in TF cloud) and what you actually have deployed in whatever provider you've chosen.

  > Note: You can move and switch states, but it's an advanced feature and out of the scope for this 101*.

* **Input Variables**: They serve as parameters for a terraform module, allowing aspects of the module to be customized without altering the module's own source code, and allowing modules to be shared between different configurations. Think of them as function arguments in coding languages.

* **Output Values**: Are return values of a terraform module. Think of them just as any other output as you are used to. (eg: An AWS EC2 instance IP address).

  > Note: Outputs are only rendered when terraform applies your plan. Running terraform plan will not render outputs.

* **Local Values**: Use them to assign a name to an expression, so you can use it multiple times within a module without repeating it.

  > Note: They only can be used within the module where they were declared. Also, notice that local values are created by a **locals** block (plural), but you reference them as attributes on an object named **local** (singular).

* **Lock file**: At present, the dependency lock file tracks only provider dependencies. Terraform automatically creates or updates the dependency lock file (*.terraform.lock.hcl*) each time you run the *terraform init* command. You should include this file in your version control repository so that you can discuss potential changes to your external dependencies via code review.

## Commands:

* **init**: 
  ```
  terraform init
  ```
  When you create a new configuration — or check out an existing configuration from version control — you need to initialize the directory with terraform init.             Initializing a configuration directory downloads and installs the providers defined in the configuration in a hidden directory called *.terraform*, which in this case   is the **aws provider**. Also, creates a **lock file** named: *.terraform.lock.hcl* which specifies the exact provider versions used.

* **validate**: 
  ```
  terraform validate
  ```
  It validates the configuration files in a directory, referring only to the configuration and not accessing any remote services such as remote state, provider APIs,       etc. It verifies whether a configuration is syntactically valid and internally consistent, regardless of any provided variables or existing state. It is thus primarily   useful for general verification of reusable modules, including correctness of attribute names and value types. It would be useful to run a test step at a CI pipeline     when you're re-using modules. 

  > Note: It requires an initialized working directory with any referenced plugins and modules installed. To initialize a working directory for validation without accessing any configured remote backend, use:

  ```
  terraform init -backend=false
  ```

* **plan**:
  ```
  terraform plan
  ```
  It creates an execution plan. By default, creating a plan consists of:

  * Reading the current state of any already-existing remote objects to make sure that the terraform state is up-to-date.
  * Comparing the current configuration to the prior state and noting any differences.
  * Proposing a set of change actions that should, if applied, make the remote objects match the configuration.

  If it detects that no changes are needed to resource instances or to root module output values, it will report that no actions need to be taken. Also, the "apply"       command automatically generates a new plan and prompts for you to approve it.

* **apply**:
  ```
  terraform apply
  ```
  It executes the actions proposed in a terraform plan. Implicitly creates a **plan** and all modes/options that applies to terraform plan command, are available to the   apply command.
  If you add ```-auto-approve``` as an option to apply command, it won't prompt you for approval.

  > Note: You could specify a previously saved plan file to apply, in this case, it won't prompt for approval.

* **destroy**:
  ```
  terraform destroy
  ```
  It's a convenient way to destroy all remote objects managed by a particular terraform configuration. It's an alias of ```terraform apply -destroy``` that's why it       accepts most of the command options as **terraform apply** command.
  If you like to run a "dry run", execute: ```terraform plan -destroy```.

  > Note: check also "show" and "state list" commands; they might come in handy.

### Differences between variable files:

For the sake of simplicity, and since this is a topic that it will vary from one use case to another, we'll keep it simple:
* Use variables.tf to declare your variable types
* Use terraform.tfvars to assign values to your previously defined variables

There are many ways to declare and set your variables. I recommend you go through the docs, not just to expand your knowledge but things might change or be deprecated.

### Variable Definition Precedence:

The above mechanisms for setting variables can be used together in any combination. If the same variable is assigned multiple values, terraform uses the last value it finds, overriding any previous values. Note that the same variable cannot be assigned multiple values within a single source.

Terraform loads variables in the following order, with later sources taking precedence over earlier ones:

* Environment variables
* The terraform.tfvars file, if present.
* The terraform.tfvars.json file, if present.
* Any *.auto.tfvars or *.auto.tfvars.json files, processed in lexical order of their filenames.
* Any -var and -var-file options on the command line, in the order they are provided. (This includes variables set by a Terraform Cloud workspace.)

---

## What's next?

I highly recommend you to understand (and find the best approach for your team) why it's important to have a centralized, versioned and protectd state file:

https://www.terraform.io/docs/language/settings/backends/s3.html

You'll store the state file at AWS S3 and the lock file at AWS DynamoDB.
Or, you could go with TF Cloud/Enterprise if it suits your need/budget:

https://www.terraform.io/docs/cloud/index.html

The process of working with any IaC tool should follow the GitOps principle. Your team should use a VCS for infrastructure and do QA for the PRs as part of your daily basis.

> I'll try to upload a full example of TF/S3/DynamoDB if I have the time.

---

**Sources**:

https://www.terraform.io/docs/language/values/variables.html#variable-definition-precedence

https://learn.hashicorp.com/terraform

https://registry.terraform.io/browse/providers

https://learn.hashicorp.com/tutorials/terraform/aws-build?in=terraform/aws-get-started

https://www.terraform.io/docs/language/modules/develop/structure.html

TechWorld with Nana: https://www.youtube.com/watch?v=l5k1ai_GBDE (check out other TF videos from her, it helped me a lot when I started).
