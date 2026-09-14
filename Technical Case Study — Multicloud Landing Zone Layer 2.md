# Technical Case Study — Multicloud Landing Zone Layer 2

## Scenario

Organizations operating across multiple cloud providers often need a consistent security baseline without forcing every cloud into an identical implementation model.

For this project, I designed a Layer-2 landing zone reference architecture spanning AWS, Azure, Google Cloud, and Oracle Cloud Infrastructure (OCI). The focus was the foundation inside an individual cloud account, subscription, project, or compartment.

I deliberately separated this layer from organization-wide governance. Controls such as AWS Organizations and SCPs, Azure Management Groups and Azure Policy, and GCP Organization Policies belong at a higher organizational layer and were outside the scope of this project.

The goal was to answer a narrower architecture question:

**What security capabilities should exist inside a cloud environment before application workloads are introduced?**

## Architecture Approach

I used four common control areas as the baseline across the cloud providers:

- Network segmentation
- Logging and telemetry
- Identity and access controls
- Infrastructure standardization

The objective was consistency of **security intent**, not identical technical implementations.

For example, networking is expressed through a VPC in AWS and GCP, a VNet in Azure, and a VCN in OCI. The services differ, but the architectural requirement remains the same: establish controlled network boundaries and separate public and private resources before workloads are deployed.

The same principle applies to logging and identity. Each cloud uses its native capabilities rather than introducing an abstraction simply to make the implementations look identical.

## Why I Separated Layer 1 and Layer 2

One of the most important decisions was defining the boundary between organizational governance and the workload-ready cloud foundation.

I treated the layers as:

**Layer 1 — Organizational governance**

Enterprise-wide policies, account structures, management groups, organization policies, SCPs, centralized identity requirements, and other controls applied above individual environments.

**Layer 2 — Cloud environment baseline**

Networking, logging, monitoring, IAM/RBAC primitives, and security controls established inside an account, subscription, project, or compartment.

**Layer 3 — Workloads and platforms**

Applications, Kubernetes platforms, CI/CD pipelines, databases, APIs, and other workload-specific infrastructure.

Keeping those responsibilities separate prevents the landing-zone template from implying that account-level Terraform can replace enterprise governance.

It also makes the architecture easier to extend. Layer 2 can provide a repeatable foundation while Layer 1 and Layer 3 evolve independently.

## Cross-Cloud Design Decision

A major design consideration was deciding what should be standardized across clouds and what should remain cloud-native.

I chose to standardize the **control objectives** rather than the services themselves.

The pattern is:

**Common requirement → cloud-native implementation**

For networking, each environment receives an appropriate virtual network and subnet structure.

For telemetry, the design enables the cloud provider's native logging capabilities.

For identity, the design favors scoped IAM/RBAC assignments and workload identity mechanisms rather than embedding long-lived credentials.

This avoids a common multicloud problem: creating artificial technical uniformity that hides meaningful differences between cloud providers.

In a production environment, I would expect the enterprise architecture and security standards to define the required control outcomes while individual cloud implementations satisfy those requirements using the appropriate native services.

## Identity and Credential Strategy

Another design principle was avoiding hardcoded credentials.

The baseline favors native workload identity mechanisms and scoped permissions. This reduces reliance on long-lived secrets and supports least-privilege access patterns.

A production implementation would extend this with enterprise identity federation, privileged-access controls, lifecycle management, MFA requirements, access reviews, and centralized identity governance.

Those capabilities were intentionally not represented as being solved by this Layer-2 template.

## Logging and Visibility

I treated telemetry as part of the foundation rather than something added after applications are deployed.

The reference implementations use native logging capabilities such as AWS VPC Flow Logs, Azure diagnostic capabilities and Log Analytics, GCP VPC Flow Logs and Cloud Logging, and OCI Logging.

The purpose is to ensure that the environment begins producing security-relevant telemetry as part of its baseline.

The project does not attempt to design an enterprise SIEM architecture. In a production environment, I would evaluate centralized log routing, retention requirements, security analytics, alerting, access controls, regulatory requirements, and cost before determining the final logging architecture.

## Security Boundary

This project intentionally does not represent a complete production landing zone.

The Layer-2 design establishes foundational controls, but several capabilities would normally exist elsewhere in an enterprise architecture, including:

- Organization-wide preventive policies
- Enterprise identity federation and lifecycle governance
- Centralized SIEM and security operations
- Policy-as-code enforcement
- Enterprise key-management strategy
- Formal change and exception processes
- Budget and FinOps controls
- Workload-specific security controls
- Independent compliance validation

Making those limitations explicit was important because a Terraform deployment alone should not be represented as establishing an organization's complete cloud-security posture.

## Compliance Considerations

I mapped elements of the architecture to security frameworks such as NIST SP 800-53, ISO 27001, and CIS guidance to show how technical controls can support broader security requirements.

I treated these mappings as architectural traceability rather than evidence of compliance.

A production compliance determination would require additional organizational processes, operating evidence, control testing, documentation, and independent validation beyond the infrastructure represented in this repository.

## Terraform's Role

Terraform is the implementation mechanism for the reference architecture, not the primary purpose of the project.

I used it because infrastructure as code makes the cloud baselines repeatable, reviewable, and easier to compare across providers.

The architectural decisions come first: security boundaries, control objectives, identity approach, network segmentation, telemetry requirements, and layer ownership. Terraform expresses those decisions as deployable infrastructure.

## Production Evolution

If I were extending this reference architecture into an enterprise implementation, I would first establish the organization's Layer-1 governance model and determine how environments are provisioned and governed at scale.

From there, I would evaluate:

- Enterprise identity and privileged-access integration
- Centralized logging and SIEM architecture
- Policy-as-code and configuration enforcement
- Encryption and key-management requirements
- Private connectivity and egress controls
- DNS and shared-network services
- Security monitoring and incident-response integration
- Cost and resource-governance controls
- Environment-specific requirements
- CI/CD controls for landing-zone changes

The exact implementation would depend on the organization's regulatory requirements, operating model, cloud strategy, threat model, and existing security services.

## Key Takeaway

The main lesson from this project was that multicloud standardization does not require making every cloud look technically identical.

The more useful architecture pattern is to establish **consistent security outcomes and clear control ownership**, then implement those requirements using the capabilities appropriate to each cloud.

By separating organizational governance, cloud-environment foundations, and workload controls, the architecture creates clearer boundaries and provides a foundation that can evolve without treating a Terraform template as the entire cloud-security program.