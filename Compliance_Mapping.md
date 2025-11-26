# Compliance Mapping (Illustrative)

This document provides an **illustrative** mapping between the Layer-2 Multicloud Landing Zone patterns and common control frameworks. It is *not* a formal compliance attestation, but a helpful reference for how these templates can support broader compliance objectives.

Frameworks referenced:

- NIST SP 800-53 (Rev. 5 style families)
- ISO/IEC 27001 (Annex A themes)
- CIS Controls / cloud benchmarks (high level)

---

## 1. Identity and Access Management

**Relevant patterns in this repo:**

- Example read-only IAM roles / RBAC assignments.
- Use of native identity primitives (roles, assignments, bindings, policies).
- Avoidance of hardcoded credentials in code.

**NIST 800-53 (examples):**

- **AC-1, AC-2:** Access control policy & account management.
- **AC-3, AC-6:** Access enforcement and least privilege.
- **IA-2:** Identification and authentication (users).
- **IA-5:** Authenticator management (ties into IdP and MFA outside this repo).

**ISO/IEC 27001 (Annex A) (examples):**

- **A.5.15:** Access control.
- **A.5.16:** Identity management.
- **A.8.2:** Information access restriction.
- **A.8.3:** User access provisioning.

**CIS (examples):**

- **CIS Control 6:** Access control management.
- **CIS Control 16:** Application software security (indirectly).
- Cloud benchmarks: IAM configuration best practices (e.g., AWS, Azure, GCP specific).

**Notes:**

- This repo shows *patterns* (roles, bindings) and not full identity governance.
- Full compliance requires integration with SSO, IdP, MFA, and lifecycle management processes.

---

## 2. Network Security and Segmentation

**Relevant patterns in this repo:**

- Creation of virtual network constructs (VPC/VNet/VPC/VCN).
- Public and private subnet segmentation.
- Basic firewall / security group examples (esp. GCP firewall, Azure NSG hooks, OCI subnet settings).

**NIST 800-53 (examples):**

- **SC-7:** Boundary protection.
- **SC-32:** System partitioning.
- **AC-4:** Information flow enforcement.

**ISO/IEC 27001 (Annex A):**

- **A.8.20:** Network security.
- **A.8.21:** Security of network services.
- **A.8.23:** Segregation in networks.

**CIS:**

- **CIS Control 4:** Secure configuration of enterprise assets and software.
- **CIS Control 12:** Network infrastructure management.

**Notes:**

- These templates define the logical segmentation; further hardening (e.g., WAF, NGFW, east-west firewalling) should be added for production workloads.

---

## 3. Logging, Monitoring, and Audit Trails

**Relevant patterns in this repo:**

- Enabling flow logs and diagnostics:
  - AWS: VPC Flow Logs → CloudWatch Logs.
  - Azure: VNet diagnostics → Log Analytics.
  - GCP: VPC Flow Logs → Cloud Logging.
  - OCI: Logging via log groups.

**NIST 800-53:**

- **AU-2:** Event logging.
- **AU-6:** Audit review, analysis, and reporting.
- **AU-8:** Time stamps.
- **SI-4:** System monitoring.

**ISO/IEC 27001 (Annex A):**

- **A.8.15:** Logging.
- **A.8.16:** Monitoring activities.
- **A.8.12:** Privileged access rights (when combined with IAM).

**CIS:**

- **CIS Control 8:** Audit log management.
- **CIS Control 13:** Network monitoring and defense.

**Notes:**

- These templates create the **log sources**; centralization, retention, correlation, and alerting integrations (e.g., SIEM) must be implemented to fully meet control intent.

---

## 4. Configuration Management and Infrastructure as Code

**Relevant patterns in this repo:**

- Terraform-based infrastructure definitions.
- Separation of configuration (variables) from logic (main.tf).
- Version-control friendliness and `.gitignore` usage.

**NIST 800-53:**

- **CM-2:** Baseline configuration.
- **CM-3:** Configuration change control.
- **CM-6:** Configuration settings.

**ISO/IEC 27001 (Annex A):**

- **A.8.9:** Configuration management.
- **A.8.33:** Test and production environment separation.
- **A.5.36:** Change management.

**CIS:**

- **CIS Control 4:** Secure configuration of enterprise assets.
- **CIS Control 11:** Data recovery processes (indirect, via reproducibility).

**Notes:**

- Formal change control processes, approvals, and CI/CD workflows are required outside this repo to fully satisfy these controls.

---

## 5. Governance and Organizational Controls (Layer 1)

This repository is **Layer 2** only. However, it aligns with and prepares for Layer-1 governance controls such as:

- AWS Organizations & SCPs, Control Tower / Landing Zone Accelerator.
- Azure Management Groups and Azure Policy.
- GCP Organization policies and folder structure.
- OCI compartment hierarchy and tenancy-level policies.

**NIST 800-53:**

- **PM-1, PM-9:** Information security program and risk management strategy.
- **PL-2:** System and communications protection planning.
- **CA-2, CA-7:** Assessments and continuous monitoring.

**ISO/IEC 27001 (Annex A):**

- **A.5.1–A.5.4:** Information security policies and roles.
- **A.5.7:** Threat intelligence (when combined with monitoring).
- **A.6:** Organization of information security.

**Notes:**

- Actual governance requires policy definition, documented procedures, risk assessments, and ongoing oversight.

---

## 6. Data Protection and Privacy

Although this Layer-2 repo does not provision data services directly, the network and logging constructs support:

- Protection of data in transit via proper segmentation and TLS termination (once workloads are added).
- Monitoring of data flows for anomalies via flow logs and diagnostics.

Controls potentially supported once extended with data services:

- **NIST:** SC-12, SC-13 (cryptographic protection), MP-5 (media transport).
- **ISO 27001:** A.8.24–A.8.29 (information classification and handling), A.8.12 (privileged access).
- **CIS:** Controls related to data protection and data access control.

---

## 7. Limitations of This Mapping

- This mapping is **illustrative**, not exhaustive.
- Control coverage depends on:
  - How these templates are instantiated.
  - What additional controls exist at Layer 1 and Layer 3.
  - The organization’s policies and procedures surrounding them.
- Formal compliance requires:
  - Risk assessments.
  - Documentation of processes and responsibilities.
  - Independent validation or audit.

---

## Summary

The Layer-2 Multicloud Landing Zone templates provide **foundational** support for several NIST 800-53, ISO/IEC 27001, and CIS control objectives, particularly around:

- Identity & access baselines
- Network segmentation
- Logging and visibility
- Infrastructure-as-code and configuration management

They are intended to be integrated into a broader governance, risk, and compliance strategy—not to stand alone as proof of compliance.
