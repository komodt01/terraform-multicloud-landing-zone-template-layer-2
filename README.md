# Multicloud Landing Zone – Layer 2

## Overview

This repository provides a **Layer-2 multicloud landing-zone reference template** implemented with Terraform across:

* AWS
* Azure
* Google Cloud
* Oracle Cloud Infrastructure (OCI)

The focus is the **cloud-environment foundation inside an existing account, subscription, project, or compartment**.

The repository demonstrates foundational patterns for:

* Network segmentation
* Public and private subnet structure
* Network telemetry
* Basic IAM/RBAC assignments
* Secure infrastructure defaults
* Repeatable infrastructure definition

The goal is to establish a workload-ready foundation while keeping organizational governance and workload-specific architecture in separate layers.

> **Standardize the architectural intent across clouds while using each provider's native capabilities.**

---

## Layered Architecture

The repository is intentionally positioned as **Layer 2**.

```text
Layer 1
Organizational Governance
        |
        v
Layer 2
Cloud Environment Foundation
        |
        v
Layer 3
Workloads / Platforms / Applications
```

### Layer 1 — Organizational Governance

Layer 1 establishes controls above individual cloud environments.

Examples include:

* AWS Organizations and SCPs
* Azure Management Groups and Azure Policy
* GCP Organization Policy
* OCI tenancy and compartment governance

Those controls are represented separately in the companion **Layer-1 Organizational Guardrails** architecture.

### Layer 2 — Cloud Environment Foundation

This repository establishes foundational resources within a cloud environment:

* Virtual networking
* Subnet segmentation
* Routing
* Network telemetry
* Example IAM/RBAC assignments
* Resource naming and configuration through variables

### Layer 3 — Workloads

Layer 3 represents the systems deployed on top of the foundation:

* Applications
* Kubernetes
* Databases
* APIs
* CI/CD pipelines
* Workload-specific security controls

Those capabilities are outside the scope of this repository.

---

# What the Terraform Actually Demonstrates

The four implementations intentionally use different provider-native resources while maintaining a common architectural pattern.

| Capability             | AWS                             | Azure                                   | GCP                                      | OCI                                         |
| ---------------------- | ------------------------------- | --------------------------------------- | ---------------------------------------- | ------------------------------------------- |
| Virtual network        | VPC                             | VNet                                    | VPC                                      | VCN                                         |
| Public subnet          | Yes                             | Yes                                     | Network/subnet foundation                | Yes                                         |
| Private subnet         | Yes                             | Yes                                     | Not modeled as a separate private subnet | Yes                                         |
| Network telemetry      | VPC Flow Logs → CloudWatch Logs | VNet diagnostic setting → Log Analytics | VPC Flow Logs → Cloud Logging            | Log Group foundation                        |
| Example access control | IAM role                        | Reader role assignment                  | Viewer IAM binding                       | No IAM assignment in current template       |
| Credential pattern     | Role assumption example         | Principal object ID                     | User email example                       | Placeholder tenancy/compartment identifiers |

The implementations are therefore **architecturally comparable but not technically identical**.

---

# AWS

The AWS implementation creates:

* VPC
* Public subnet
* Private subnet
* Internet Gateway
* Public route table and association
* CloudWatch Log Group
* VPC Flow Logs
* IAM role used by VPC Flow Logs
* Example read-only IAM role

The public subnet is configured to map public IP addresses on launch.

The private subnet is created without a public-IP-on-launch setting and does not have the public route-table association used by the public subnet.

VPC Flow Logs capture all traffic and send the records to CloudWatch Logs.

The example IAM role demonstrates a scoped access pattern using AWS's managed `ReadOnlyAccess` policy.

### Important Boundary

This template does not establish AWS Organizations governance, SCP enforcement, enterprise identity federation, or a complete multi-account landing-zone hierarchy.

Those capabilities belong above this Layer-2 foundation.

---

# Azure

The Azure implementation creates:

* Resource Group
* Virtual Network
* Public subnet
* Private subnet
* Log Analytics workspace
* VNet diagnostic setting
* Example Reader role assignment

The VNet provides the address-space and subnet foundation for workloads.

The diagnostic setting connects the VNet to Log Analytics and enables the metric category represented in the template.

The Reader assignment demonstrates a basic read-only RBAC pattern at resource-group scope.

### Important Boundary

The current template does **not** implement:

* Azure Management Groups
* Azure Policy
* NSGs
* Private Endpoints
* Enterprise identity federation
* Full centralized logging architecture

Those can be added as additional architecture layers when required.

---

# Google Cloud

The GCP implementation creates:

* Custom VPC
* Regional subnet
* VPC Flow Logs
* Example ingress firewall rule
* Project-level Viewer IAM binding

The VPC disables automatic subnet creation so the network structure is explicitly defined.

The subnet enables VPC Flow Logs with configured aggregation and sampling.

The firewall rule permits TCP/22 from the configured CIDR range.

The IAM example grants the Viewer role to a specified user.

### Important Boundary

The repository does not attempt to implement a complete GCP organization hierarchy, Organization Policy model, enterprise identity architecture, or production firewall architecture.

Those belong to broader governance and workload designs.

---

# OCI

The OCI implementation creates:

* VCN
* Internet Gateway
* Public route table
* Public subnet
* Private subnet
* OCI Logging log group

The public subnet permits public IP assignment and uses the Internet Gateway route.

The private subnet explicitly prohibits public IP assignment.

The OCI log group establishes a logging destination foundation, but the current template does **not** configure a VCN flow-log source or log object that sends network telemetry into that group.

The `tenant_ocid` variable is retained as an extension point but is not currently used by the Terraform resources.

### Important Boundary

The current OCI implementation does not create:

* Dynamic groups
* OCI IAM policies
* Security Zones
* Complete tenancy governance
* VCN flow-log configuration

Those capabilities can be incorporated when the landing-zone requirements expand.

---

# Security Model

The landing zone establishes several foundational security properties.

## Network Separation

Public and private subnet patterns provide an initial separation between resources that may require internet connectivity and resources intended to remain private.

This is a **foundation**, not a complete network-security architecture.

Production environments may require:

* Security groups
* Network security groups
* Network ACLs
* Firewall inspection
* WAF
* Egress controls
* Private service connectivity
* Centralized network inspection

## Network Visibility

Network telemetry is treated as part of the foundation where implemented.

The current implementations demonstrate:

* AWS VPC Flow Logs
* GCP VPC Flow Logs
* Azure diagnostic configuration
* OCI logging destination infrastructure

The degree of telemetry differs by provider and by what the current Terraform actually configures.

## Access Control

The examples use native cloud IAM/RBAC mechanisms rather than embedding credentials in Terraform.

They are deliberately simple examples rather than a complete enterprise identity architecture.

Production implementations should integrate:

* Enterprise identity providers
* Federation
* MFA
* Privileged access management
* Access reviews
* Lifecycle management
* Workload identity

---

# Architecture Philosophy

The design is based on four principles.

### 1. Security Foundation Before Workloads

Network boundaries, visibility, and basic access controls should exist before application workloads are introduced.

### 2. Native Cloud Capabilities

Each cloud should use its native networking, logging, and identity mechanisms rather than creating artificial abstractions simply for implementation symmetry.

### 3. Explicit Layer Ownership

Organizational governance, cloud foundations, and workloads should have clearly defined ownership.

### 4. Repeatability

Terraform provides a version-controlled and repeatable representation of the foundation.

Terraform is the implementation mechanism; the architecture and security requirements come first.

See `Architecture_Philosophy.md` for the design principles behind the template.

---

# Security Considerations

This repository is a **reference architecture**, not a complete production landing zone.

Important considerations include:

* Placeholder identities must be replaced.
* CIDR ranges must be adapted to the enterprise network.
* Logging retention must reflect operational and regulatory requirements.
* Network access rules require workload-specific review.
* Public subnet placement does not by itself make a workload secure.
* Private subnet placement does not eliminate the need for additional network controls.
* IAM/RBAC examples should be integrated with enterprise identity governance.
* Additional encryption, secrets management, monitoring, and incident-response controls are required for production.

See `Security_Considerations.md` for the detailed security discussion.

---

# Compliance Context

The repository includes an illustrative compliance mapping to help connect the architecture to common security-control objectives.

The mapping references:

* NIST SP 800-53
* ISO/IEC 27001
* CIS Controls and cloud benchmarks

These mappings describe how the architecture may **support** broader control objectives.

They are not evidence of compliance, certification, or audit completion.

See `Compliance_Mapping.md`.

---

# Using the Templates

The Terraform examples are intended to be adapted rather than deployed unchanged.

For a selected provider:

```bash
cd AWS/Terraform
```

or the corresponding Azure, GCP, or OCI directory.

Review the provider configuration and variables before initialization.

Typical Terraform workflow:

```bash
terraform init
terraform plan
terraform apply
```

Use the supplied `terraform.tfvars.example` as a starting point and replace all placeholder values.

A production implementation should also use appropriate:

* Remote state
* State locking
* CI/CD controls
* Credential management
* Change approval
* Environment separation
* Policy validation

---

# Repository Structure

```text
terraform-multicloud-landing-zone-template-layer-2/
│
├── AWS/
│   └── Terraform
│       ├── provider.tf
│       ├── main.tf
│       ├── variables.tf
│       ├── outputs.tf
│       └── terraform.tfvars.example
│
├── Azure/
│   └── Terraform
│       ├── provider.tf
│       ├── main.tf
│       ├── variables.tf
│       ├── outputs.tf
│       └── terraform.tfvars.example
│
├── GCP/
│   └── Terraform
│       ├── provider.tf
│       ├── main.tf
│       ├── variables.tf
│       ├── outputs.tf
│       └── terraform.tfvars.example
│
├── OCI/
│   └── Terraform
│       ├── provider.tf
│       ├── main.tf
│       ├── variables.tf
│       ├── outputs.tf
│       └── terraform.tfvars.example
│
├── Architecture_Philosophy.md
├── Security_Considerations.md
├── Compliance_Mapping.md
├── Technical Case Study — Multicloud Landing Zone Layer 2.md
└── README.md
```

---

# Related Architecture

This Layer-2 foundation is intended to work with higher- and lower-level security architecture.

### Layer 1

Organizational guardrails establish enterprise-level preventive governance above individual cloud environments.

### Layer 2

This repository establishes the foundational cloud environment.

### Layer 3

Workload and platform architectures build on the foundation.

Examples include secure Kubernetes, application security, CI/CD security, data protection, and workload-specific Zero Trust architectures.

---

# Limitations

This repository does not attempt to provide:

* A complete enterprise landing-zone product
* A full multi-account or multi-subscription hierarchy
* Enterprise SSO or IAM lifecycle management
* Complete network inspection
* Complete SIEM architecture
* Complete secrets management
* Complete encryption architecture
* Workload deployment
* Formal compliance evidence
* Production operational processes

Those capabilities require additional architecture, governance, and operational controls.

---

# Key Takeaway

A landing zone is more than a collection of Terraform resources.

The architecture establishes a **repeatable security foundation** that workloads can build upon.

The important design decision is to maintain consistent security intent across cloud providers while allowing each provider to implement that intent using its native capabilities.

Layer 1 governs the organization.

Layer 2 establishes the cloud environment.

Layer 3 secures the workloads.

Keeping those responsibilities distinct makes the overall cloud-security architecture easier to govern, validate, and evolve.
