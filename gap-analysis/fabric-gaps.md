# Microsoft Fabric Regulatory Capability Gap Register

## Assessment Scope

This register documents Microsoft Fabric platform capability and assurance gaps identified when evaluating Fabric as a data platform for HIPAA-regulated workloads.

A gap does **not** automatically constitute HIPAA noncompliance.

HIPAA generally defines required security and privacy outcomes rather than prescribing specific Microsoft Fabric features such as sensitivity labels, OneLake security, Purview, automated lineage, or a particular auditing implementation.

The purpose of this register is to identify areas where Fabric does not natively provide the level of control, granularity, evidence, consistency, or integration desired to demonstrate those regulatory outcomes efficiently.

---

# FAB-GAP-001 — PHI Classification Granularity

## Related HIPAA Requirements

### 45 CFR §164.308(a)(4) — Information Access Management

Requires policies and procedures for authorizing access to electronic protected health information (ePHI).

Source:

[45 CFR §164.308 — Administrative Safeguards](https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-C/section-164.308)

### 45 CFR §164.312(a)(1) — Access Control

Requires technical policies and procedures that allow access only to persons or software programs that have been granted access rights.

Source:

[45 CFR §164.312 — Technical Safeguards](https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-C/section-164.312)

### 45 CFR §164.502(b) and §164.514(d) — Minimum Necessary

Requires covered entities, where applicable, to limit PHI uses, disclosures, and requests to the minimum necessary to accomplish the intended purpose.

Source:

[45 CFR §164.514 — Other Requirements Relating to Uses and Disclosures of PHI](https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-E/section-164.514)

## Current Fabric Capability

Microsoft Fabric provides sensitivity labels and related Microsoft Purview information-protection capabilities for supported Fabric items.

This enables a Lakehouse, Warehouse, semantic model, or other supported Fabric item to be classified as containing sensitive information.

Fabric also provides access-control mechanisms at multiple layers, including workspace permissions, item permissions, OneLake security, SQL security, and semantic-model security.

## Gap Statement

The assessed Fabric governance model does not provide equivalent native sensitivity-label granularity at the individual table level within a Lakehouse or Warehouse.

A Fabric item may contain both PHI and non-PHI tables while the primary sensitivity classification is associated with the containing Fabric item rather than each individual table.

For example:

```text
Lakehouse
├── Patient_Demographics       <- PHI
├── Patient_Diagnoses          <- PHI
├── Reference_Country          <- Non-PHI
└── Reference_Diagnosis_Code   <- Non-PHI
```

The desired governance model would allow the first two tables to be explicitly classified as PHI without requiring the entire Lakehouse to carry the same classification.

Sensitivity labels are not themselves mandated by HIPAA.

The capability gap concerns the granularity of the classification mechanism available to governance and compliance teams to support:

- PHI inventory
- information access management
- minimum-necessary policies
- DLP policies
- governance reporting
- access reviews
- regulatory evidence
- lineage analysis

## Risk / Impact

Organizations may need to treat an entire Lakehouse or Warehouse as PHI-bearing when only a subset of its tables contain PHI.

This can result in:

- overly broad security restrictions;
- reduced classification accuracy;
- unnecessary segregation of PHI and non-PHI data;
- additional Lakehouse or Warehouse proliferation;
- additional workspace proliferation;
- more complicated lifecycle management;
- less precise DLP and governance policies;
- less precise regulatory reporting.

The absence of table-level classification may also make it harder to create automated relationships between:

```text
PHI Classification
      |
      v
Access Policy
      |
      v
Security Group
      |
      v
OneLake Security Role
      |
      v
Approved Data Access
```

## Desired State

Fabric should provide native table-level sensitivity classification for Lakehouse and Warehouse tables.

Table classifications should be consumable by:

- Fabric governance experiences;
- Microsoft Purview;
- OneLake security;
- DLP policies;
- information-protection policies;
- data catalogs;
- access reviews;
- lineage;
- regulatory reporting;
- APIs used for automated governance.

## Closure Criteria

This gap can be considered closed when Fabric supports table-level PHI classification with sufficient integration into governance, security, DLP, discovery, access-management, lineage, and compliance workflows.

---

# FAB-GAP-002 — Fragmented Authorization Across Fabric Security Planes

## Related HIPAA Requirements

### 45 CFR §164.308(a)(4) — Information Access Management

Requires policies and procedures for authorizing access to ePHI.

Source:

[45 CFR §164.308 — Administrative Safeguards](https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-C/section-164.308)

### 45 CFR §164.312(a)(1) — Access Control

Requires technical policies and procedures that allow access only to appropriately authorized persons or software programs.

Source:

[45 CFR §164.312 — Technical Safeguards](https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-C/section-164.312)

### 45 CFR §164.514(d) — Minimum Necessary

Supports limiting access to the information required for an authorized purpose.

Source:

[45 CFR §164.514 — Minimum Necessary](https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-E/section-164.514)

## Current Fabric Capability

Fabric provides multiple mechanisms for implementing authorization.

These include:

- Workspace RBAC
- Item-level permissions
- OneLake security roles
- OneLake row-level security
- OneLake column-level security
- SQL permissions
- SQL row-level security
- Semantic-model RLS
- Semantic-model OLS
- Workspace identities
- Service principals
- Connection identities
- Entra ID groups

These capabilities are sufficient to construct a strong PHI authorization architecture.

A possible organizational access-management pattern is:

```text
PHI Table
   |
   v
OneLake Security Role
   |
   v
Entra ID Security Group
   |
   v
Approved Access Workflow
   |
   v
ServiceNow / Access Governance Process
   |
   v
Authorized User
```

This provides a viable mechanism for implementing controlled PHI access.

## Gap Statement

Authorization is distributed across multiple Fabric security planes.

There is no single authorization policy that automatically follows governed data through every Fabric execution and consumption path.

Effective authorization can differ depending on whether data is accessed through:

- Workspace permissions
- OneLake
- SQL analytics endpoints
- Fabric Warehouse
- Spark
- Direct Lake
- semantic models
- shortcuts
- APIs
- external engines
- workload identities
- connection identities

Conceptually:

```text
User
  |
  +--> Workspace RBAC
  |
  +--> Item Permissions
  |
  +--> OneLake Security
  |
  +--> SQL Permissions
  |
  +--> Semantic Model RLS / OLS
  |
  +--> Direct Lake Identity
  |
  +--> Connection Identity
  |
  +--> Workload Identity
```

Identity mode can also affect which authorization layer performs enforcement.

For example, an SQL analytics endpoint using delegated identity and an endpoint using end-user identity can have different authorization behavior at the OneLake layer.

A restriction demonstrated at one layer therefore does not automatically prove that the same restriction exists through every other permitted data-access path.

## Risk / Impact

A user may be correctly restricted through one interface while retaining broader access through another independently authorized interface.

For example:

```text
Power BI RLS
     |
     | correctly restricts Study A
     v
Semantic Model

BUT

User also has broader OneLake or SQL access
     |
     v
Raw PHI
```

The issue is not an absence of Fabric access controls.

The gap is the additional assurance burden required to prove that controls across multiple security planes collectively create the intended authorization boundary.

This increases the complexity of:

- access certification;
- security testing;
- role design;
- study isolation;
- investigation;
- least-privilege validation;
- regulatory evidence.

## Desired State

Fabric should provide a unified data-authorization model in which a policy associated with governed data can be consistently enforced across supported Fabric engines and consumption paths.

Conceptually:

```text
Governed Data Policy
        |
        +--> SQL
        +--> Spark
        +--> OneLake
        +--> Direct Lake
        +--> Semantic Models
        +--> APIs
        +--> Shortcuts
        +--> External Engines
```

A user granted access through an approved governance workflow should receive the intended data entitlement regardless of which supported Fabric engine is used.

## Closure Criteria

Until a unified authorization model exists, closure requires documented positive and negative access testing for every supported PHI access path.

Testing should include, where applicable:

- Workspace access
- Item permissions
- OneLake access
- SQL
- Spark
- Direct Lake
- semantic models
- shortcuts
- APIs
- external-engine access
- workload identities
- connection identities

---

# FAB-GAP-003 — Audit Completeness, Attribution, and Data-Access Granularity

## Related HIPAA Requirements

### 45 CFR §164.312(b) — Audit Controls

Requires hardware, software, and/or procedural mechanisms that record and examine activity in information systems containing or using ePHI.

Source:

[45 CFR §164.312 — Technical Safeguards](https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-C/section-164.312)

### 45 CFR §164.308(a)(1)(ii)(D) — Information System Activity Review

Requires procedures to regularly review records of information-system activity, such as:

- audit logs;
- access reports;
- security incident tracking reports.

Source:

[45 CFR §164.308 — Administrative Safeguards](https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-C/section-164.308)

### 45 CFR §164.308(a)(6) — Security Incident Procedures

Requires policies and procedures for identifying, responding to, mitigating, and documenting security incidents.

Source:

[45 CFR §164.308 — Administrative Safeguards](https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-C/section-164.308)

## Current Fabric Capability

Fabric provides several audit and telemetry mechanisms.

These include:

- Fabric activity events
- Microsoft Purview Audit
- SQL auditing
- OneLake diagnostics
- semantic-model telemetry
- workload-specific telemetry
- Entra ID logs
- workspace monitoring

Together these provide meaningful evidence for activity review and security investigations.

## Gap Statement

No single Fabric audit source provides a complete and consistently attributable record of PHI access across every Fabric execution path.

Specific limitations include:

1. SQL auditing must be explicitly enabled and configured.

2. SQL auditing is documented as best-effort and may omit configured events under high activity or network pressure.

3. Direct Lake and other OneLake-based reads do not necessarily issue SQL queries.

4. SQL auditing therefore cannot serve as the complete audit source for all Fabric data access.

5. Different Fabric workloads generate different forms of audit evidence.

6. Delegated execution can result in different identities appearing at different layers.

For example:

```text
Human User
    |
    v
Semantic Model
    |
    v
Query / Workload Identity
    |
    v
Storage Identity
    |
    v
OneLake
```

Security investigators may therefore need to correlate several records to determine who actually initiated an action.

7. Object-access evidence does not necessarily identify every individual patient record returned by a query.

8. Fabric does not currently provide one native audit surface that universally answers:

```text
Who?
Did what?
Through which engine?
Using which effective identity?
Against which PHI object?
When?
With what result?
```

## HIPAA Qualification

HIPAA does **not** explicitly require universal row-level or cell-level logging of every PHI read.

The gap should therefore **not** be characterized as:

> Fabric cannot meet HIPAA because it does not audit every row.

The more accurate gap statement is:

> Fabric does not currently provide a single unified audit plane that guarantees complete, consistently attributable evidence across all PHI access mechanisms.

## Risk / Impact

Security and compliance teams may need to correlate multiple telemetry sources:

```text
Entra Logs
    +
Fabric Activity
    +
SQL Audit
    +
OneLake Diagnostics
    +
Semantic Model Logs
    +
Application Logs
```

This increases:

- investigation complexity;
- evidence reconstruction effort;
- dependency on correlation identifiers;
- risk of incomplete audit reconstruction;
- difficulty proving complete audit coverage;
- SOC operational complexity.

This gap also affects security incident procedures because investigation quality depends on the completeness and attribution of available evidence.

## Desired State

Fabric should provide a unified audit plane capable of consistently correlating:

```text
Initiating User
      |
      v
Effective Query Identity
      |
      v
Workload / Service Identity
      |
      v
Storage Identity
      |
      v
Data Object
      |
      v
Action
      |
      v
Result
```

across supported Fabric data-access paths.

The audit platform should allow investigators to move from a high-level user activity event to the relevant workload and data-access evidence without manually stitching together unrelated telemetry systems.

## Closure Criteria

Until a unified audit plane exists, closure requires:

- multi-source audit collection;
- correlation between audit planes;
- independent retention of required evidence;
- monitoring of audit-collector health;
- explicit testing of every supported PHI access path;
- documented understanding of known evidence gaps;
- protection of logs that may themselves contain PHI;
- retention aligned to organizational and regulatory requirements.

---

# FAB-GAP-004 — Table-Level and Historical PHI Lineage

## Related HIPAA Requirements

There is no HIPAA requirement stating that a data platform must provide an automated lineage visualization.

Lineage is instead an important technical capability supporting several HIPAA obligations.

### 45 CFR §164.308(a)(1)(ii)(A) — Risk Analysis

Requires an accurate and thorough assessment of potential risks and vulnerabilities to the confidentiality, integrity, and availability of ePHI.

Source:

[45 CFR §164.308 — Risk Analysis](https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-C/section-164.308)

Understanding where PHI resides, where it moves, and where it is processed is necessary input into that analysis.

### 45 CFR §164.308(a)(4) — Information Access Management

Requires controls around access to ePHI.

Source:

[45 CFR §164.308 — Information Access Management](https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-C/section-164.308)

Understanding PHI lineage assists in identifying every location requiring an access-control decision.

### 45 CFR §164.316(b) — Documentation

Requires applicable Security Rule documentation to be maintained and updated.

Source:

[45 CFR §164.316 — Policies, Procedures, and Documentation](https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-C/section-164.316)

## Current Fabric Capability

Fabric provides native lineage for supported relationships between Fabric items.

For example:

```text
Pipeline
   |
   v
Lakehouse
   |
   v
Semantic Model
   |
   v
Power BI Report
```

Microsoft Purview can also discover more granular metadata for supported structured data sources, including individual tables in applicable scenarios.

However:

> Metadata discovery and lineage are separate capabilities.

Knowing that a table exists does not necessarily mean the platform can display its complete upstream and downstream lineage.

## Gap Statement

Fabric does not currently establish a complete native capability for tracing an individual PHI-bearing table through its complete upstream and downstream lifecycle.

The desired lineage capability would allow an assessor to select a specific table such as:

```text
Patient_Diagnosis
```

and see a complete path such as:

```text
Epic / EHR
    |
    v
Ingestion Pipeline
    |
    v
Bronze.Patient_Diagnosis
    |
    v
Transformation Notebook
    |
    v
Silver.Patient_Diagnosis
    |
    v
Warehouse.Patient_Diagnosis
    |
    v
Semantic Model
    |
    v
Clinical Report
```

Native Fabric lineage is stronger at the Fabric-item level than at this sub-item/table level.

## Historical Lineage Gap

The second component is historical lineage.

A regulatory, privacy, or security investigation may need to answer:

> Where did this PHI table originate, and where was it consumed on a particular date six months ago?

A current lineage graph does not necessarily reconstruct the historical state of:

- pipelines;
- notebook transformations;
- table dependencies;
- downstream consumers;
- semantic models;
- security policies;
- data classifications.

Therefore, the desired capability is not merely table-level lineage.

It is:

> **Versioned, historical, table-level lineage.**

## Purview Dependency

Microsoft Purview can provide richer metadata discovery for supported data sources.

However, the ability to catalog or scan a table should not be interpreted as proof that complete table-level or sub-item lineage exists for that table.

For example:

```text
Purview knows:
    Table X exists
    Table X contains certain columns
    Table X has classification metadata

But that does not automatically mean:

    Source A
       |
       v
    Pipeline B
       |
       v
    Notebook C
       |
       v
    Table X
       |
       v
    Semantic Model D
       |
       v
    Report E
```

Where Purview supplies metadata or governance functionality that Fabric does not yet expose natively, that dependency should be explicitly documented as part of the architecture.

## Risk / Impact

A healthcare organization performing risk analysis or responding to a regulatory inquiry may be able to identify the Lakehouse containing PHI but still require manual investigation to determine:

- where a specific PHI table originated;
- every transformation it passed through;
- all downstream datasets;
- all downstream semantic models;
- all downstream reports;
- external destinations;
- historical versions of those relationships.

This increases the effort needed for:

- risk assessments;
- impact analysis;
- incident investigation;
- data-access reviews;
- change management;
- regulatory evidence;
- research reproducibility.

## Desired State

Fabric should provide:

1. Native table-level lineage for Lakehouse and Warehouse tables.
2. End-to-end lineage across ingestion, transformation, storage, semantic, and consumption layers.
3. Historical and versioned lineage.
4. Integration between lineage and PHI sensitivity classification.
5. Programmatic lineage retrieval through supported APIs.
6. Ability to identify downstream external consumers where supported.

The expected baseline is table-level lineage.

Universal row-level lineage is not proposed as a baseline healthcare regulatory requirement.

## Closure Criteria

The gap can be considered closed when an organization can select a PHI-bearing table and reliably reconstruct:

```text
Upstream Sources
      +
Transformations
      +
Intermediate Tables
      +
Downstream Tables
      +
Semantic Models
      +
Reports / Consumers
```

including the ability to reconstruct the relevant lineage historically.

---

# FAB-GAP-005 — Native Fabric Governance Capability Dependency on Microsoft Purview

## Related HIPAA Requirements

This is a cross-cutting architectural dependency rather than a requirement derived from a single HIPAA provision.

Relevant HIPAA requirements include the following.

### 45 CFR §164.308(a)(2) — Assigned Security Responsibility

Source:

[45 CFR §164.308 — Administrative Safeguards](https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-C/section-164.308)

### 45 CFR §164.308(a)(4) — Information Access Management

Source:

[45 CFR §164.308 — Administrative Safeguards](https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-C/section-164.308)

### 45 CFR §164.312(a) — Access Control

Source:

[45 CFR §164.312 — Technical Safeguards](https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-C/section-164.312)

### 45 CFR §164.312(b) — Audit Controls

Source:

[45 CFR §164.312 — Technical Safeguards](https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-C/section-164.312)

### 45 CFR §164.316 — Policies, Procedures, and Documentation

Source:

[45 CFR §164.316 — Policies, Procedures, and Documentation](https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-C/section-164.316)

## Current State

Several capabilities important to a regulated Microsoft Fabric architecture currently involve Microsoft Purview.

These can include:

- sensitivity classification;
- information protection;
- DLP;
- audit;
- metadata discovery;
- governance;
- catalog capabilities;
- regulatory reporting inputs.

Fabric increasingly exposes governance capabilities through Fabric and OneLake experiences.

However, Fabric-native capability and Purview capability should not be assumed to be equivalent.

## Gap Statement

Where a regulatory control depends on functionality that is currently supplied by Microsoft Purview but is not available natively in Fabric at the required granularity, the Fabric solution remains dependent on a separate governance control plane.

For example:

```text
Required Capability
      |
      v
Table Metadata Discovery
      |
      v
Available through Purview
      |
      X
Equivalent Fabric-native capability not available
```

This should be represented as a current Fabric capability dependency.

A capability should not be credited as a Fabric-native control merely because:

- an equivalent capability exists in Purview;
- Fabric displays some Purview information;
- Fabric integrates with Purview;
- a capability is expected to become more deeply integrated into Fabric in the future.

For regulatory architecture purposes, the relevant question is:

> What product or control plane actually provides the required capability today?

## Regulatory Relevance

Each control should be mapped explicitly.

For example:

```text
HIPAA Requirement
      |
      v
Required Capability
      |
      v
Implementing Product
      |
      +--> Fabric
      |
      +--> Purview
      |
      +--> Entra
      |
      +--> External System
```

This allows a regulator, auditor, or architecture reviewer to understand the actual control implementation rather than assuming that every Microsoft governance capability exists inside Fabric itself.

## Risk / Impact

Failing to identify these dependencies can result in an architecture that incorrectly assumes Fabric alone provides controls that actually depend upon:

- separate Purview services;
- additional licensing;
- tenant-level permissions;
- separate administrative roles;
- additional APIs;
- separate monitoring infrastructure;
- separate governance ownership.

This can create a discrepancy between the documented architecture and the actual control implementation.

It can also affect platform simplification decisions if an organization attempts to remove or reduce Purview usage before equivalent Fabric capability exists.

## Desired State

Fabric should progressively provide the governance capabilities necessary to operate a regulated Fabric environment without unnecessary fragmentation across multiple Microsoft control planes.

Where Purview remains the appropriate governance plane, Fabric should provide deep, consistent integration and explicit control ownership.

For every regulatory capability, the target architecture should be able to state:

```text
Requirement
    |
    v
Capability
    |
    v
Product Providing Capability
    |
    v
Required License
    |
    v
Required Role / Permission
    |
    v
Evidence Source
```

## Closure Criteria

Each regulatory capability should have an explicit control mapping such as:

| Requirement | Required Capability | Current Implementation | Fabric-Native | External Dependency |
|---|---|---|---|---|
| PHI classification | Identify sensitive data | Fabric / Purview | Partial | Purview |
| Table metadata | Discover PHI tables | Purview / Fabric catalog | Partial | Purview |
| Table lineage | Trace PHI movement | Fabric lineage / Purview metadata | Partial | Purview and/or manual evidence |
| Audit | Record and investigate activity | Fabric + Purview + Entra | Partial | Purview / Entra |
| DLP | Detect or restrict sensitive-data handling | Purview | Integrated | Purview |

A specific gap can be considered closed when either:

1. the required capability becomes available natively in Fabric at the necessary level; or
2. the Purview dependency is explicitly incorporated into the approved target architecture, including licensing, permissions, ownership, availability, integration, and evidence requirements.

---

# Current Fabric Gap Summary

| ID | Gap | Primary HIPAA Relationship | Primary Concern |
|---|---|---|---|
| FAB-GAP-001 | PHI Classification Granularity | §164.308(a)(4), §164.312(a), §164.514(d) | Sensitivity classification is not sufficiently granular at the table level |
| FAB-GAP-002 | Fragmented Authorization Across Fabric Security Planes | §164.308(a)(4), §164.312(a), §164.514(d) | Authorization is distributed across workspace, OneLake, SQL, semantic, and identity planes |
| FAB-GAP-003 | Audit Completeness, Attribution, and Data-Access Granularity | §164.312(b), §164.308(a)(1)(ii)(D), §164.308(a)(6) | No single unified audit source covers every PHI access path |
| FAB-GAP-004 | Table-Level and Historical PHI Lineage | §164.308(a)(1)(ii)(A), §164.308(a)(4), §164.316(b) | Native lineage does not provide complete historical table-level provenance |
| FAB-GAP-005 | Native Fabric Governance Capability Dependency on Microsoft Purview | §164.308, §164.312, §164.316 | Some regulatory governance capabilities remain dependent on separate Purview control planes |

---

# Requirements Reviewed Without a New Fabric Capability Gap

The following HIPAA requirements were also reviewed from a **platform-vendor capability perspective**.

No additional Fabric capability gap was identified for these requirements beyond the gaps already documented above.

## Risk Management

### HIPAA Requirement

**45 CFR §164.308(a)(1)(ii)(B) — Risk Management**

Source:

[45 CFR §164.308 — Administrative Safeguards](https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-C/section-164.308)

Fabric provides sufficient access-control mechanisms for an organization to implement approved risk mitigations.

A representative PHI access-governance implementation can use:

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

No additional Fabric capability gap was identified specifically for this requirement.

---

## Sanction Policy

### HIPAA Requirement

**45 CFR §164.308(a)(1)(ii)(C) — Sanction Policy**

Source:

[45 CFR §164.308 — Administrative Safeguards](https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-C/section-164.308)

This is primarily an organizational and operational responsibility.

Fabric can provide supporting evidence through audit and activity records, but the platform is not responsible for defining or imposing workforce disciplinary actions.

No Fabric capability gap was identified.

---

## Information System Activity Review

### HIPAA Requirement

**45 CFR §164.308(a)(1)(ii)(D) — Information System Activity Review**

Source:

[45 CFR §164.308 — Administrative Safeguards](https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-C/section-164.308)

Fabric provides activity and audit mechanisms supporting this requirement.

The limitations identified during assessment are already captured under:

**FAB-GAP-003 — Audit Completeness, Attribution, and Data-Access Granularity**

No separate duplicate gap is required.

---

## Assigned Security Responsibility

### HIPAA Requirement

**45 CFR §164.308(a)(2) — Assigned Security Responsibility**

Source:

[45 CFR §164.308 — Administrative Safeguards](https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-C/section-164.308)

This requires the healthcare organization to designate a security official.

This is an organizational responsibility rather than a Fabric product capability.

No Fabric capability gap was identified.

---

## Workforce Security

### HIPAA Requirement

**45 CFR §164.308(a)(3) — Workforce Security**

Source:

[45 CFR §164.308 — Administrative Safeguards](https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-C/section-164.308)

Fabric provides the technical mechanisms required to enforce workforce access decisions through:

- workspace RBAC;
- item permissions;
- OneLake security;
- SQL security;
- Entra ID groups;
- workload identities.

The organization remains responsible for determining who should receive access.

No new Fabric capability gap was identified.

---

## Information Access Management

### HIPAA Requirement

**45 CFR §164.308(a)(4) — Information Access Management**

Source:

[45 CFR §164.308 — Administrative Safeguards](https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-C/section-164.308)

Fabric provides sufficient technical access-control points to implement information-access management.

The complexity associated with coordinating those controls across multiple Fabric access planes is already captured under:

**FAB-GAP-002 — Fragmented Authorization Across Fabric Security Planes**

No additional gap is required.

---

## Security Awareness and Training

### HIPAA Requirement

**45 CFR §164.308(a)(5) — Security Awareness and Training**

Source:

[45 CFR §164.308 — Administrative Safeguards](https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-C/section-164.308)

This is primarily an organizational workforce responsibility.

Fabric is not expected to provide the organization's security-awareness training program.

No Fabric capability gap was identified.

---

## Security Incident Procedures

### HIPAA Requirement

**45 CFR §164.308(a)(6) — Security Incident Procedures**

Source:

[45 CFR §164.308 — Administrative Safeguards](https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-C/section-164.308)

Fabric provides logging, telemetry, diagnostics, and activity evidence that can support security-incident investigations.

The principal capability limitation is already captured under:

**FAB-GAP-003 — Audit Completeness, Attribution, and Data-Access Granularity**

No additional duplicate gap is required.

---

## Contingency Plan

### HIPAA Requirement

**45 CFR §164.308(a)(7) — Contingency Plan**

Source:

[45 CFR §164.308 — Administrative Safeguards](https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-C/section-164.308)

Fabric provides platform mechanisms that can support contingency planning.

These include:

- OneLake resiliency;
- capacity disaster recovery;
- asynchronous regional replication;
- item recovery mechanisms;
- Git integration for supported Fabric artifact definitions;
- APIs and infrastructure-as-code approaches for reconstructing configuration.

Fabric disaster recovery alone should not be treated as complete application recovery.

However, when combined with version-controlled artifact definitions and a documented reconstruction process, the platform provides the capabilities necessary to implement a complete contingency strategy.

No additional inherent Fabric capability gap was identified from the platform-vendor perspective.

---

## Periodic Evaluation

### HIPAA Requirement

**45 CFR §164.308(a)(8) — Evaluation**

Source:

[45 CFR §164.308 — Administrative Safeguards](https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-C/section-164.308)

The organization must periodically evaluate whether its security controls remain appropriate and effective.

Fabric exposes evidence that can support such evaluations, including:

- tenant settings;
- workspace configuration;
- item permissions;
- OneLake security configuration;
- SQL security;
- activity logs;
- audit logs;
- monitoring;
- APIs exposing configuration state.

Tenant settings alone demonstrate configuration rather than operating effectiveness.

However, the broader Fabric evidence ecosystem provides sufficient capability for an organization to perform a technical evaluation.

No new Fabric capability gap was identified.