# Terraform Multi-Environment Infrastructure

A Terraform project for provisioning AWS EC2 infrastructure across separate **Dev, Test, and Prod environments** using Terraform workspaces, variables, data sources, and environment-specific configuration files.

## Project Overview

This project demonstrates how the **same Terraform configuration** can be reused for multiple environments without hardcoding environment-specific values.

```text
                    Terraform
                       │
                  Common Code
                    main.tf
                       │
          ┌────────────┼────────────┐
          │            │            │
         DEV          TEST         PROD
          │            │            │
      tfvars.dev   tfvars.test   tfvars.prod
          │            │            │
      Workspace     Workspace     Workspace
          │            │            │
          ▼            ▼            ▼
       AWS EC2      AWS EC2      AWS EC2
```

## Features

* Multi-environment infrastructure using Terraform workspaces
* Separate `dev`, `test`, and `prod` environments
* Environment-specific `.tfvars` configuration
* Reusable Terraform configuration
* Dynamic AWS AMI selection
* Dynamic VPC and subnet discovery
* Dynamic Availability Zone discovery
* Different EC2 instance types per environment
* Different EC2 instance counts per environment
* Environment-based resource tagging
* Terraform state management
* Infrastructure provisioning and destruction through Terraform

## Tech Stack

* **Terraform**
* **AWS**
* **Amazon EC2**
* **AWS VPC**
* **PowerShell / Windows**
* **Git & GitHub**

## Project Structure

```text
terraform-multi-env/
│
├── main.tf
├── provider.tf
├── variables.tf
├── terraform.tf
│
├── terraform.tfvars.dev
├── terraform.tfvars.test
├── terraform.tfvars.prod
│
├── .gitignore
└── README.md
```

### File Responsibilities

| File                    | Purpose                                                   |
| ----------------------- | --------------------------------------------------------- |
| `provider.tf`           | Configures AWS provider and region                        |
| `variables.tf`          | Defines Terraform input variables                         |
| `main.tf`               | Defines AWS infrastructure                                |
| `terraform.tf`          | Terraform/backend-related configuration                   |
| `terraform.tfvars.dev`  | Dev environment values                                    |
| `terraform.tfvars.test` | Test environment values                                   |
| `terraform.tfvars.prod` | Prod environment values                                   |
| `.gitignore`            | Prevents Terraform state/cache files from being committed |
| `README.md`             | Project documentation                                     |

## Environment Configuration

### Dev

```text
Environment: dev
Instance Type: t3.micro
Instance Count: 1
```

### Test

Configuration is maintained separately through:

```text
terraform.tfvars.test
```

### Prod

```text
Environment: prod
Instance Type: t3.small
Instance Count: 3
```

The important concept is that **`main.tf` remains the same** while the environment-specific values are supplied through `.tfvars` files.

## Terraform Workflow

### 1. Initialize

```powershell
terraform init
```

Downloads the required Terraform providers and initializes the project.

### 2. Format

```powershell
terraform fmt
```

Formats Terraform configuration files.

### 3. Validate

```powershell
terraform validate
```

Checks whether the Terraform configuration is syntactically and structurally valid.

### 4. Select Environment

```powershell
terraform workspace list
```

View available workspaces.

```powershell
terraform workspace select dev
```

Switch to the Dev workspace.

### 5. Plan

```powershell
terraform plan -var-file="terraform.tfvars.dev"
```

Shows what Terraform intends to create, modify, or destroy.

### 6. Apply

```powershell
terraform apply -var-file="terraform.tfvars.dev"
```

Creates the infrastructure in AWS.

For Prod:

```powershell
terraform workspace select prod
terraform apply -var-file="terraform.tfvars.prod"
```

### 7. Check State

```powershell
terraform state list
```

Shows resources currently managed by Terraform.

```powershell
terraform show
```

Displays the current Terraform state.

### 8. Destroy

To remove an environment's infrastructure:

```powershell
terraform destroy -var-file="terraform.tfvars.dev"
```

For Prod:

```powershell
terraform destroy -var-file="terraform.tfvars.prod"
```

## Data Sources

The project avoids hardcoding AWS infrastructure IDs where possible.

Terraform dynamically retrieves information such as:

* Default VPC
* Default subnets
* Availability Zones
* Amazon Linux AMI

Conceptually:

```text
Terraform
   │
   ├── Find VPC
   ├── Find Subnets
   ├── Find Availability Zones
   └── Find AMI
          │
          ▼
      Create EC2
```

## Variables

Variables make the infrastructure reusable.

Example:

```hcl
variable "instance_type" {
  type = string
}
```

The actual value is provided through an environment-specific file:

```hcl
instance_type = "t3.micro"
```

The resource then uses:

```hcl
instance_type = var.instance_type
```

This prevents environment-specific values from being hardcoded into the main infrastructure configuration.

## Environment Isolation

Terraform workspaces maintain separate state for different environments:

```text
dev workspace
     │
     └── Dev state

test workspace
     │
     └── Test state

prod workspace
     │
     └── Prod state
```

This allows the same Terraform codebase to manage different environments independently.

## AWS Authentication

AWS credentials should be configured through the AWS CLI:

```powershell
aws configure
```

Authentication can be verified with:

```powershell
aws sts get-caller-identity
```

Do not commit AWS credentials, access keys, secrets, Terraform state, or `.terraform/` into Git.

## Example Deployment

### Dev

```powershell
terraform workspace select dev
terraform plan -var-file="terraform.tfvars.dev"
terraform apply -var-file="terraform.tfvars.dev"
```

Expected infrastructure:

```text
DEV
└── 1 × t3.micro EC2
```

### Prod

```powershell
terraform workspace select prod
terraform plan -var-file="terraform.tfvars.prod"
terraform apply -var-file="terraform.tfvars.prod"
```

Expected infrastructure:

```text
PROD
├── 1 × t3.small EC2
├── 1 × t3.small EC2
└── 1 × t3.small EC2
```

## Key Concepts Demonstrated

This project demonstrates:

* Infrastructure as Code (IaC)
* Terraform providers
* Terraform resources
* Terraform variables
* `.tfvars` files
* Terraform workspaces
* Terraform state
* AWS data sources
* Environment separation
* Dynamic infrastructure discovery
* Infrastructure planning
* Infrastructure provisioning
* Infrastructure destruction
* Avoiding hardcoded AWS resource IDs

## Author

**Ankit Kumar**
---
