What is Terraform?
-
- Open source IaC tool developed by hashicorp that lets us define and manage our cloud infrastructure using declarative configuration langauge
- Terraform allows to describe our infrastructure (servers, databases, networks) in code and provision it automatically across different cloud providers like AWS, AZURE, GCP

- It has declarative syntax, multi cloud support
- We can have xecution plan by preview what changes will be made before applying them
- We can keep track of existing infrastructure (terraform.tfstate)

- Workflow
  - Write configuration in .tf files
  - Initialize project with "terraform init"
  - Plan changes with "terraform plan"
  - Apply configuration with "terraform apply"
  - Destry infrastructure with "terraform destroy" if needed
 
----------------------------------------------------------------------------------  

What are providers in Terraform?
-
- In terraform, providers are plugins that allow terraform to interact with different external services like cloud platforms
- Provider is responsible for understanding API interactions with service we want to manage and exposing resources from that service
- Examples
  - AWS :- hashicorp/aws
  - Azure :- hashicorp/azurerm
  - GitHub :- integrations/github
  - Google cloud :- hashicorp/google
 
- To declare provider
-       provider "aws" {
-         region = "us-west-2"
-         profile = "deafult"
-       }
  - Here we tell terraform to use AWS provider and connect to "us-west-2" using default profile
 
- When we run init, terraform downloads necessary provider plugins
- When we run apply, terraform uses provider to interact with external API (create EC2 via AWS API)

----------------------------------------------------------------------------------

What is the difference between terraform apply and terraform plan?
-
- **Terraform plan**
  - Shows what terraform would do if you run apply
  - Helps you understand changes without actually making them
 
- **Terraform apply**
  - Actually executes changes required to reach desired state
  - Creates, modifies and destroys infra
  - Applies everything shows in plan

----------------------------------------------------------------------------------

What is a Terraform state file?
-
- terraform.tfstate is a local or remote JSON file that tracks actual state of infra
- Terraform compares .tf config with state file to determine what needs to be created, updated and destroyed
- This file contains :- resource names and types, provider config, resource IDs, dependency graphs
- When we run
  - terraform init :- it sets up env
  - terraform apply :- it updates terraform.tfstate file after provisioning
 
- This file may contain sensitive data (password, access tokens)
- So use version control carefully

- terraform show : view current state
- terraform state list : list tracked resources
- terraform state mv : move items within state
- terraform state rm : remove resources from state without destroying them

----------------------------------------------------------------------------------

What is the purpose of terraform init?
-
- It is first command we should run when starting new or existing terraform project
- It initializes terraform working directory by
  - Downloading required provider plugins
  - Setting up backend config
  - preparing project for further commands like plan and apply
 
- It creates .terraform/ directory to store plugins and modules
- It also creates .terraform.lock.hcl to lock provider version to ensure consistency across machines

----------------------------------------------------------------------------------

What are variables in Terraform?
-
- Variables in terraform are used to make configurations flexible, reusable and cleaner by avoiding hardcoding values like region name, AMI IDs or instance types
- We define them once and then pass values when running terraform
- Types :-
  - Input :- Used to parameterize code
  - Output :- Used to export values after apply
  - Environment variables :- Can pass inputs via shell env
 
- To assign values to variable
  - Using CLI :- terraform apply -var="instance_type=t2.micro"
  - .tfvars file :- terraform apply -var-file="dev.tfvars"

----------------------------------------------------------------------------------

What is the difference between var and local?
-
- var is inout variables while local is local values
- var accepts values from users while local defines internal constants and expressions
- var is defined in variable block while local in locals block
- var scope is global while local is local
- var is used to customize config, pass parameters whereas local is used to reuse computed values

![image](https://github.com/user-attachments/assets/28112726-d27e-4cf7-899f-7458cca69042)

![image](https://github.com/user-attachments/assets/29977c48-1af3-446f-96b4-17f428936274)

----------------------------------------------------------------------------------

What are data sources in Terraform?
-
- Data Sources in terraform are used to read info from existing resources outside of Terraform's direct control, so we can use them in our terraform configuration.
- Use cases
  - Fetching latest AMI for EC2
  - Looking up SG IDs and VPC IDs
  - Reading secrets from AWS secrets manager
 
----------------------------------------------------------------------------------

Explain the difference between terraform state, terraform show, and terraform refresh.
-
- Terraform state
  - Used to interact directly with state file
  - Lets us inspect, manipulate or modify contents of state
  - Used for debugging scenarios
  - e.g :- terraform state list, terraform state rm $name, terraform state mv $name
 
- Terraform show
  - Displays current state of file or saved plan
  - Converts .tf file into readable format
  - We can also use it to preview plan file
  - e.g :- terraform show, terraform show terraform.tfstate
  - We can use to review infra details
 
- Terraform refresh
  - Not used now
  - Was used to sync state file with real infra

----------------------------------------------------------------------------------

How do you manage secrets or sensitive data in Terraform?
-
- When working with terraform, managing secrets or sensitive data (like passwords, API keys, tokens) securely is critical.

- Use sensitive = true in variables
  - Mark variables as sensitive to hide values in output/logs

![image](https://github.com/user-attachments/assets/d951f729-1e60-4b12-a1b4-540f6a42bcd7)

- Dont hardcode secrets in code
  - Pass secrets via .tfvars file, env variables, secret manager

 ![image](https://github.com/user-attachments/assets/bddd2a30-9340-4227-ae02-ba1807a61506)

- Use env variables
  - Set env vars with TF_VAR_ prefix before running terraform
 
- Leverage secrets manager

----------------------------------------------------------------------------------

How do you handle remote state in Terraform?
-
- Remote state allows terraform to store terraform.tfstate file in shared, remote location instead of locally on our machine
- This helps collaboration of teams, prevent conflicting updates

- Why use remote state?
  - If state file gets lost, use remote backend
  - If multiple users overwrite state, state locking
  - If we need history of infra changes, use state versioning
  - If we've sensitive data in state, use remote encryption
 
-----------------------------------------------------------------------------------

What are Terraform modules and how are they used?
-
- Module in tf is a container for multiple resources that are used together
- Use modules for reusability, cleaner code, consistency, maintenance

- Types of modules
  - Root module :- main folder where tf is run
  - Child module :- module called from another module
  - External module :- hosted on GitHub, tf registry

- Modules are called with module block
 
-----------------------------------------------------------------------------------

What happens when a resource is removed from the Terraform code?
- 
- If we remove resource block from .tf files and run terraform apply, terraform will destroy that resource from our infra
- Created resource will be in terraform code, terraform state file and real infra. If we delete resource block from code and do terraform apply to detroy the resource

-----------------------------------------------------------------------------------

How do you import existing infrastructure into Terraform?
-
- terraform import brings existing infra under terraforms control by writing it into state file without changing or recreating actual resource

- First write resource block
- Then run import command :- terraform import aws_s3_bucket.example my-existing-bucket
- terraform plan

- EC2 Instance :-	terraform import aws_instance.web i-0abcd1234ef567890
- S3 Bucket :-	terraform import aws_s3_bucket.my_bucket my-existing-bucket
- Security Group :-	terraform import aws_security_group.my_sg sg-0123456789abcdef0

- It only imports state
