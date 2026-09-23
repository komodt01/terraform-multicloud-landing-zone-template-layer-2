# Architecture Philosophy

This Landing Zone is designed as an **architecture reference implemented through Terraform**.

The purpose is to establish a repeatable cloud-environment foundation across AWS, Azure, Google Cloud, and OCI while recognizing that the providers do not expose identical networking, identity, or logging capabilities.

The architecture therefore standardizes the **intent and responsibilities** of the landing zone rather than forcing identical technical implementations.

---

## 1. Establish the Foundation Before the Workload

A workload should not be the first thing deployed into a cloud environment.

The landing zone establishes foundational capabilities first:

* Network address space
* Subnet structure
* Routing
* Initial network segmentation
* Network or platform telemetry where implemented
* Basic access-control primitives
* Consistent resource naming and configuration

The foundation provides a defined environment on which workload-specific architecture can be built.

It is not intended to solve every security requirement before workloads exist.

---

## 2. Standardize the Pattern, Not the Provider

AWS, Azure, GCP, and OCI expose different services and authorization models.

The architecture therefore uses a common conceptual model:

| Architectural Concern | AWS                        | Azure               | GCP                    | OCI                        |
| --------------------- | -------------------------- | ------------------- | ---------------------- | -------------------------- |
| Virtual network       | VPC                        | VNet                | VPC                    | VCN                        |
| Subnet boundary       | Subnets                    | Subnets             | Subnet                 | Subnets                    |
| Network telemetry     | VPC Flow Logs              | Diagnostic Settings | VPC Flow Logs          | Logging foundation         |
| Access control        | IAM role                   | RBAC assignment     | IAM binding            | Provider IAM model         |
| Internet boundary     | Internet Gateway / routing | VNet foundation     | Firewall/routing model | Internet Gateway / routing |

The implementation details remain provider-specific.

This is intentional.

A multicloud architecture becomes harder to govern when technical uniformity is treated as the objective instead of consistent security and operational outcomes.

---

## 3. Layer Ownership

The landing zone is explicitly positioned between organizational governance and workloads.

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
Workloads and Platforms
```

### Layer 1

Examples include:

* Organizational policies
* AWS Organizations and SCPs
* Azure Management Groups and Azure Policy
* GCP Organization Policy
* OCI tenancy governance

These controls establish requirements above the individual environment.

### Layer 2

This repository establishes:

* Networking
* Subnet structure
* Routing
* Initial telemetry
* Basic IAM/RBAC examples

### Layer 3

Examples include:

* Applications
* Kubernetes
* Databases
* APIs
* CI/CD platforms
* Workload-specific security controls

Keeping ownership separated prevents the landing-zone template from being represented as the complete cloud-security architecture.

---

## 4. Network Segmentation as a Foundation

The landing zone establishes an initial distinction between public and private network areas where the provider implementation supports that pattern.

The purpose is to create an architectural boundary before workload resources are introduced.

The distinction should not be interpreted as complete network security.

Production environments may require additional controls such as:

* Security groups
* Network security groups
* Network ACLs
* Firewall inspection
* WAF
* Egress filtering
* Private service connectivity
* Centralized network inspection
* DNS controls

The landing zone establishes the starting topology; workload and enterprise network architecture determine the final control model.

---

## 5. Visibility Is Part of the Foundation

Telemetry should be considered during landing-zone design rather than after workloads are deployed.

The current Terraform demonstrates different levels of network or logging capability by provider:

* AWS creates VPC Flow Logs and sends them to CloudWatch Logs.
* Azure creates a Log Analytics workspace and a VNet diagnostic setting.
* GCP enables VPC Flow Logs on the subnet.
* OCI creates an OCI Logging log group as a logging foundation.

These implementations are intentionally not described as equivalent.

For example, the current OCI implementation does not configure a VCN flow-log source.

Similarly, the Azure diagnostic configuration should not be interpreted as a complete enterprise logging architecture.

Production environments would normally extend this foundation with:

* Centralized log routing
* Retention policies
* SIEM integration
* Alerting
* Access controls
* Log integrity protections
* Regulatory retention requirements

---

## 6. Identity Without Embedded Credentials

The architecture avoids placing long-lived credentials directly into the Terraform configuration.

The current examples demonstrate provider-native access-control patterns where implemented:

* AWS IAM role
* Azure Reader role assignment
* GCP Viewer IAM binding

OCI currently focuses on the network and logging foundation and does not create an IAM assignment in this template.

The broader architectural direction is to use provider-native identity mechanisms rather than embedding credentials in workloads or infrastructure code.

Production implementations would extend this with:

* Enterprise federation
* MFA
* Privileged access management
* Access lifecycle management
* Access reviews
* Workload identity
* Separation of duties

Those capabilities are outside the current Layer-2 implementation.

---

## 7. Secure Defaults Require Context

A landing zone should establish reasonable defaults, but "secure by default" does not mean that every restrictive control belongs in Layer 2.

For example:

* A private subnet is useful as a foundation.
* Public exposure may still be required for a specific workload.
* A read-only example role demonstrates least-privilege intent but does not establish enterprise IAM governance.
* Network telemetry improves visibility but does not create a complete detection capability.

The architectural decision is therefore to establish useful security primitives without pretending that the foundation eliminates the need for workload-specific risk analysis.

---

## 8. Terraform as an Architectural Expression

Terraform is used to express the architecture in a repeatable and reviewable form.

The important design decisions precede the Terraform resources:

1. Define the required cloud-environment capabilities.
2. Identify the appropriate provider-native mechanism.
3. Establish the intended security boundary.
4. Express the design through variables and resources.
5. Validate the resulting infrastructure.
6. Extend the foundation for production requirements.

Terraform therefore acts as the implementation mechanism for the architecture rather than becoming the architecture itself.

---

## 9. Explicit Dependencies

Some Layer-2 decisions create dependencies on higher or lower layers.

For example, stronger private-network designs may require:

* DNS
* Routing
* Private endpoints
* Shared network services
* Administrative access paths

Likewise, identity examples depend on enterprise identity architecture for production use.

The landing zone should therefore be treated as part of a larger architecture rather than an isolated Terraform deployment.

A control that is technically secure but operationally disconnected from its dependencies can still create availability or administrative problems.

---

## 10. Extensibility

The landing zone is intentionally designed as a starting point.

A production architecture could extend it with:

* Multi-account or multi-subscription structures
* Shared networking
* Private connectivity
* Centralized DNS
* Enterprise identity integration
* Privileged-access architecture
* Centralized logging
* SIEM integration
* Key management
* Secrets management
* Policy-as-code
* Kubernetes foundations
* Workload security controls
* Cost and resource governance

These extensions should be introduced according to organizational requirements rather than assuming every environment needs the same implementation.

---

## Summary

The architecture philosophy can be summarized as:

> **Establish a secure, observable, repeatable cloud foundation while preserving clear ownership between organizational governance, environment infrastructure, and workloads.**

The design standardizes architectural intent across AWS, Azure, GCP, and OCI while allowing each provider to use its native capabilities.

The result is not intended to be a complete production landing-zone product.

It is a reference architecture showing how foundational cloud security and infrastructure concerns can be organized into a coherent Layer-2 model.
