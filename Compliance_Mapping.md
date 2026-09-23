# Compliance Mapping — Illustrative

This document provides an **illustrative mapping** between the Layer-2 Multicloud Landing Zone patterns and common security-control objectives.

It is not a compliance attestation, certification, audit result, or statement that the Terraform configuration satisfies any framework requirement by itself.

The mapping is intended to show how the architecture may contribute to broader security and governance objectives when combined with organizational processes and additional controls.

Frameworks referenced include:

* NIST SP 800-53 Rev. 5
* ISO/IEC 27001 Annex A
* CIS Controls and relevant cloud-security benchmarks

---

## 1. Identity and Access Management

### Patterns Represented

The repository demonstrates basic provider-native access-control patterns:

* AWS IAM role with read-only access
* Azure Reader role assignment
* GCP Viewer IAM binding
* Avoidance of hardcoded cloud credentials in the Terraform configuration

The OCI implementation does not currently create an IAM assignment or policy.

### NIST SP 800-53 — Examples

* **AC-2:** Account management
* **AC-3:** Access enforcement
* **AC-6:** Least privilege
* **IA-2:** Identification and authentication
* **IA-5:** Authenticator management

### ISO/IEC 27001 — Examples

* **A.5.15:** Access control
* **A.5.16:** Identity management
* **A.8.2:** Privileged access rights
* **A.8.3:** Information access restriction

### CIS — Examples

* **CIS Control 5:** Account management
* **CIS Control 6:** Access control management
* Relevant cloud-provider IAM benchmarks

### Considerations

The repository demonstrates access-control primitives rather than complete identity governance.

Production implementations would additionally require:

* Enterprise identity federation
* Group-based authorization
* MFA
* Privileged access management
* Access lifecycle management
* Access reviews
* Separation of duties

---

## 2. Network Security and Segmentation

### Patterns Represented

The repository establishes foundational network structures:

* AWS VPC with public and private subnets
* Azure VNet with separate subnets
* GCP custom VPC with a regional subnet and firewall rule
* OCI VCN with public and private subnets

The examples establish initial network boundaries but do not constitute a complete network-security architecture.

### NIST SP 800-53 — Examples

* **SC-7:** Boundary protection
* **SC-32:** System partitioning
* **AC-4:** Information flow enforcement

### ISO/IEC 27001 — Examples

* **A.8.20:** Networks security
* **A.8.21:** Security of network services
* **A.8.22:** Segregation of networks

### CIS — Examples

* **CIS Control 4:** Secure configuration
* **CIS Control 12:** Network infrastructure management

### Considerations

Production environments may require additional controls such as:

* AWS Security Groups and NACLs
* Azure NSGs
* GCP firewall policies
* OCI security lists or NSGs
* Network firewalls
* WAF
* Egress controls
* Private connectivity
* Network inspection
* DNS security

The current Azure implementation does not create an NSG, and the GCP SSH firewall rule is an example configuration requiring workload-specific review.

---

## 3. Logging, Monitoring, and Auditability

### Patterns Represented

The implementations provide different levels of network or platform telemetry:

* **AWS:** VPC Flow Logs → CloudWatch Logs
* **Azure:** VNet diagnostic setting → Log Analytics, with the metric configuration represented in the template
* **GCP:** VPC Flow Logs enabled on the subnet
* **OCI:** OCI Logging log group created as a logging destination foundation

The provider implementations are therefore not technically equivalent.

### NIST SP 800-53 — Examples

* **AU-2:** Event logging
* **AU-6:** Audit record review, analysis, and reporting
* **AU-8:** Time stamps
* **SI-4:** System monitoring

### ISO/IEC 27001 — Examples

* **A.8.15:** Logging
* **A.8.16:** Monitoring activities

### CIS — Examples

* **CIS Control 8:** Audit log management
* **CIS Control 13:** Network monitoring and defense

### Considerations

The current repository does not implement a complete enterprise monitoring architecture.

Production environments should evaluate:

* Centralized log routing
* Retention
* Protected log storage
* SIEM integration
* Alerting
* Detection engineering
* Access controls
* Regulatory retention requirements

In particular, the OCI template creates the logging group but does not configure a VCN flow-log source that sends network telemetry into it.

---

## 4. Configuration Management and Infrastructure as Code

### Patterns Represented

The repository uses Terraform to represent infrastructure configuration.

The implementations use:

* Provider-specific Terraform resources
* Variables for environment-specific values
* Outputs for selected resource identifiers
* Example variable files
* Version-controlled infrastructure definitions

### NIST SP 800-53 — Examples

* **CM-2:** Baseline configuration
* **CM-3:** Configuration change control
* **CM-6:** Configuration settings

### ISO/IEC 27001 — Examples

* **A.8.9:** Configuration management
* **A.8.32:** Change management

### CIS — Examples

* **CIS Control 4:** Secure configuration of enterprise assets and software

### Considerations

Terraform alone does not establish formal change control.

Production implementations should add:

* Code review
* CI/CD validation
* Approval workflows
* State protection
* Drift detection
* Policy-as-code
* Change records
* Separation of deployment duties

---

## 5. Governance and Organizational Controls

This repository represents **Layer 2**.

Organizational governance is primarily a Layer-1 concern.

Relevant mechanisms include:

* AWS Organizations and SCPs
* Azure Management Groups and Azure Policy
* GCP Organization Policy
* OCI tenancy and compartment governance

### NIST SP 800-53 — Examples

* **PM-1:** Information security program plans
* **PM-9:** Risk management strategy
* **PL-2:** System and communications protection planning
* **CA-2:** Control assessments
* **CA-7:** Continuous monitoring

### ISO/IEC 27001 — Examples

* **A.5.1:** Policies for information security
* **A.5.2:** Information security roles and responsibilities
* **A.5.4:** Management responsibilities
* **A.6:** People controls

### Considerations

Layer-1 governance requires more than Terraform resources.

Organizations also need:

* Policy ownership
* Risk assessment
* Governance procedures
* Exception management
* Approval authorities
* Continuous oversight
* Evidence collection

The Layer-2 foundation should operate within those higher-level controls.

---

## 6. Data Protection

The current Layer-2 implementation does not provision application data stores.

The network and telemetry foundations can nevertheless support broader data-protection architecture when combined with workload controls.

Potential areas include:

* Encryption in transit
* Encryption at rest
* Key management
* Data classification
* Data residency
* Backup protection
* Access control

### NIST SP 800-53 — Examples

* **SC-8:** Transmission and confidentiality protection
* **SC-12:** Cryptographic key establishment and management
* **SC-13:** Cryptographic protection

### ISO/IEC 27001 — Examples

* **A.5.12:** Classification of information
* **A.5.13:** Labelling of information
* **A.8.24:** Use of cryptography
* **A.8.25–A.8.29:** Secure development and related controls

### Considerations

These controls require additional workload, data, and key-management architecture.

They should not be considered implemented by the landing-zone Terraform alone.

---

## 7. Security Architecture Support

The landing zone provides infrastructure foundations that can support additional security architecture.

Examples include:

* Network segmentation
* Network telemetry
* Basic access-control boundaries
* Repeatable infrastructure configuration

Those capabilities can become inputs to higher-level security controls such as:

* Zero Trust architecture
* Centralized security monitoring
* Workload identity
* Privileged access management
* Application security
* Data-security controls

The relationship is architectural rather than a claim that those higher-level controls are implemented here.

---

## 8. Control Responsibility by Layer

| Security Concern                        | Primary Layer      |
| --------------------------------------- | ------------------ |
| Organizational guardrails               | Layer 1            |
| Account/subscription/project governance | Layer 1 / Layer 2  |
| Network foundation                      | Layer 2            |
| Basic IAM/RBAC primitives               | Layer 2            |
| Centralized identity                    | Layer 1            |
| Workload identity                       | Layer 3            |
| Application security                    | Layer 3            |
| Data protection                         | Layer 3            |
| CI/CD security                          | Layer 3 / Platform |
| Centralized detection                   | Cross-layer        |

This separation helps prevent a foundational Terraform template from being treated as the complete security solution.

---

## 9. Evidence Considerations

The Terraform repository itself is not sufficient compliance evidence.

Depending on the control, useful production evidence could include:

* Terraform plans and apply records
* IAM/RBAC configuration
* Network configuration
* Policy assignments
* Logging configuration
* Cloud audit records
* Compliance results
* Access reviews
* Change approvals
* Exception records
* Monitoring evidence

Evidence should demonstrate that a control is actually implemented and operating within the organization's defined scope.

---

## 10. Limitations

This mapping is:

* Illustrative
* Non-exhaustive
* Architecture-oriented
* Dependent on additional controls

Control relevance and effectiveness depend on:

* How the Terraform is deployed
* The surrounding Layer-1 governance
* Workload architecture
* Organizational procedures
* Risk assessments
* Monitoring
* Validation

The repository does not provide formal compliance certification or audit evidence.

---

## Summary

The Layer-2 Multicloud Landing Zone provides foundational support for several security-control objectives, particularly around:

* Network segmentation
* Basic access control
* Network visibility
* Infrastructure configuration
* Repeatable cloud foundations

The architecture is intended to become one component of a broader security and governance program.

The central distinction is:

> **A Terraform implementation can establish a technical control foundation, but compliance depends on the complete control environment: architecture, configuration, governance, operation, evidence, and ongoing validation.**
