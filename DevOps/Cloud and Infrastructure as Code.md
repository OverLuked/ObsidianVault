## Cloud & Infrastructure as Code
*On-demand computing resources, managed and provisioned through code*

### Cloud Service Models

| Model                              | Provides                              | Example                        |
| -------------------------------------- | ---------------------------------------- | --------------------------------- |
| IaaS (Infrastructure as a Service)      | Raw compute, storage, networking          | AWS EC2, Azure VMs                |
| PaaS (Platform as a Service)            | Managed runtime - no server management    | Heroku, AWS Elastic Beanstalk     |
| SaaS (Software as a Service)            | Fully managed application                 | Gmail, Salesforce                 |

### Major Cloud Providers
- AWS (Amazon Web Services) - largest market share, broadest service catalog
- Azure - strong enterprise/Microsoft ecosystem integration
- GCP (Google Cloud Platform) - strong in data/ML tooling, birthplace of Kubernetes

### Deployment Models
- Public Cloud -> shared infrastructure, managed by the provider
- Private Cloud -> dedicated infrastructure, on-prem or hosted
- Hybrid Cloud -> combination of public and private
- Multi-Cloud -> using more than one public cloud provider

### Infrastructure as Code (IaC)
*Defining and provisioning infrastructure through machine-readable configuration files*

##### Benefits
- Version-controlled, reviewable infrastructure changes
- Repeatable and consistent environments
- Faster disaster recovery - rebuild from code

##### Common Tools
- Terraform - declarative, multi-cloud, `.tf` files, maintains state
- AWS CloudFormation - declarative, AWS-native
- Pulumi - IaC using general-purpose programming languages

##### Sample Terraform
```hcl
resource "aws_instance" "web" {
  ami           = "ami-0123456789"
  instance_type = "t3.micro"

  tags = {
    Name = "web-server"
  }
}
```

### Configuration Management
*Keeps existing servers in a consistent, defined state - contrast with IaC, which provisions them*

| Tool     | Approach                              |
| --------- | ---------------------------------------- |
| Ansible    | Agentless, YAML playbooks, push-based     |
| Chef       | Ruby DSL, agent-based, pull-based         |
| Puppet     | Declarative DSL, agent-based, pull-based  |

### Common Interview Questions
- What's the difference between IaaS, PaaS, and SaaS?
- What's the difference between Infrastructure as Code and Configuration Management?
- Why use Terraform over manually clicking through a cloud console?
- What is Terraform state and why does it matter?
