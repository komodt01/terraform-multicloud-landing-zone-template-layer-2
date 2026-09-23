# Technical Case Study — Multicloud Landing Zone Layer 2

## Scenario

Organizations operating across multiple cloud providers need a repeatable cloud foundation without assuming that AWS, Azure, Google Cloud, and OCI implement security in the same way.

For this project, I developed a **Layer-2 multicloud landing-zone reference architecture** using Terraform across those four providers.

The focus is the foundation inside an existing cloud environment:

* AWS account
* Azure subscription
* GCP project
* OCI compartment

The architecture deliberately separates this layer from organizational governance and workload-specific security.

The central question was:

> **What foundational capabilities should exist inside a cloud environment before application workloads are introduced?**

---

## Architecture Approach

I organized the Layer-2 foundation around four architectural concerns:

* Network structure
* Network or platform visibility
* Basic access-control primitives
* Repeatable infrastructure configuration

The objective was to establish consistent **architectural intent**, not identical cloud implementations.

For example:

* AWS uses a VPC.
* Azure uses a VNet.
* GCP uses a custom VPC.
* OCI uses a VCN.

The same principle applies to telemetry and access control.

Each provider uses its native mechanisms rather than forcing a common abstraction over fundamentally different cloud architectures.

---

## Layer Separation

One of the most important design decisions was separating the cloud foundation from the controls above and below it.

### Layer 1 — Organizational Governance

Layer 1 establishes enterprise-wide requirements and governance.

Examples include:

* AWS Organizations and SCPs
* Azure Management Groups and Azure Policy
* GCP Organization Policy
* OCI tenancy and compartment governance
* Enterprise identity requirements
* Centralized security standards

These controls establish requirements above individual cloud environments.

### Layer 2 — Cloud Environment Foundation

This repository establishes foundational infrastructure inside the governed environment.

Examples include:

* Virtual networks
* Subnets
* Routing
* Network telemetry where implemented
* Basic IAM/RBAC examples
* Terraform-based configuration

### Layer 3 — Workloads and Platforms

Layer 3 contains workload-specific architecture such as:

* Applications
* Kubernetes
* Databases
* APIs
* CI/CD platforms
* Workload identity
* Application security
* Data protection

Keeping these layers distinct prevents a landing-zone template from being represented as the organization's complete cloud-security architecture.

---

## Cross-Cloud Design Decision

The primary design decision was determining what should be standardized and what should remain provider-specific.

I chose to standardize the **architectural requirement**, not the technical implementation.

The pattern is:

```text id="9m5f3n"
Enterprise Requirement
        |
        v
Layer-2 Capability
        |
        v
Cloud-Native Implementation
```

For example, the requirement may be to establish a defined network boundary.

The implementation becomes:

* AWS VPC and subnets
* Azure VNet and subnets
* GCP VPC and subnet
* OCI VCN and subnets

This approach avoids artificial technical symmetry.

A multicloud architecture should make provider differences explicit rather than hiding them behind an abstraction that does not accurately represent the underlying platforms.

---

# AWS Foundation

The AWS implementation creates:

* VPC
* Public subnet
* Private subnet
* Internet Gateway
* Public route table
* VPC Flow Logs
* CloudWatch Log Group
* VPC Flow Logs IAM role
* Example read-only IAM role

The public subnet is configured to map public IP addresses on launch.

The private subnet is not associated with the public route table.

VPC Flow Logs provide network telemetry through CloudWatch Logs.

The IAM example demonstrates a role-based read-only access pattern.

### Architectural Boundary

This implementation does not create:

* AWS Organizations
* SCP attachments
* Multi-account hierarchy
* Enterprise federation
* Complete network inspection

Those capabilities belong to higher-level architecture.

---

# Azure Foundation

The Azure implementation creates:

* Resource Group
* Virtual Network
* Public subnet
* Private subnet
* Log Analytics workspace
* VNet diagnostic setting
* Reader role assignment

The VNet establishes the network and subnet foundation.

The diagnostic configuration connects the VNet to Log Analytics and represents the metric configuration included in the Terraform.

It should not be interpreted as a complete Azure network-logging architecture.

The Reader assignment demonstrates resource-group-scoped Azure RBAC.

### Architectural Boundary

The current implementation does not create:

* NSGs
* Azure Policy
* Management Groups
* Private Endpoints
* Enterprise identity federation
* Network firewalls

These are potential extensions rather than implemented capabilities.

---

# GCP Foundation

The GCP implementation creates:

* Custom VPC
* Regional subnet
* VPC Flow Logs
* Example ingress firewall rule
* Project-level Viewer IAM binding

Automatic subnet creation is disabled so that the network structure is explicitly defined.

VPC Flow Logs are enabled on the subnet.

The firewall example permits TCP/22 from the configured CIDR.

That rule is intentionally an example rather than a claim of production-ready SSH exposure.

The Viewer binding demonstrates project-level IAM scoping.

### Architectural Boundary

The implementation does not establish:

* GCP organization hierarchy
* Organization Policy
* Enterprise identity federation
* Complete firewall architecture
* Workload identity architecture

Those capabilities belong to broader platform and governance designs.

---

# OCI Foundation

The OCI implementation creates:

* VCN
* Internet Gateway
* Public route table
* Public subnet
* Private subnet
* OCI Logging log group

The public subnet permits public IP assignment and uses the Internet Gateway route.

The private subnet explicitly prohibits public IP assignment.

The Logging log group provides a logging-destination foundation.

The current Terraform does **not** configure a VCN flow-log source that sends network telemetry into that log group.

The `tenant_ocid` variable is retained as an extension point but is not currently used by the resources.

### Architectural Boundary

The implementation does not create:

* Dynamic groups
* IAM policies
* Security Zones
* Tenancy-wide governance
* VCN flow-log configuration

Those are potential extensions rather than current capabilities.

---

## Identity Architecture

Identity was deliberately kept at the **basic IAM/RBAC primitive level**.

The current implementations demonstrate:

* AWS IAM role
* Azure Reader assignment
* GCP Viewer binding

OCI does not currently create an IAM assignment.

The architecture avoids embedding cloud credentials directly into the Terraform configuration.

This should not be confused with implementing enterprise identity architecture.

A production landing zone would normally integrate with:

* Enterprise identity providers
* Federation
* MFA
* Privileged access management
* Access lifecycle management
* Access reviews
* Workload identity
* Break-glass procedures

Those controls belong to the broader identity architecture rather than this template.

---

## Network Architecture

Network segmentation is treated as a foundational capability.

The implementations establish public/private distinctions where modeled, but the subnet structure is only the beginning of the network-security architecture.

Production environments may require:

* Security groups
* NSGs
* Network ACLs
* Firewall inspection
* WAF
* Egress controls
* Private connectivity
* DNS architecture
* Centralized network services
* Network monitoring

A landing zone should therefore establish the foundation while leaving workload-specific communication requirements to later architecture layers.

---

## Visibility Architecture

Visibility was considered during the foundation design rather than as a post-deployment addition.

The current implementations provide different levels of telemetry:

| Provider | Current Capability                      |
| -------- | --------------------------------------- |
| AWS      | VPC Flow Logs → CloudWatch Logs         |
| Azure    | VNet diagnostic setting → Log Analytics |
| GCP      | VPC Flow Logs → Cloud Logging           |
| OCI      | OCI Logging log group                   |

These should not be interpreted as equivalent implementations.

The next architectural step in a production environment would be determining:

* Which events are required
* Where logs are centralized
* Retention requirements
* Access controls
* SIEM integration
* Detection requirements
* Regulatory requirements
* Cost constraints

The landing zone provides the foundation; the enterprise monitoring architecture determines the complete visibility model.

---

## Security Boundary

A key design decision was explicitly defining what this repository does **not** attempt to solve.

The Layer-2 foundation does not provide:

* Complete organizational governance
* Enterprise identity
* Full privileged-access architecture
* Complete network inspection
* Complete SIEM architecture
* Secrets management
* Enterprise key management
* Application security
* CI/CD security
* Workload-specific Zero Trust architecture
* Formal compliance validation

These limitations are intentional.

A landing zone becomes easier to govern when each architectural layer has a clearly defined responsibility.

---

## Compliance Approach

I included illustrative mappings to security frameworks such as:

* NIST SP 800-53
* ISO/IEC 27001
* CIS Controls and cloud benchmarks

The purpose is **architectural traceability**.

The mapping helps identify how foundational capabilities may contribute to broader control objectives.

It does not establish compliance.

A production compliance determination would require:

* Control ownership
* Policies and procedures
* Risk assessment
* Operating evidence
* Control testing
* Monitoring
* Remediation
* Independent validation where required

---

## Terraform's Role

Terraform is the implementation mechanism rather than the architectural objective.

The architectural sequence is:

```text id="v5gjbr"
Security / Business Requirement
            |
            v
Architecture Decision
            |
            v
Provider-Native Capability
            |
            v
Terraform Implementation
            |
            v
Validation
```

This keeps the infrastructure code subordinate to the architecture.

Terraform provides repeatability and reviewability, but the presence of Terraform code does not by itself establish that a security requirement has been satisfied.

---

## Production Evolution

If extending this reference architecture into an enterprise landing zone, I would address the next capabilities based on organizational requirements.

### Governance

* Multi-account or subscription hierarchy
* Organization policies
* Policy-as-code
* Exception management
* Change governance

### Identity

* Enterprise federation
* Privileged access
* MFA
* Workload identity
* Access lifecycle

### Network

* Shared networking
* Private connectivity
* DNS
* Egress controls
* Network inspection

### Visibility

* Centralized logging
* SIEM integration
* Detection engineering
* Alerting
* Evidence retention

### Platform Security

* Secrets management
* Key management
* Kubernetes security
* CI/CD security
* Workload protection

The sequence should be driven by the organization's risk profile, operating model, regulatory requirements, and existing security capabilities.

---

## Key Architectural Lesson

The main lesson from this project is that multicloud standardization does not require identical infrastructure.

The stronger pattern is:

> **Define the common security and architectural outcome, establish clear control ownership, and use the provider-native mechanism that best implements that requirement.**

Layer 1 establishes organizational governance.

Layer 2 establishes the cloud environment foundation.

Layer 3 secures the workloads and platforms.

That separation creates a landing-zone architecture that can evolve without turning a Terraform template into a claim of complete cloud-security coverage.
::
