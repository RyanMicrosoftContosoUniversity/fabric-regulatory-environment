# Azure Databricks: hospital and research security/compliance assessment

**As of:** September 30, 2026.  
**Requirements:** `requirements-matrix.md`, all 60 IDs. Its definitions, applicability rules, and regulatory source register apply here.  
**Disposition:** **Conditional shortlist**, particularly for research engineering and custom ML. Not authorized for production PHI by this assessment.

## 1. Scope and overall finding

Evaluate Azure Databricks on commercial Azure with Unity Catalog, a Premium-capable architecture, the HIPAA compliance security profile, and the appropriate Enhanced Security and Compliance add-on. The HIPAA documentation requires enabling the profile and limits eligible preview features. An executed Microsoft BAA and actual service/region eligibility still require hospital review. [D01, D02]

The platform's principal strength is that governed data engineering and research development can share a consistent catalog and access-policy framework. Its principal risk is **configuration complexity across several security planes**: Databricks identity/catalog permissions, compute access modes, Azure storage permissions, classic/serverless networking, clients, and downstream systems. A secure workspace UI does not prove that its data is inaccessible through another path. [D03-D08]

**Analyst conclusion:** Strong candidate when the hospital can operate a disciplined Azure landing zone and data-platform security program. It is a weaker fit if the organization expects the purchase itself to create a turnkey consent system, research enclave, lossless audit record, or validated clinical application.

## 2. Strengths and limitations

| Dimension | Documented strength | Hospital implication / limitation |
|---|---|---|
| Regulated processing | HIPAA-specific controls and a hardened, monitored compliance-profile model. [D01, D02] | Enable the actual standard before PHI processing; do not assume all new services/previews are eligible. |
| Identity | Entra-backed SSO, account identity provisioning, OAuth, and distinct workspace/account/data authorization systems. [D03] | Design the entire identity lifecycle, not only browser MFA. Remove unused credential paths. |
| Governance | Unity Catalog row filters, masks, dynamic views, and centrally attached ABAC policy options. [D07] | Select supported compute/operations; separately prevent ungoverned storage access. |
| Research and ML | Notebook languages, MLflow, model/feature workflows, and distributed training capabilities. [D13] | Availability is not PHI eligibility or clinical validation. Benchmark the approved workload and runtime. |
| Networking | Distinct front-end, classic back-end, and serverless private connectivity. [D04, D05] | Inbound Private Link alone is not an outbound firewall or a guarantee about every service. |
| Delivery | Declarative Automation Bundles support versioned resource/code definitions and CI/CD. [D14] | Clinical/research validation and independent approval remain hospital processes. |
| Recovery | A documented cross-region recovery approach and a newer managed DR option. [D12, D16] | Managed DR is gated and has additional requirements; external-table/volume data is not replicated merely because metadata is. |

### Highest-risk design mistakes

Giving a researcher direct ADLS credentials or a sufficiently privileged storage role creates an alternate authority path. **Analyst inference:** catalog restrictions cannot be relied upon to constrain a separately authorized raw-storage read. Give researchers governed interfaces, not independent raw PHI storage access; test this explicitly. Catalog/workspace isolation and privilege design are critical. [D08]

Notebook results, SQL literals, model artifacts, embeddings, checkpoints, logs, and cached/client data belong in the PHI inventory where they contain sensitive information. The vendor specifically warns against sensitive information in customer-defined resource/metadata fields that can sit outside the compliance boundary. [D01]

The audit system table is explicitly **Public Preview** in the reviewed reference. Deleting a workspace removes its audit events older than 14 days from that table. This is not a statement that every independently delivered audit archive is deleted; it is a reason not to make the system table the hospital's only evidence repository. [D09]

Azure Monitor diagnostic settings do not contain every event/service in the diagnostic reference and do not include account-level events. The hospital must design an eligible multi-source collection strategy; simply turning on Azure diagnostics does not close all audit requirements. Protect any approved exported logs because they can contain sensitive information. [D10]

## 3. Complete requirements gap analysis

**M:** documented native capability; **C:** material conditions; **G:** documented baseline gap; **U:** insufficient evidence. None means deployment approval.

### Legal and research

| ID | Rating | Evidence / remaining gap | Required closure |
|---|---|---|---|
| L01 | C | Microsoft BAA relationship is described; the hospital agreement was not supplied. [D01, REG02] | Counsel verifies effective terms and related Azure services. |
| L02 | U | HIPAA feature/region restrictions exist; no complete approved service inventory supplied. [D01] | Obtain written eligibility for every selected compute, model, preview, and DR path. |
| L03 | U | Required scoped assurance artifacts were not reviewed. | Procurement/security reviews current reports, exceptions, and conditional authorizations. |
| L04 | C | HIPAA documentation assigns shared responsibilities. [D01] | Approve risk assessment and Databricks/Azure/hospital control RACI. |
| L05 | C | No supplied evidence of a deployed Part 2/sensitive-record workflow. [REG03] | Legal defines applicable restrictions; encode and test dataset/purpose policies. |
| L06 | C | Catalog authorization is not an IRB or research-agreement system. [D08, REG06] | Tie researcher groups, study status, and permitted purposes to approved records. |
| L07 | C | Platform capability is not validated Part 11 application evidence. [REG05] | Validate the actual regulated workflow; integrate controlled signatures where required. |
| L08 | C | Compliance-profile documentation describes identity/attribute locations beyond a single workspace region. [D02] | Map metadata, data, inference, support, and secondary-region processing. |
| L09 | U | Signed notification, audit-right, and supplier terms were unavailable. | Counsel verifies contractual obligations and operational escalation. |
| L10 | C | No hospital retention/hold/deletion implementation assessed. | Define primary, historical, model, log, backup, and research-record schedules. |

### Identity and study boundaries

| ID | Rating | Evidence / remaining gap | Required closure |
|---|---|---|---|
| I01 | M | Entra SSO and MFA integration are documented. [D03] | Test clients, guests, emergency access, and fallback settings. |
| I02 | C | OAuth is recommended; PATs and other supported credential paths require management. [D03] | Prefer short-lived identities; restrict/revoke legacy tokens. |
| I03 | C | Powerful catalog ownership/MANAGE and administrative roles require careful assignment. [D08] | Separate policy, platform, researcher, and audit duties. |
| I04 | C | Unity Catalog isolation is a design/configuration task. [D08] | Bind the approved catalog/workspace boundaries; deny cross-study enumeration and reads. |
| I05 | C | Filters/masks govern supported query paths, not an independent raw-storage entitlement. [D07, D08] | Test all engines/access modes; remove alternate storage authority. |
| I06 | C | Service-principal production jobs and controlled ownership are recommended. [D08] | Researchers cannot modify ingest policies or protected source tables. |
| I07 | C | Administrative roles exist; hospital elevation/support controls unverified. [D03] | Implement just-in-time elevation and independent review. |
| I08 | C | Provisioning is supported; study expiry/session behavior not demonstrated. [D03] | Test group changes, active sessions, tokens, and collaborator termination. |

### Network, encryption, and secrets

| ID | Rating | Evidence / remaining gap | Required closure |
|---|---|---|---|
| N01 | C | Separate inbound/back-end/serverless connectivity controls are documented. [D04] | Disable prohibited public paths; test UI/API/SQL/storage and account scope. |
| N02 | C | Serverless egress policy and NCC/private connectivity are distinct controls. [D05, D15] | Apply approved policies to both classic and serverless workloads; test exfiltration destinations. |
| N03 | C | Private connectivity supports customer-resource access; connector workflow unverified. [D04, D05] | Validate hospital DNS, routing, connector identities, and outages. |
| N04 | M | Encryption options and compliance-profile transport protections are documented. [D02, D06] | Verify independent ADLS, client, integration, and certificate boundaries. |
| N05 | C | CMK options have different coverage for managed services, storage, and compute. [D06] | Produce an artifact-by-artifact coverage map, including query history and exports. |
| N06 | C | Customer key operations add hospital availability responsibilities. [D06] | Rehearse rotation, revoked-key recovery, and independently protected backups. |
| N07 | C | Vendor warns about sensitive customer-defined metadata. [D01] | Scan resource names, repos, notebook outputs, secrets, and diagnostics. |
| N08 | C | Isolation can combine several private-link controls; some newer services have separate eligibility. [D04] | Prove required compute, BI, delivery, monitoring, and AI together. |

### Governance and research workflows

| ID | Rating | Evidence / remaining gap | Required closure |
|---|---|---|---|
| G01 | C | Governance primitives exist; complete clinical/unstructured classification not established. [D07, D08] | Validate inventory, tags, custodians, and false negatives with hospital samples. |
| G02 | C | Automatic column lineage and external-lineage registration are documented. [D11] | Preserve historical dataset/code/policy/consent versions for released analyses. |
| G03 | C | Query policies can restrict access, but consent is hospital business logic. [D07] | Implement study/purpose eligibility and authorized withdrawal handling across derived data. |
| G04 | C | Masking is not evidence of HIPAA de-identification. [D07, REG04] | Validate approved release transformations and re-identification risk. |
| G05 | C | A governed sharing design must be specified; no recipient agreement reviewed. [D08] | Approve recipients, protocol/access mode, replication, and revocation. |
| G06 | C | Research compute alone does not constrain endpoint/download/output behavior. | Add managed researcher access, egress controls, restricted packages, and output approval. |
| G07 | C | ML capability exists; PHI feature eligibility is separately restricted. [D01, D13] | Approve model/provider/tool/trace paths and enforce retrieval authorization. |
| G08 | C | Lake access/governance boundaries require deliberate design. [D08] | Reproduce protections when data leaves Databricks or is read externally. |
| G09 | C | Downstream BI/client copies are outside the catalog's sole authority. | Apply client/export controls, receiving-system policies, and copy retention. |
| G10 | C | General engineering capability does not prove the required clinical semantics. [D13] | Validate selected FHIR/HL7/OMOP/DICOM transformations and terminology. |
| G11 | C | Model development features are not clinical intended-use approval. [D13, REG05] | Separate research from care deployment and obtain clinical/model validation. |

### Audit and incident evidence

| ID | Rating | Evidence / remaining gap | Required closure |
|---|---|---|---|
| A01 | C | Audit and query-history sources cover defined events/workloads. [D09, D10, D17] | Correlate Databricks, storage, identity, clients, sharing, and BI activity. |
| A02 | C | Audit schema includes requesting/running identity metadata. [D09] | Demonstrate actual human-to-job-to-storage attribution and stable correlation. |
| A03 | U | No reviewed guarantee proves lossless audit across all proposed PHI paths. | Obtain scoped guarantees and load/failure reconciliation; otherwise redesign or reassess policy. |
| A04 | C | Current privileges plus activity do not automatically reconstruct all historical effective access. [D08, D10] | Archive group/grant/ownership/policy snapshots and change streams. |
| A05 | C | Preview system-table evidence has workspace-deletion implications. [D09] | Retain independent immutable archives and collector-health evidence. |
| A06 | C | Query history and diagnostic parameters can expose sensitive information. [D09, D17] | Restrict log readers and avoid PHI literals/unnecessary outputs. |
| A07 | C | SOC rules and resilient collection are hospital integrations. [D10] | Alert independently on grants, exports, audit changes, and collection failures. |
| A08 | C | Event sources support investigation, not a complete breach-response program. [D09, D10] | Rehearse containment, evidence preservation, scope, and counsel notification. |
| A09 | C | System-table access and masked parameters have role-dependent visibility. [D09, D17] | Approve a collector/read model that supplies sufficient evidence without excess PHI. |

### Resilience and delivery

| ID | Rating | Evidence / remaining gap | Required closure |
|---|---|---|---|
| R01 | C | DR and storage protection are not a tested ransomware recovery plan. [D12] | Restore data, code, grants, keys, and identities from protected recovery assets. |
| R02 | C | Managed DR is gated; external-table/volume data replication remains separate. [D12, D16] | Validate purchased support, selected assets, secondary networks, and measured full recovery. |
| R03 | C | Required hospital-vs-research contention behavior not benchmarked. | Separate appropriate compute/workloads; test concurrency, quotas, and failover capacity. |
| R04 | C | Bundles support software-engineering/CI/CD practices. [D14] | Add approved promotion, policy tests, validation artifacts, and rollback. |
| R05 | C | Compliance profile adds hardened images, updates, and monitoring. [D02] | Maintain hospital-owned packages, clients, agents, and supported runtimes. |
| R06 | C | No source-to-analysis reconciliation or clinical quality evidence supplied. | Validate patient/study identifiers, transformations, units, and analytical results. |
| R07 | C | External storage and metadata complicate complete exit/deletion scope. [D06, D08] | Test export, reauthorization, retention exceptions, and contractual termination. |

### Commercial and operations

| ID | Rating | Evidence / remaining gap | Required closure |
|---|---|---|---|
| O01 | C | Compliance, private networking, storage/compute, and managed DR add cost. [D02, D04-D06, D16] | Model fully loaded costs, including security staff and independent evidence storage. |
| O02 | C | No demonstrated safe budget policy across classic/serverless/AI. | Implement workload-specific alerts/limits without disrupting required hospital services. |
| O03 | C | Governed identity/catalogs and bundles enable repeatable designs. [D08, D14] | Model studies, environments, ownership, isolation, and retirement automation. |
| O04 | C | Feature maturity, region eligibility, and support vary. [D01, D04, D16] | Freeze the evaluated feature inventory and revalidate changes. |
| O05 | C | Multiple authorization/network planes create an operating burden. [D03-D08] | Fund Azure, catalog, compute, identity, SOC, and research-engineering ownership. |
| O06 | C | Required plans/add-ons and central Azure controls must be available. [D02, D04, D16] | Obtain license, network, key, identity, and collector approvals. |
| O07 | C | No independent hospital workload comparison was run. | Benchmark approved controls, workload mix, throughput, concurrency, recovery, and cost. |

## 4. Material gap register and mitigations

Risk severity below is an **analyst prioritization of a possible design failure**, not a discovered exploit in a hospital installation.

| Risk | Severity | Requirements | Mitigation and accountable owner | Closure evidence |
|---|---|---|---|---|
| PHI in an unapproved feature, region, or contract scope | Critical approval blocker | L01-L02, L08 | Legal/procurement and platform owner approve a versioned service inventory; block unapproved services. | Executed terms, scoped eligibility confirmation, deployment policy. |
| Raw-storage authority bypasses researcher catalog restrictions | High | I03-I05, N02 | Azure security removes researcher raw credentials/roles; data security controls catalog boundaries. | SQL/notebook/direct-storage negative-test results. |
| Notebook/job/API egress or researcher endpoint leaks PHI | High | N01-N02, G06, G09 | Network/endpoint teams implement and test private ingress, egress allowlists, and approved output release. | Denied exfiltration tests and approved researcher workflow. |
| Evidence repository is incomplete or destroyed with operational assets | High | A01, A03, A05 | SOC/records owners define independent collection, reconciliation, immutability, and retention. | Access-suite reconciliation and privileged-deletion recovery evidence. |
| Consent/study restrictions do not reach copies or ML derivatives | High | L05-L06, G03-G07 | Research governance defines enforceable use rules and disposition for derived artifacts. | Expiry/withdrawal and release-test evidence. |
| Regional failover restores metadata but not a usable end-to-end workflow | High | R01-R02 | Resilience owner designs recovery for external storage, identity, integrations, and clients as well as Databricks. | Timed full failover/failback and restore exercise. |
| Costs or staffing undermine continuous control operation | Medium | O01-O06 | Platform/FinOps owners fund required tiers, networking, evidence, and operating capacity. | Costed operating model and support rota. |

**Native limitations versus remediable gaps:** There is no evidence here of a universal Databricks incapability preventing regulated healthcare use. Most C rows are implementation/operating requirements. The U rows are serious evidence gaps: particularly contract scope, required assurance, and lossless auditing. A proof-of-concept can test observed behavior but cannot manufacture a contractual guarantee.

## 5. Recommended deployment conditions

Use separate approved study/environment boundaries, tightly controlled production writers, centralized account identities, governed PHI interfaces, and independent evidence storage. Start with the smallest approved compute/feature inventory rather than enabling every research convenience. These are proposed hospital design requirements, not statements about an inspected deployment.

For managed DR, the reviewed documentation requires gated enrollment, Premium workspaces, Mission Critical add-ons, and serverless in both regions. It replicates selected managed Delta data and workspace assets, but external table/volume data needs its own recovery design. Validate HIPAA eligibility and the excluded-resource list before counting this service toward RPO/RTO. [D16]

**Choose conditionally** when custom research/ML and engineering flexibility are important and the institution can sustain the control program. **Do not approve yet** if any applicable P0/P0* remains unresolved, if researchers retain alternate raw-storage access, or if required evidence/recovery depends solely on unverified behavior.

## 6. Primary source register

Reviewed September 30, 2026. Regulatory source IDs use the REG prefix and resolve in `requirements-matrix.md`. No source is treated as a hospital deployment attestation.

| ID | Official documentation | Source location |
|---|---|---|
| D01 | HIPAA; feature/regional restrictions and responsibilities | `https://learn.microsoft.com/en-us/azure/databricks/security/privacy/hipaa` |
| D02 | Compliance security profile; add-on and metadata boundaries | `https://learn.microsoft.com/en-us/azure/databricks/security/privacy/security-profile` |
| D03 | Authentication and access control | `https://learn.microsoft.com/en-us/azure/databricks/security/auth/` |
| D04 | Private Link concepts | `https://learn.microsoft.com/en-us/azure/databricks/security/network/concepts/privatelink-concepts` |
| D05 | Serverless compute plane networking | `https://learn.microsoft.com/en-us/azure/databricks/security/network/serverless-network-security/` |
| D06 | Customer-managed keys | `https://learn.microsoft.com/en-us/azure/databricks/security/keys/customer-managed-keys` |
| D07 | Row filters and column masks; ABAC comparison | `https://learn.microsoft.com/en-us/azure/databricks/data-governance/unity-catalog/filters-and-masks/` |
| D08 | Unity Catalog best practices | `https://learn.microsoft.com/en-us/azure/databricks/data-governance/unity-catalog/best-practices` |
| D09 | Audit log system table reference | `https://learn.microsoft.com/en-us/azure/databricks/admin/system-tables/audit-logs` |
| D10 | Diagnostic log reference | `https://learn.microsoft.com/en-us/azure/databricks/admin/account-settings/audit-logs` |
| D11 | Lineage in Unity Catalog | `https://learn.microsoft.com/en-us/azure/databricks/data-governance/unity-catalog/data-lineage` |
| D12 | Disaster recovery | `https://learn.microsoft.com/en-us/azure/databricks/admin/disaster-recovery` |
| D13 | Machine learning on Azure Databricks | `https://learn.microsoft.com/en-us/azure/databricks/machine-learning/` |
| D14 | Declarative Automation Bundles | `https://learn.microsoft.com/en-us/azure/databricks/dev-tools/bundles/` |
| D15 | Serverless egress control | `https://learn.microsoft.com/en-us/azure/databricks/security/network/serverless-network-security/network-policies` |
| D16 | Managed disaster recovery | `https://learn.microsoft.com/en-us/azure/databricks/admin/managed-disaster-recovery` |
| D17 | Query history system table reference | `https://learn.microsoft.com/en-us/azure/databricks/admin/system-tables/query-history` |

**Evidence limitations:** Public documentation establishes capabilities and restrictions, not the hospital's signed BAA, required third-party reports, actual configuration, contractual audit guarantees, measured recovery, or clinical/research validation. Recheck the precise feature and regional support tables at procurement and before changes.
