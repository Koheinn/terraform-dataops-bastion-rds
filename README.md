# DataOps with Terraform — AWS Bastion Host & Private RDS

![Terraform](https://img.shields.io/badge/Terraform-IaC-7B42BC?logo=terraform\&logoColor=white)
![AWS](https://img.shields.io/badge/AWS-Cloud-232F3E?logo=amazonaws\&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-Database-4169E1?logo=postgresql\&logoColor=white)
![EC2](https://img.shields.io/badge/AWS-EC2-orange?logo=amazonec2\&logoColor=white)
![RDS](https://img.shields.io/badge/AWS-RDS-527FFF?logo=amazonrds\&logoColor=white)
![DataOps](https://img.shields.io/badge/DataOps-Infrastructure%20Automation-blue)

## 📌 Project Overview

This project demonstrates the implementation of **DataOps principles using Terraform as an Infrastructure as Code (IaC) tool**.

The infrastructure consists of a private **Amazon RDS PostgreSQL database** and an **Amazon EC2 bastion host**. The RDS database is deployed inside a private subnet and is not directly accessible from the public internet. The EC2 instance is deployed in a public subnet and acts as a secure gateway to the private database.

Terraform is used to automate the creation, configuration, and destruction of the required AWS infrastructure.

The project was completed as part of **Course 2 of the DeepLearning.AI Data Engineering Professional Certificate on Coursera**.

---

## 🏗️ Architecture

The infrastructure follows a bastion-host architecture:

```text
                    Internet
                        │
                        │
                     SSH / EC2
                        │
                        ▼
              ┌───────────────────┐
              │   Public Subnet   │
              │                   │
              │   EC2 Bastion     │
              │      Host         │
              └─────────┬─────────┘
                        │
                        │ PostgreSQL
                        │ Port 5432
                        ▼
              ┌───────────────────┐
              │   Private Subnet  │
              │                   │
              │   Amazon RDS      │
              │   PostgreSQL      │
              └───────────────────┘
```

### Architecture Components

* **Amazon VPC** — Provided networking environment
* **Public Subnet** — Hosts the bastion EC2 instance
* **Private Subnets** — Host the RDS database
* **Amazon EC2** — Bastion/jump host for secure database access
* **Amazon RDS PostgreSQL** — Private relational database
* **Security Groups** — Control network access between resources
* **IAM Role & Instance Profile** — Provides permissions to the EC2 instance
* **Terraform** — Provisions and manages infrastructure
* **TLS Provider** — Generates the SSH key pair
* **PostgreSQL** — Used to create and query the training dataset

---

## 🎯 Project Objectives

The main objectives of this project were to:

* Apply **Infrastructure as Code (IaC)** using Terraform
* Deploy AWS infrastructure programmatically
* Configure a private RDS PostgreSQL database
* Deploy an EC2 bastion host in a public subnet
* Configure AWS security groups
* Generate and manage an SSH key pair using Terraform
* Securely access a private database through a bastion host
* Create and populate a PostgreSQL table
* Query and inspect database data
* Use Terraform outputs to retrieve infrastructure information
* Practice infrastructure lifecycle management with `terraform destroy`

---

## 🛠️ Technologies & Tools

| Technology      | Purpose                         |
| --------------- | ------------------------------- |
| Terraform       | Infrastructure as Code          |
| AWS             | Cloud infrastructure            |
| Amazon VPC      | Network environment             |
| Amazon EC2      | Bastion host                    |
| Amazon RDS      | PostgreSQL database             |
| PostgreSQL      | Database management and queries |
| AWS IAM         | Identity and access management  |
| Security Groups | Network access control          |
| TLS             | SSH key generation              |
| Git & GitHub    | Version control                 |

---

## 📁 Project Structure

```text
terraform/
│
├── main.tf
├── variables.tf
├── outputs.tf
├── backend.tf
├── terraform.tfvars
│
└── modules/
    └── bastion_host/
        ├── ec2.tf
        ├── rds.tf
        ├── network.tf
        ├── iam_roles.tf
        ├── policies.tf
        ├── variables.tf
        ├── outputs.tf
        └── providers.tf
```

### Root Terraform Configuration

The root configuration is responsible for:

* Calling the `bastion_host` module
* Passing infrastructure variables to the module
* Managing Terraform backend configuration
* Exporting module outputs

### Bastion Host Module

The module contains the infrastructure configuration for:

* Networking data sources
* Security groups
* RDS PostgreSQL
* EC2 bastion host
* IAM resources
* SSH key generation
* Terraform outputs

---

# ⚙️ Terraform Configuration

## 1. Backend Configuration

Terraform state is configured using a local backend.

The backend stores the Terraform state in a location associated with the assigned AWS account.

The state file allows Terraform to keep track of the infrastructure it manages.

> **Security Note:** Terraform state files can contain sensitive information. They should not be committed to a public repository.

---

## 2. Variables

Terraform variables are used to make the configuration reusable.

The configuration includes variables for resources such as:

* VPC ID
* Public subnet IDs
* Private subnet IDs
* Database master username
* Resource naming configuration

Environment-specific values are provided through:

```text
terraform.tfvars
```

Sensitive values and environment-specific configuration should not be committed to GitHub.

---

## 3. Networking

The VPC and subnets used in this project were provided as existing AWS resources.

Terraform retrieves these resources using data sources.

Example:

```hcl
data "aws_subnet" "private_a" {
  id = var.private_subnet_a_id
}
```

Security groups are created for:

* The RDS database
* The bastion host

This allows network traffic to be restricted according to the intended architecture.

---

# 🗄️ Amazon RDS PostgreSQL

The project provisions an Amazon RDS PostgreSQL database in private subnets.

The database configuration includes:

* PostgreSQL engine
* `db.t3.micro` instance class
* Private subnet group
* RDS security group
* Generated database password
* Configurable master username

The database is intentionally configured as **private**, meaning it cannot be accessed directly from the public internet.

This improves the security of the database by requiring access through the bastion host.

---

# 🖥️ Bastion Host

An Amazon EC2 instance is deployed into the public subnet.

The EC2 instance acts as a **bastion host**, also known as a jump server.

Its purpose is to provide a controlled access point between an external user and resources located inside the private network.

The bastion host uses:

```text
t3.nano
```

Terraform generates an SSH key pair using the TLS provider.

The public key is registered with AWS as an EC2 key pair, while the private key is saved locally for authentication.

---

# 🔐 Secure Database Access

The database is not publicly accessible.

The connection flow is:

```text
User
 │
 │ SSH / EC2 Instance Connect
 ▼
Bastion Host
 │
 │ PostgreSQL : 5432
 ▼
Private RDS PostgreSQL
```

This architecture demonstrates how a bastion host can be used to securely access resources inside a private network.

---

# 🚀 Deployment

## Initialize Terraform

Navigate to the Terraform directory:

```bash
cd terraform
```

Initialize the Terraform working directory:

```bash
terraform init
```

This downloads and initializes the required Terraform providers.

---

## Generate an Execution Plan

Before creating resources, review the proposed infrastructure changes:

```bash
terraform plan
```

The plan shows which resources Terraform intends to create, modify, or destroy.

---

## Deploy the Infrastructure

Apply the configuration:

```bash
terraform apply
```

Confirm the deployment by entering:

```text
yes
```

Terraform then creates the required AWS infrastructure.

The deployment may take several minutes, particularly while Amazon RDS is being provisioned.

---

# 📤 Terraform Outputs

After deployment, Terraform provides useful infrastructure information through outputs.

Examples include:

```bash
terraform output
```

The database host can be retrieved with:

```bash
terraform output db_host
```

The generated database password can be retrieved with:

```bash
terraform output db_master_password
```

> **Security Warning:** Never expose database passwords or other secrets in a public GitHub repository.

---

# 🔎 Testing Private RDS Access

Attempting to connect directly to the RDS endpoint from outside the private network should fail because the database is private.

Example:

```bash
psql -h <RDS-HOST> -U postgres_admin -p 5432 -d postgres --password
```

The intended architecture requires connecting through the bastion host.

---

# 🔑 Connecting Through the Bastion Host

The bastion host can be accessed using AWS EC2 Instance Connect.

After connecting to the EC2 instance, PostgreSQL can be accessed using:

```bash
psql -h <RDS-HOST> -U postgres_admin -p 5432 -d postgres --password
```

Enter the database password when prompted.

This demonstrates successful connectivity from the public bastion host to the private RDS database.

---

# 🗃️ Database Operations

After connecting to PostgreSQL, the provided SQL scripts can be used to create and populate the database.

## Create the Table

```sql
\i sql/ratings_table_ddl.sql
```

## Populate the Table

```sql
\i sql/copy_data.sql
```

## Inspect the Data

```sql
SELECT * FROM ratings_training LIMIT 10;
```

The dataset used in the project is:

```text
data/ratings_ml_training_dataset.csv
```

The database can also be inspected using:

```sql
SELECT COUNT(*) FROM ratings_training;
```

To exit PostgreSQL:

```sql
\q
```

---

# 🧹 Destroy the Infrastructure

One of the major advantages of Infrastructure as Code is the ability to reproduce and remove infrastructure consistently.

To destroy the resources created by Terraform:

```bash
terraform destroy
```

Confirm with:

```text
yes
```

Terraform removes the infrastructure that it manages.

> Always verify that you are working in the correct Terraform environment before running `terraform destroy`.

---

# 📚 Key Learning Outcomes

Through this project, I gained practical experience with:

### Infrastructure as Code

Using Terraform to define cloud infrastructure through declarative configuration rather than manually creating resources through the AWS console.

### AWS Networking

Understanding the relationship between:

* VPCs
* Public subnets
* Private subnets
* Security groups
* EC2
* RDS

### Secure Database Architecture

Learning why production databases should generally remain private and how a bastion host can provide controlled access.

### Terraform Modules

Using a reusable Terraform module to organize infrastructure configuration.

### Terraform State

Understanding how Terraform state tracks the resources managed by Terraform.

### Infrastructure Lifecycle

Practicing the complete Terraform workflow:

```text
terraform init
       ↓
terraform plan
       ↓
terraform apply
       ↓
Infrastructure
       ↓
terraform destroy
```

### Data Engineering Infrastructure

Connecting infrastructure automation with database deployment and data operations, which is an important part of modern DataOps workflows.

---

# 🎓 Course Context

This project was completed as part of the:

**DeepLearning.AI Data Engineering Professional Certificate**

**Course 2 — Data Engineering-related infrastructure/DataOps lab**

The project provided hands-on experience applying Terraform to cloud infrastructure and securely accessing a PostgreSQL database deployed on AWS.

---

# ⚠️ Security Considerations

This project involves AWS infrastructure and credentials, so sensitive information should never be committed to GitHub.

Do **not** commit:

```text
terraform.tfstate
terraform.tfstate.*
*.pem
*.key
terraform.tfvars
.env
```

A suitable `.gitignore` should be used.

Example:

```gitignore
# Terraform
.terraform/
*.tfstate
*.tfstate.*
*.tfvars

# Terraform crash logs
crash.log
crash.*.log

# SSH private keys
*.pem
*.key

# Environment variables
.env

# Terraform lock file can optionally be committed
# .terraform.lock.hcl
```

For real-world projects, secrets should be managed using an appropriate secrets-management solution rather than being stored directly in source code.

---

# 💡 What This Project Demonstrates

This project demonstrates the ability to combine **cloud infrastructure, Terraform automation, networking, security, and database technologies** into a working DataOps workflow.

The overall architecture can be summarized as:

```text
Terraform
    │
    ├── AWS Networking
    │
    ├── Security Groups
    │
    ├── EC2 Bastion Host
    │
    ├── IAM
    │
    └── RDS PostgreSQL
              │
              ▼
       Private Database
              │
              ▼
       PostgreSQL Queries
              │
              ▼
        Training Dataset
```

---

## 👨‍💻 Author

**Heinn Htet Zan**

Computer Science Student | Software Developer | Aspiring Data / ML Engineer

GitHub: [@Koheinn](https://github.com/Koheinn)

---

## ⭐ Acknowledgements

This project was completed as part of the **DeepLearning.AI Data Engineering Professional Certificate on Coursera** and was created for educational and portfolio purposes.
