# HIPAA Security Rule Requirements Review List

## Purpose

This document captures the HIPAA Security Rule requirements being reviewed against Microsoft Fabric from a **platform-vendor capability perspective**.

The primary question for each requirement is:

> Does Microsoft Fabric provide the technical capability necessary for a healthcare organization to implement this HIPAA requirement?

The assessment does **not** assume that Microsoft Fabric itself is responsible for carrying out organizational, legal, HR, privacy, or clinical processes.

For each requirement, the review should determine one of the following:

- Fabric provides the necessary platform capability and no product gap exists.
- Fabric provides the capability, but implementation requires specific architectural controls.
- Fabric has a material capability or assurance gap.
- The requirement is primarily organizational and therefore does not create a Fabric capability requirement.

---

# Review Status Legend

| Status | Meaning |
|---|---|
| ✅ **Discussed** | Requirement has been explicitly reviewed in the working assessment |
| ◐ **Partially Discussed** | Requirement was addressed as part of a broader requirement family, but still warrants an explicit standalone review |
| ⬜ **Not Yet Discussed** | Requirement has not yet been explicitly reviewed |

---

# 1. General Security Rule Requirement

## S01 — Confidentiality, Integrity, and Availability

**Status:** ✅ Discussed

**Citation:** 45 CFR §164.306(a)-(e)

**Requirement:**

Protect the confidentiality, integrity, and availability of ePHI.

The organization must also protect against reasonably anticipated threats, impermissible uses or disclosures, and workforce noncompliance.

**Source:**

[45 CFR §164.306 — Security Standards: General Rules](https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-C/section-164.306)

### Fabric Assessment Status

Fabric provides the fundamental access-control, security, identity, encryption, availability, and auditing mechanisms necessary to support this requirement.

However, the review identified several capability and assurance gaps associated with demonstrating the requirement consistently across Fabric.

Relevant gaps:

- **FAB-GAP-001 — PHI Classification Granularity**
- **FAB-GAP-002 — Fragmented Authorization Across Fabric Security Planes**
- **FAB-GAP-003 — Audit Completeness, Attribution, and Data-Access Granularity**

---

# 2. Administrative Safeguards

HIPAA administrative safeguards are defined primarily in:

[45 CFR §164.308 — Administrative Safeguards](https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-C/section-164.308)

---

## S02 — Risk Analysis

**Status:** ✅ Discussed

**Citation:** 45 CFR §164.308(a)(1)(ii)(A)

**Class:** Required

**Requirement:**

Conduct an accurate and thorough assessment of the potential risks and vulnerabilities to the confidentiality, integrity, and availability of ePHI.

From a data-platform perspective, this requires sufficient evidence to understand:

- where ePHI is stored;
- where ePHI is processed;
- where ePHI moves;
- which identities interact with it;
- which systems consume it;
- which external destinations receive it.

### Fabric Assessment Status

Fabric provides item-level lineage and metadata capabilities that assist with risk analysis.

However, complete table-level and historical lineage is not natively available across all Fabric paths.

Relevant gaps:

- **FAB-GAP-004 — Table-Level and Historical PHI Lineage**
- **FAB-GAP-005 — Native Fabric Governance Capability Dependency on Microsoft Purview**

---

## S03 — Risk Management

**Status:** ✅ Discussed

**Citation:** 45 CFR §164.308(a)(1)(ii)(B)

**Class:** Required

**Requirement:**

Implement security measures sufficient to reduce identified risks and vulnerabilities to a reasonable and appropriate level.

### Fabric Assessment Status

Fabric provides the technical capabilities necessary to implement risk-mitigation controls.

A representative access-control architecture can use:

```text
PHI Table
    |
    v
OneLake Security Role
    |
    v
Entra Security Group
    |
    v
Approved Access Workflow
    |
    v
Authorized User
```

The approval workflow may be implemented through an external governance or service-management platform such as ServiceNow.

**Conclusion:** No new inherent Fabric capability gap identified for this requirement.

---

## S04 — Sanction Policy

**Status:** ✅ Discussed

**Citation:** 45 CFR §164.308(a)(1)(ii)(C)

**Class:** Required

**Requirement:**

Apply appropriate sanctions against workforce members who fail to comply with security policies and procedures.

### Fabric Assessment Status

This is primarily an organizational and HR responsibility.

Fabric can provide supporting audit evidence, but Fabric is not expected to define or administer workforce disciplinary policy.

**Conclusion:** No Fabric capability gap.

---

## S05 — Information System Activity Review

**Status:** ✅ Discussed

**Citation:** 45 CFR §164.308(a)(1)(ii)(D)

**Class:** Required

**Requirement:**

Implement procedures to regularly review records of information-system activity, including:

- audit logs;
- access reports;
- security incident tracking reports.

### Fabric Assessment Status

Fabric provides substantial activity and audit telemetry.

However, evidence is distributed across multiple logging systems and access mechanisms.

Relevant gap:

- **FAB-GAP-003 — Audit Completeness, Attribution, and Data-Access Granularity**

No additional separate gap is necessary.

---

## S06 — Assigned Security Responsibility

**Status:** ✅ Discussed

**Citation:** 45 CFR §164.308(a)(2)

**Class:** Standard

**Requirement:**

Identify the security official responsible for developing and implementing security policies and procedures.

### Fabric Assessment Status

This is an organizational governance responsibility.

Fabric is not required to designate the healthcare organization's security officer.

**Conclusion:** No Fabric capability gap.

---

# 3. Workforce Security

## S07 — Authorization and/or Supervision

**Status:** ◐ Partially Discussed

**Citation:** 45 CFR §164.308(a)(3)(ii)(A)

**Class:** Addressable

**Requirement:**

Implement procedures for authorizing and supervising workforce members who work with ePHI or in locations where ePHI may be accessed.

### Fabric Capability Question

Does Fabric provide sufficient control points for an organization to grant access only to approved workforce members?

### Preliminary Fabric Position

Yes.

Relevant mechanisms include:

- workspace RBAC;
- item permissions;
- OneLake security;
- SQL security;
- Entra groups.

Relevant broader gap:

- **FAB-GAP-002 — Fragmented Authorization Across Fabric Security Planes**

---

## S08 — Workforce Clearance Procedure

**Status:** ◐ Partially Discussed

**Citation:** 45 CFR §164.308(a)(3)(ii)(B)

**Class:** Addressable

**Requirement:**

Implement procedures to determine whether workforce access to ePHI is appropriate.

### Fabric Capability Question

Can Fabric enforce an access decision made by the organization's clearance process?

### Preliminary Fabric Position

Yes.

The clearance decision itself is organizational.

Fabric provides the enforcement mechanisms through Entra groups, workspace permissions, OneLake security roles, and other supported authorization systems.

**No new Fabric gap identified.**

---

## S09 — Termination Procedures

**Status:** ◐ Partially Discussed

**Citation:** 45 CFR §164.308(a)(3)(ii)(C)

**Class:** Addressable

**Requirement:**

Implement procedures for terminating access to ePHI when employment or other authorization ends.

### Fabric Capability Question

Can Fabric access be revoked when authorization ends?

### Preliminary Fabric Position

Yes, through:

- Entra identity disabling;
- group removal;
- workspace permission removal;
- OneLake role removal;
- SQL grant removal;
- semantic-model permission removal.

A separate consideration is the time required for revocation to propagate through tokens, sessions, and cached identities.

This should receive explicit standalone review.

---

# 4. Information Access Management

## S10 — Isolating Healthcare Clearinghouse Functions

**Status:** ⬜ Not Yet Discussed

**Citation:** 45 CFR §164.308(a)(4)(ii)(A)

**Class:** Required when applicable

**Requirement:**

If a healthcare clearinghouse is part of a larger organization, protect the clearinghouse's ePHI from unauthorized access by the larger organization.

### Fabric Capability Question

Can Fabric isolate data, identities, workspaces, and access paths sufficiently to separate regulated clearinghouse functions from unrelated organizational functions?

---

## S11 — Access Authorization

**Status:** ✅ Discussed

**Citation:** 45 CFR §164.308(a)(4)(ii)(B)

**Class:** Addressable

**Requirement:**

Implement policies and procedures for granting access to ePHI.

### Fabric Assessment Status

Fabric provides sufficient mechanisms through:

- workspace RBAC;
- item permissions;
- OneLake security;
- SQL security;
- semantic-model security;
- Entra groups.

Relevant gap:

- **FAB-GAP-002 — Fragmented Authorization Across Fabric Security Planes**

---

## S12 — Access Establishment and Modification

**Status:** ◐ Partially Discussed

**Citation:** 45 CFR §164.308(a)(4)(ii)(C)

**Class:** Addressable

**Requirement:**

Implement policies and procedures to:

- establish access;
- document access;
- review access;
- modify access.

### Fabric Capability Question

Can Fabric represent, modify, and provide evidence of user and workload access over time?

### Preliminary Concern

Fabric provides current authorization state and permission-change evidence, but historical effective-access reconstruction can require correlation across:

- Entra groups;
- workspace roles;
- OneLake roles;
- SQL grants;
- semantic-model permissions;
- workload identities.

This should receive explicit standalone review.

---

# 5. Security Awareness and Training

## S13 — Security Awareness and Training

**Status:** ✅ Discussed

**Citation:** 45 CFR §164.308(a)(5)(i)

**Class:** Standard

**Requirement:**

Implement a security awareness and training program for workforce members.

### Fabric Assessment Status

This is primarily an organizational responsibility.

Fabric is not required to supply the organization's workforce security-training program.

**Conclusion:** No Fabric capability gap.

---

## S14 — Security Reminders

**Status:** ⬜ Not Yet Discussed

**Citation:** 45 CFR §164.308(a)(5)(ii)(A)

**Class:** Addressable

**Requirement:**

Provide periodic security updates or reminders to workforce members.

### Fabric Capability Question

Primarily organizational rather than a Fabric platform capability.

Likely no Fabric gap, but explicit review has not yet been completed.

---

## S15 — Protection from Malicious Software

**Status:** ⬜ Not Yet Discussed

**Citation:** 45 CFR §164.308(a)(5)(ii)(B)

**Class:** Addressable

**Requirement:**

Implement procedures for guarding against, detecting, and reporting malicious software.

### Fabric Capability Question

Evaluate responsibility across:

- Microsoft-managed SaaS infrastructure;
- notebooks;
- libraries;
- packages;
- gateways;
- endpoints;
- CI/CD dependencies;
- external tools.

---

## S16 — Log-In Monitoring

**Status:** ⬜ Not Yet Discussed

**Citation:** 45 CFR §164.308(a)(5)(ii)(C)

**Class:** Addressable

**Requirement:**

Implement procedures for monitoring login attempts and reporting discrepancies.

### Fabric Capability Question

Evaluate Fabric and Entra capabilities for:

- successful authentication;
- failed authentication;
- suspicious login behavior;
- workload identities;
- service principals;
- guest users.

---

## S17 — Password and Credential Management

**Status:** ⬜ Not Yet Discussed

**Citation:** 45 CFR §164.308(a)(5)(ii)(D)

**Class:** Addressable

**Requirement:**

Implement procedures for creating, changing, and safeguarding passwords and credentials.

### Fabric Capability Question

Evaluate:

- Entra authentication;
- managed/workspace identities;
- service principals;
- connection identities;
- secrets;
- token-based access;
- credential rotation.

---

# 6. Security Incident Procedures

## S18 — Security Incident Procedures

**Status:** ✅ Discussed

**Citation:** 45 CFR §164.308(a)(6)(i)-(ii)

**Class:** Standard + Required

**Requirement:**

Identify and respond to suspected or known security incidents, mitigate harmful effects, and document incidents and outcomes.

### Fabric Assessment Status

Fabric provides evidence to support incident detection and investigation.

The primary limitation is already captured by:

- **FAB-GAP-003 — Audit Completeness, Attribution, and Data-Access Granularity**

No additional duplicate gap was identified.

---

# 7. Contingency Plan

## S19 — Data Backup Plan

**Status:** ◐ Partially Discussed

**Citation:** 45 CFR §164.308(a)(7)(ii)(A)

**Class:** Required

**Requirement:**

Establish and implement procedures to create and maintain retrievable exact copies of ePHI.

### Fabric Discussion Completed So Far

Fabric provides:

- OneLake resilience;
- recovery mechanisms;
- regional replication;
- Git integration for supported artifact definitions.

The distinction between data recovery and artifact/configuration recovery has been recognized.

Explicit standalone assessment should still be completed.

---

## S20 — Disaster Recovery Plan

**Status:** ✅ Discussed

**Citation:** 45 CFR §164.308(a)(7)(ii)(B)

**Class:** Required

**Requirement:**

Establish procedures to restore lost data and required operations following disruption.

### Fabric Assessment Status

Fabric provides capacity disaster recovery and asynchronous regional replication for supported data.

Supported definitions and code can also be maintained through Git integration and reconstruction processes.

**Conclusion:** No inherent Fabric capability gap identified from the platform-vendor perspective.

---

## S21 — Emergency Mode Operation Plan

**Status:** ⬜ Not Yet Discussed

**Citation:** 45 CFR §164.308(a)(7)(ii)(C)

**Class:** Required

**Requirement:**

Establish procedures that enable critical business processes for protection of ePHI during emergency operations.

### Fabric Capability Question

Evaluate whether Fabric provides sufficient:

- failover;
- identity access;
- recovery;
- alternate processing;
- degraded-mode operation

to support customer emergency procedures.

---

## S22 — Testing and Revision Procedures

**Status:** ⬜ Not Yet Discussed

**Citation:** 45 CFR §164.308(a)(7)(ii)(D)

**Class:** Addressable

**Requirement:**

Periodically test and revise contingency procedures.

### Fabric Capability Question

Can customers meaningfully test:

- regional failure;
- workspace deletion;
- data loss;
- credential loss;
- key failure;
- recovery;
- reconstruction?

---

## S23 — Applications and Data Criticality Analysis

**Status:** ⬜ Not Yet Discussed

**Citation:** 45 CFR §164.308(a)(7)(ii)(E)

**Class:** Addressable

**Requirement:**

Assess the relative criticality of applications and data in support of contingency planning.

### Fabric Capability Question

Does Fabric expose enough metadata, dependency information, lineage, capacity information, and workload structure to support criticality analysis?

Potential relationship:

- **FAB-GAP-004 — Table-Level and Historical PHI Lineage**

---

# 8. Evaluation

## S24 — Periodic Technical and Nontechnical Evaluation

**Status:** ✅ Discussed

**Citation:** 45 CFR §164.308(a)(8)

**Class:** Standard

**Requirement:**

Periodically evaluate security controls and reevaluate them when operational or environmental changes affect ePHI security.

### Fabric Assessment Status

Fabric exposes sufficient configuration and operational evidence through:

- tenant settings;
- workspace settings;
- permissions;
- OneLake security;
- SQL security;
- audit data;
- monitoring;
- APIs.

Tenant configuration alone does not demonstrate control effectiveness.

However, the broader Fabric evidence ecosystem provides sufficient platform capability.

**Conclusion:** No new Fabric capability gap identified.

---

# 9. Business Associate Arrangements

## S25 — Business Associate Contracts and Other Arrangements

**Status:** ⬜ Not Yet Discussed

**Citation:** 45 CFR §164.308(b)(1)-(3)

**Class:** Required

**Requirement:**

Obtain appropriate written assurances from business associates that handle ePHI.

### Fabric Capability Question

This is primarily contractual rather than technical.

The review should establish whether the Microsoft contractual framework, DPA, BAA provisions, and selected Fabric services support the intended PHI processing.

---

# 10. Physical Safeguards

HIPAA physical safeguards are primarily defined in:

[45 CFR §164.310 — Physical Safeguards](https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-C/section-164.310)

---

## S26 — Emergency Facility Access

**Status:** ⬜ Not Yet Discussed

**Citation:** 45 CFR §164.310(a)(2)(i)

**Class:** Addressable

**Requirement:**

Establish procedures allowing facility access in support of restoration of lost data and emergency operations.

### Fabric Capability Question

Microsoft manages Fabric's physical cloud infrastructure.

The healthcare organization remains responsible for its own offices, devices, gateways, networking facilities, and emergency access arrangements.

---

## S27 — Facility Security Plan

**Status:** ⬜ Not Yet Discussed

**Citation:** 45 CFR §164.310(a)(1), §164.310(a)(2)(ii)

**Class:** Standard + Addressable

**Requirement:**

Implement policies and procedures to safeguard facilities and equipment from unauthorized physical access.

### Fabric Capability Question

Evaluate Microsoft physical-security assurances for hosted infrastructure.

Likely primarily a provider-assurance and organizational responsibility.

---

## S28 — Access Control and Validation Procedures

**Status:** ⬜ Not Yet Discussed

**Citation:** 45 CFR §164.310(a)(2)(iii)

**Class:** Addressable

**Requirement:**

Control and validate physical access according to a person's role or function.

### Fabric Capability Question

Primarily outside Fabric itself.

Cloud authentication does not replace physical facility-access controls.

---

## S29 — Maintenance Records

**Status:** ⬜ Not Yet Discussed

**Citation:** 45 CFR §164.310(a)(2)(iv)

**Class:** Addressable

**Requirement:**

Document repairs and modifications to physical components affecting facility security.

### Fabric Capability Question

Microsoft-managed infrastructure requires provider assurance.

Customer-managed physical infrastructure remains an organizational responsibility.

---

## S30 — Workstation Use

**Status:** ⬜ Not Yet Discussed

**Citation:** 45 CFR §164.310(b)

**Class:** Standard

**Requirement:**

Define appropriate functions and physical characteristics for workstations accessing ePHI.

### Fabric Capability Question

Evaluate interactions with:

- browsers;
- desktop clients;
- notebooks;
- SQL clients;
- managed endpoints;
- VDI;
- downloads.

---

## S31 — Workstation Security

**Status:** ⬜ Not Yet Discussed

**Citation:** 45 CFR §164.310(c)

**Class:** Standard

**Requirement:**

Implement physical safeguards restricting access to workstations containing or accessing ePHI.

### Fabric Capability Question

Mostly endpoint and organizational responsibility.

Fabric can integrate with identity and Conditional Access controls but does not physically secure endpoints.

---

## S32 — Device and Media Controls / Disposal

**Status:** ⬜ Not Yet Discussed

**Citation:** 45 CFR §164.310(d)(1), §164.310(d)(2)(i)

**Class:** Standard + Required

**Requirement:**

Control receipt, removal, disposal, and movement of hardware and electronic media containing ePHI.

### Fabric Capability Question

Evaluate:

- exports;
- downloaded files;
- caches;
- backups;
- local copies;
- cloud deletion behavior.

---

## S33 — Media Reuse

**Status:** ⬜ Not Yet Discussed

**Citation:** 45 CFR §164.310(d)(2)(ii)

**Class:** Required

**Requirement:**

Remove ePHI before media is reused.

### Fabric Capability Question

Primarily shared responsibility between Microsoft infrastructure handling and customer-controlled exports/endpoints.

---

## S34 — Accountability for Hardware and Media

**Status:** ⬜ Not Yet Discussed

**Citation:** 45 CFR §164.310(d)(2)(iii)

**Class:** Addressable

**Requirement:**

Maintain records of hardware and media movement and accountable individuals where appropriate.

### Fabric Capability Question

Assess relevance to:

- exports;
- offline copies;
- removable media;
- generated files.

---

## S35 — Data Backup and Storage Before Equipment Movement

**Status:** ⬜ Not Yet Discussed

**Citation:** 45 CFR §164.310(d)(2)(iv)

**Class:** Addressable

**Requirement:**

Create retrievable exact copies of ePHI before equipment movement where appropriate.

### Fabric Capability Question

Mostly endpoint/customer infrastructure responsibility rather than Fabric SaaS functionality.

---

# 11. Technical Safeguards

HIPAA technical safeguards are defined primarily in:

[45 CFR §164.312 — Technical Safeguards](https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-C/section-164.312)

---

## S36 — Access Control

**Status:** ✅ Discussed

**Citation:** 45 CFR §164.312(a)(1)

**Class:** Standard

**Requirement:**

Implement technical policies and procedures that allow access to ePHI only to persons or software programs that have been granted access rights.

### Fabric Assessment Status

Fabric provides extensive access-control capabilities.

Relevant gap:

- **FAB-GAP-002 — Fragmented Authorization Across Fabric Security Planes**

The issue is consistency across access paths, not absence of access-control capability.

---

## S37 — Unique User Identification

**Status:** ◐ Partially Discussed

**Citation:** 45 CFR §164.312(a)(2)(i)

**Class:** Required

**Requirement:**

Assign a unique identifier for identifying and tracking user identity.

### Fabric Capability Question

Evaluate:

- Entra users;
- service principals;
- workspace identities;
- workload identities;
- delegated execution;
- effective query identity.

Potential relationship:

- **FAB-GAP-003 — Audit Completeness, Attribution, and Data-Access Granularity**

---

## S38 — Emergency Access Procedure

**Status:** ⬜ Not Yet Discussed

**Citation:** 45 CFR §164.312(a)(2)(ii)

**Class:** Required

**Requirement:**

Establish procedures for obtaining necessary ePHI during an emergency.

### Fabric Capability Question

Evaluate whether Fabric supports attributable emergency access while retaining:

- authentication;
- authorization;
- logging;
- recovery access;
- key access.

---

## S39 — Automatic Logoff

**Status:** ⬜ Not Yet Discussed

**Citation:** 45 CFR §164.312(a)(2)(iii)

**Class:** Addressable

**Requirement:**

Implement electronic procedures terminating an electronic session after a predetermined period of inactivity when reasonable and appropriate.

### Fabric Capability Question

Evaluate:

- browser sessions;
- Power BI;
- SQL clients;
- notebooks;
- APIs;
- Entra session policies;
- Conditional Access;
- lack of Fabric CAE support.

---

## S40 — Encryption and Decryption

**Status:** ⬜ Not Yet Discussed

**Citation:** 45 CFR §164.312(a)(2)(iv)

**Class:** Addressable

**Requirement:**

Implement a mechanism to encrypt and decrypt ePHI when reasonable and appropriate.

### Fabric Capability Question

Evaluate:

- default encryption at rest;
- encryption in transit;
- workspace CMK;
- Power BI BYOK;
- export encryption;
- backups;
- logs;
- key scope.

---

## S41 — Audit Controls

**Status:** ✅ Discussed

**Citation:** 45 CFR §164.312(b)

**Class:** Standard

**Requirement:**

Implement hardware, software, and/or procedural mechanisms that record and examine activity in systems containing or using ePHI.

### Fabric Assessment Status

Fabric provides multiple audit capabilities.

Relevant gap:

- **FAB-GAP-003 — Audit Completeness, Attribution, and Data-Access Granularity**

---

## S42 — Protection Against Improper Alteration or Destruction

**Status:** ⬜ Not Yet Discussed

**Citation:** 45 CFR §164.312(c)(1)

**Class:** Standard

**Requirement:**

Implement policies and procedures to protect ePHI from improper alteration or destruction.

### Fabric Capability Question

Evaluate:

- write permissions;
- transaction controls;
- Delta capabilities;
- data validation;
- source reconciliation;
- recovery;
- destructive administrative actions.

---

## S43 — Mechanism to Authenticate ePHI Integrity

**Status:** ⬜ Not Yet Discussed

**Citation:** 45 CFR §164.312(c)(2)

**Class:** Addressable

**Requirement:**

Implement electronic mechanisms to confirm that ePHI has not been improperly altered or destroyed.

### Fabric Capability Question

Evaluate:

- checksums;
- manifests;
- transactional integrity;
- source reconciliation;
- authenticated ingestion;
- version history;
- data validation.

---

## S44 — Person or Entity Authentication

**Status:** ⬜ Not Yet Discussed

**Citation:** 45 CFR §164.312(d)

**Class:** Standard

**Requirement:**

Implement procedures verifying that a person or entity seeking access to ePHI is who or what it claims to be.

### Fabric Capability Question

Evaluate:

- Entra authentication;
- MFA;
- service principals;
- workload identities;
- workspace identities;
- managed identities;
- downstream authentication.

---

## S45 — Transmission Security

**Status:** ⬜ Not Yet Discussed

**Citation:** 45 CFR §164.312(e)(1)

**Class:** Standard

**Requirement:**

Implement technical security measures guarding against unauthorized access to ePHI transmitted over electronic communications networks.

### Fabric Capability Question

Evaluate all:

- ingress paths;
- egress paths;
- connectors;
- gateways;
- APIs;
- Spark connections;
- SQL connections;
- cross-workspace traffic;
- external recipients.

---

## S46 — Transmission Integrity

**Status:** ⬜ Not Yet Discussed

**Citation:** 45 CFR §164.312(e)(2)(i)

**Class:** Addressable

**Requirement:**

Implement measures ensuring electronically transmitted ePHI is not improperly modified without detection.

### Fabric Capability Question

Evaluate:

- TLS integrity;
- certificate validation;
- authenticated protocols;
- data reconciliation;
- tamper detection.

---

## S47 — Transmission Encryption

**Status:** ⬜ Not Yet Discussed

**Citation:** 45 CFR §164.312(e)(2)(ii)

**Class:** Addressable

**Requirement:**

Implement a mechanism to encrypt ePHI in transit when reasonable and appropriate.

### Fabric Capability Question

Evaluate:

- Fabric TLS requirements;
- gateways;
- APIs;
- external connections;
- notebook code;
- exports;
- recipient systems.

---

# 12. Organizational Requirements

HIPAA organizational requirements are primarily defined in:

[45 CFR §164.314 — Organizational Requirements](https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-C/section-164.314)

---

## S48 — Business Associate and Subcontractor Arrangements

**Status:** ⬜ Not Yet Discussed

**Citation:** 45 CFR §164.314(a)

**Class:** Required

**Requirement:**

Ensure appropriate contractual arrangements exist with business associates and downstream subcontractors.

### Fabric Capability Question

Primarily contractual.

Evaluate:

- Microsoft BAA/DPA coverage;
- selected Fabric services;
- third-party connectors;
- subcontractors;
- support services.

---

## S49 — Group Health Plan Safeguards

**Status:** ⬜ Not Yet Discussed

**Citation:** 45 CFR §164.314(b)

**Class:** Required when applicable

**Requirement:**

Ensure appropriate separation between group health plan PHI and plan sponsors.

### Fabric Capability Question

Can Fabric isolate employee-plan PHI from unrelated HR, analytics, and organizational access?

---

# 13. Policies, Procedures, and Documentation

HIPAA documentation requirements are defined primarily in:

[45 CFR §164.316 — Policies, Procedures, and Documentation](https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-C/section-164.316)

---

## S50 — Reasonable and Appropriate Policies and Procedures

**Status:** ⬜ Not Yet Discussed

**Citation:** 45 CFR §164.316(a)

**Class:** Standard

**Requirement:**

Implement reasonable and appropriate policies and procedures to comply with the Security Rule.

### Fabric Capability Question

Primarily organizational.

Fabric must provide sufficient configurable controls for the organization to implement its approved security policies.

---

## S51 — Written or Electronic Documentation

**Status:** ⬜ Not Yet Discussed

**Citation:** 45 CFR §164.316(b)(1)

**Class:** Standard

**Requirement:**

Maintain required policies, procedures, actions, activities, and assessments in written or electronic form.

### Fabric Capability Question

Primarily organizational.

Evaluate whether Fabric, Purview, or external systems provide sufficient evidence for documenting implemented platform controls.

---

## S52 — Documentation Retention

**Status:** ⬜ Not Yet Discussed

**Citation:** 45 CFR §164.316(b)(2)(i)

**Class:** Required

**Requirement:**

Retain required Security Rule documentation for six years from creation or the date it was last in effect, whichever is later.

### Important Qualification

This is **not** a universal six-year retention requirement for:

- all raw logs;
- all medical records;
- every telemetry event.

### Fabric Capability Question

Evaluate retention and export mechanisms for the evidence that the organization chooses or is required to preserve.

Potential relationships:

- **FAB-GAP-003 — Audit Completeness, Attribution, and Data-Access Granularity**
- **FAB-GAP-004 — Table-Level and Historical PHI Lineage**

---

## S53 — Documentation Availability

**Status:** ⬜ Not Yet Discussed

**Citation:** 45 CFR §164.316(b)(2)(ii)

**Class:** Required

**Requirement:**

Make documentation available to persons responsible for implementing the procedures to which the documentation pertains.

### Fabric Capability Question

Evaluate whether evidence can be accessed by appropriately authorized security, compliance, architecture, and audit personnel without granting unnecessarily broad access to PHI.

---

## S54 — Documentation Updates

**Status:** ⬜ Not Yet Discussed

**Citation:** 45 CFR §164.316(b)(2)(iii)

**Class:** Required

**Requirement:**

Review documentation periodically and update it in response to environmental or operational changes affecting ePHI security.

### Fabric Capability Question

Evaluate:

- configuration APIs;
- Git integration;
- deployment history;
- audit history;
- architecture versioning;
- policy history;
- product change tracking.

Potential relationships:

- **FAB-GAP-004 — Table-Level and Historical PHI Lineage**
- **FAB-GAP-005 — Native Fabric Governance Capability Dependency on Microsoft Purview**

---

# Review Progress Summary

| ID | Requirement | Status |
|---|---|---|
| S01 | Confidentiality, Integrity, Availability | ✅ Discussed |
| S02 | Risk Analysis | ✅ Discussed |
| S03 | Risk Management | ✅ Discussed |
| S04 | Sanction Policy | ✅ Discussed |
| S05 | Information System Activity Review | ✅ Discussed |
| S06 | Assigned Security Responsibility | ✅ Discussed |
| S07 | Authorization / Supervision | ◐ Partially Discussed |
| S08 | Workforce Clearance | ◐ Partially Discussed |
| S09 | Termination Procedures | ◐ Partially Discussed |
| S10 | Clearinghouse Isolation | ⬜ Not Yet Discussed |
| S11 | Access Authorization | ✅ Discussed |
| S12 | Access Establishment / Modification | ◐ Partially Discussed |
| S13 | Security Awareness and Training | ✅ Discussed |
| S14 | Security Reminders | ⬜ Not Yet Discussed |
| S15 | Malicious Software Protection | ⬜ Not Yet Discussed |
| S16 | Login Monitoring | ⬜ Not Yet Discussed |
| S17 | Password / Credential Management | ⬜ Not Yet Discussed |
| S18 | Security Incident Procedures | ✅ Discussed |
| S19 | Data Backup Plan | ◐ Partially Discussed |
| S20 | Disaster Recovery Plan | ✅ Discussed |
| S21 | Emergency Mode Operations | ⬜ Not Yet Discussed |
| S22 | Contingency Testing / Revision | ⬜ Not Yet Discussed |
| S23 | Application / Data Criticality | ⬜ Not Yet Discussed |
| S24 | Periodic Evaluation | ✅ Discussed |
| S25 | BA Assurances / Contracts | ⬜ Not Yet Discussed |
| S26 | Emergency Facility Access | ⬜ Not Yet Discussed |
| S27 | Facility Security Plan | ⬜ Not Yet Discussed |
| S28 | Physical Access Validation | ⬜ Not Yet Discussed |
| S29 | Facility Maintenance Records | ⬜ Not Yet Discussed |
| S30 | Workstation Use | ⬜ Not Yet Discussed |
| S31 | Workstation Security | ⬜ Not Yet Discussed |
| S32 | Device / Media Disposal | ⬜ Not Yet Discussed |
| S33 | Media Reuse | ⬜ Not Yet Discussed |
| S34 | Media Accountability | ⬜ Not Yet Discussed |
| S35 | Backup Before Equipment Movement | ⬜ Not Yet Discussed |
| S36 | Authorized Technical Access | ✅ Discussed |
| S37 | Unique User Identification | ◐ Partially Discussed |
| S38 | Emergency Access Procedure | ⬜ Not Yet Discussed |
| S39 | Automatic Logoff | ⬜ Not Yet Discussed |
| S40 | Encryption / Decryption | ⬜ Not Yet Discussed |
| S41 | Audit Controls | ✅ Discussed |
| S42 | Improper Alteration / Destruction Protection | ⬜ Not Yet Discussed |
| S43 | ePHI Integrity Authentication | ⬜ Not Yet Discussed |
| S44 | Person / Entity Authentication | ⬜ Not Yet Discussed |
| S45 | Transmission Security | ⬜ Not Yet Discussed |
| S46 | Transmission Integrity | ⬜ Not Yet Discussed |
| S47 | Transmission Encryption | ⬜ Not Yet Discussed |
| S48 | BA / Subcontractor Arrangements | ⬜ Not Yet Discussed |
| S49 | Group Health Plan Safeguards | ⬜ Not Yet Discussed |
| S50 | Policies and Procedures | ⬜ Not Yet Discussed |
| S51 | Written / Electronic Documentation | ⬜ Not Yet Discussed |
| S52 | Documentation Retention | ⬜ Not Yet Discussed |
| S53 | Documentation Availability | ⬜ Not Yet Discussed |
| S54 | Documentation Updates | ⬜ Not Yet Discussed |

---

# Current Gap Mapping

| Gap | Requirements Currently Linked |
|---|---|
| **FAB-GAP-001 — PHI Classification Granularity** | S01, S11, S36; minimum-necessary requirements |
| **FAB-GAP-002 — Fragmented Authorization Across Fabric Security Planes** | S01, S07-S12, S36 |
| **FAB-GAP-003 — Audit Completeness, Attribution, and Data-Access Granularity** | S01, S05, S18, S37, S41 |
| **FAB-GAP-004 — Table-Level and Historical PHI Lineage** | S02, S23, S52, S54 |
| **FAB-GAP-005 — Native Fabric Governance Capability Dependency on Microsoft Purview** | Cross-cutting: S02, S11-S12, S41, S51-S54 |

---

# Next Requirement for Review

The next requirement in sequence that has **not** yet been explicitly reviewed is:

## S10 — Isolating Healthcare Clearinghouse Functions

**Citation:** 45 CFR §164.308(a)(4)(ii)(A)

**Source:**

[45 CFR §164.308 — Administrative Safeguards](https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-C/section-164.308)

**Review question:**

> Does Microsoft Fabric provide sufficient workspace, identity, data, and authorization boundaries to isolate healthcare clearinghouse ePHI from unrelated organizational users and workloads?