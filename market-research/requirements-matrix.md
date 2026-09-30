# Healthcare data-platform requirements and selection matrix

**Assessment date:** September 30, 2026.  
**Perspective:** Independent security and compliance analysis for a US hospital, academic medical center, and research organization.  
**Companion assessments:** `azure-databricks-assessment.md`, `snowflake-assessment.md`, and `microsoft-fabric-assessment.md`.

## 1. Executive decision

**All three platforms merit consideration; none is approved for PHI by this desk review.** The decision must apply to an identified architecture, purchased edition, region, feature inventory, contract, and operating model, not a product brand.

The preliminary selection hypotheses are:

| Primary need | Candidate to investigate first | Reason and qualification |
|---|---|---|
| Complex research engineering, distributed processing, and custom ML | Azure Databricks | Its documented research/ML and lakehouse capabilities support this hypothesis. Prove compute eligibility, storage-policy enforcement, egress restrictions, and audit coverage. [D07, D08, D13] |
| Governed SQL analytics, a managed warehouse, and controlled multi-party collaboration | Snowflake on Azure | Documented policy controls and clean rooms support this hypothesis. Evaluate Business Critical costs, access-history coverage, external data paths, and individual AI/ML services. [S01, S04, S05, S15, S19] |
| Microsoft-centered analytics and Power BI delivery | Microsoft Fabric | Its integrated security/governance and BI model support this hypothesis. Workspace-private networking, artifact-specific CMK coverage, and audit completeness require particularly careful design. [F01-F04, F09-F11] |
| A trusted research environment with no uncontrolled raw-data export | No automatic winner | A clean room, notebook platform, or private endpoint alone does not satisfy the enclave requirement. Validate the entire researcher-to-output workflow. |
| EHR transaction processing, regulated e-signatures, or clinical device functionality | Do not substitute any of these platforms for the required clinical/validated system | Assess the specific application and intended use separately; analytical infrastructure is not a blanket clinical-system approval. [REG05] |

These are **analyst judgments**, not performance benchmarks or unconditional recommendations. Product source IDs resolve in the corresponding assessment's source register.

## 2. Scope and baseline architectures

The evaluation assumes identifiable clinical data, free-text notes, imaging metadata, claims, genomics, research cohorts, external collaborators, and operational BI. Not every dataset is subject to every regime.

| Platform | Evaluated baseline | Important boundary |
|---|---|---|
| Azure Databricks | Commercial Azure, US region, Premium-capable design, Unity Catalog, HIPAA compliance security profile, appropriate Enhanced Security and Compliance add-on; approved classic/serverless workloads | Azure storage, identity, networks, clients, and integrations remain separately governed. Managed DR is an additional, gated offering, not assumed purchased. [D01-D06, D12, D16] |
| Snowflake | Commercial Azure, US account region, Business Critical, signed Snowflake BAA, native policy-governed tables, private connectivity where required | Azure hosting does not transfer a Microsoft BAA to Snowflake. External storage, applications, containers, and collaborators introduce additional boundaries. [S01, S07-S09] |
| Microsoft Fabric | Commercial tenant, US home/capacity-region review, suitable Fabric capacity, OneLake security, relevant Entra/Purview licenses; workspace-level private isolation for restricted PHI engineering | Tenant-level Private Link is a separate design option. Power BI and all Fabric workloads must not be assumed to have identical network, key, identity, or audit behavior. [F01-F08, F12-F14] |

No accounts, tenant configurations, contracts, protected datasets, or vendor assurance reports were inspected. No vulnerability testing was performed. Existing `regulatory-requirements\requirements.txt` notes were treated as research leads, not verified findings; private architecture assertions in those notes are outside this public-evidence assessment.

The Fabric N08 G rating applies to the strict variant requiring workspace-level inbound restrictions on every PHI-bearing workspace, including BI serving. The N05 G rating applies where policy demands customer-controlled keys for every designated PHI artifact. A different serving/tenant-private architecture or a narrower approved artifact scope must be assessed separately.

This is procurement and architecture guidance, **not legal advice, a certification, or a production authorization**. Requirements such as private-only networking, CMK for every artifact, and zero lost audit events are proposed hospital policies; they are not represented as universal HIPAA prescriptions.

## 3. Regulatory interpretation

| Regime or obligation | How it affects selection | Applicability caveat |
|---|---|---|
| HIPAA/HITECH privacy, security, and business-associate obligations | Require appropriate contractual coverage, risk management, access control, auditability, incident processes, and evidence of hospital controls. [REG01, REG02] | There is no HHS-approved general HIPAA certification that independently authorizes this deployment. A vendor attestation or BAA is not the hospital's compliance program. [REG02] |
| 42 CFR Part 2 | Identify applicable substance-use-disorder records and implement permitted-use/disclosure rules in data and workflow controls. [REG03] | The 2024 final rule became effective April 16, 2024; its compliance date was February 16, 2026, already past on this assessment date. Do not treat every hospital record as a Part 2 record. |
| HIPAA research and human-subject requirements | Obtain the appropriate research authorization/waiver and study approvals; restrict data access to approved purposes and study participants. [REG06] | IRB approval is not automatically a HIPAA authorization; applicability and documentation require research/legal review. |
| De-identification and limited-data-set releases | Validate the legally appropriate release method; masking and pseudonymization are not interchangeable with HIPAA de-identification. [REG04] | A limited data set remains subject to applicable HIPAA requirements and a data-use agreement; it is not the same as de-identified data. [REG06] |
| FDA electronic records/signatures and regulated research | Establish applicability, validation, controlled records, signatures, and change evidence for regulated workflows. [REG05] | Part 11 is conditional on the relevant records and predicate-rule context, not automatically applicable to every hospital analytics workload. |
| Grant, genomic repository, sponsor, state, and international requirements | Add the actual agreement-specific restrictions to the baseline before approval. | Do not infer FedRAMP, a particular NIST control baseline, US-only residency, or international permission from HIPAA alone. |

**Retention distinction:** NIST's HIPAA guidance addresses required documentation retention. Do not turn that into a claim that HIPAA universally requires every raw access event, research dataset, or clinical record to be retained for six years. Legal, research, security, and records teams must approve separate schedules. [REG01]

Proposed rule changes and roadmap promises receive no compliance credit without confirming the applicable final obligation or released capability.

## 4. Ratings, priority, and evidence rules

| Code | Meaning |
|---|---|
| **M** | Meets the platform-capability requirement in documented supported functionality. Configuration and deployment verification are still required. |
| **C** | Conditionally meets: extra licensing, architecture, hospital processes, scope restrictions, or compensating controls are required. The conditions are material. |
| **G** | A documented product/design gap against the stated requirement in the evaluated baseline. A redesigned solution may address it; the present baseline does not. |
| **U** | Unverified: public evidence or supplied artifacts do not establish the requirement. This is not a claim that the capability is absent. |

**P0:** production approval gate. **P0\*:** approval gate when the named dataset, use case, or policy applies. **P1:** important selection criterion. **P2:** optimization criterion.

For a P0/P0* row, **C is not an approval**: its conditions must be closed. U blocks approval until resolved. G requires redesign or formal policy exception where legally permissible. A contract or legal requirement cannot be waived merely through technical risk acceptance.

Evidence hierarchy: executed contract and current scoped assurance artifact; current official technical documentation; released-feature announcement; public overview. Marketing and roadmap statements cannot close a gate. Product facts are source-cited in the companion assessments; proposed tests, risk judgments, and hospital operating requirements are labeled through this methodology rather than presented as vendor facts.

## 5. Requirements matrix

Every product assessment maps to all **60** requirement IDs below. Ratings are deliberately conservative and are not counts of installed controls.

### Legal, contractual, and research obligations

| ID | Priority | Requirement | Evidence / acceptance criterion | Databricks | Snowflake | Fabric |
|---|---|---|---|---|---|---|
| L01 | P0 | Effective BAA and appropriate processor/subcontractor terms | Counsel confirms executed terms cover the hospital and relevant processing relationship. | C | C | C |
| L02 | P0 | Exact service, region, feature, preview, and subprocessor eligibility | Written scope inventory covers every PHI path, AI service, support process, and secondary region. | U | U | U |
| L03 | P0* | Required third-party assurance or government authorization | Review current scoped reports, exceptions, bridge letters, and any contract-required authorization. | U | U | U |
| L04 | P0 | Hospital risk assessment and shared-responsibility allocation | Approved risk register and RACI identify each technical and organizational control owner. | C | C | C |
| L05 | P0* | Sensitive-category and Part 2 use/disclosure restrictions | Legal-approved policy and negative tests cover the applicable record categories. | C | C | C |
| L06 | P0* | Research authorization, IRB, grant, and data-use restrictions | Approved study/purpose list, investigator roster, agreements, and expiration evidence. | C | C | C |
| L07 | P0* | Validated regulated records and signatures | Document applicability; retain validation, signature linkage, change controls, and audit evidence. | C | C | C |
| L08 | P0* | Approved residency and processing geography | Map data, metadata, logs, backups, support, and inference; approve every location. | C | C | C |
| L09 | P0 | Incident, notification, audit, and supplier-accountability terms | Counsel approves escalation, contractual deadlines, evidence access, and supplier responsibilities. | U | U | U |
| L10 | P1 | Approved retention, legal hold, permitted deletion, and access rights | Records schedule distinguishes primary data, replicas, derived data, audit evidence, and research exceptions. | C | C | C |

### Identity, authorization, and study boundaries

| ID | Priority | Requirement | Evidence / acceptance criterion | Databricks | Snowflake | Fabric |
|---|---|---|---|---|---|---|
| I01 | P0 | Federated human identity and enforceable MFA | Test normal, emergency, guest, browser, and client authentication without unintended fallback. | M | M | M |
| I02 | P0 | Separate workload identities and short-lived authentication | Inventory service identities; verify supported federation/OAuth paths and revoke legacy credentials. | C | M | C |
| I03 | P0 | Least privilege and separation of duties | Researchers cannot administer policy, grant themselves access, or alter independent audit evidence. | C | C | C |
| I04 | P0 | Study, environment, and collaborator isolation | Negative tests prove Study A cannot enumerate/read Study B; isolate dev/test/prod appropriately. | C | C | C |
| I05 | P0 | Row/column restrictions across every allowed read path | Test SQL, notebooks, APIs, storage, BI, sharing, caches, and third-party engines. | C | C | C |
| I06 | P1 | Controlled writers and transformation identities | Restrict writes and policy changes; separate ingestion/curation from restricted researcher access. | C | C | C |
| I07 | P1 | Privileged and vendor-support oversight | Time-bound elevation, emergency-use review, support-access procedure, and independently retained evidence. | C | C | C |
| I08 | P0* | External researcher onboarding and timely revocation | Sponsor approval, study expiry, group reconciliation, and measured session/token revocation behavior. | C | C | C |

### Network, encryption, and secrets

| ID | Priority | Requirement | Evidence / acceptance criterion | Databricks | Snowflake | Fabric |
|---|---|---|---|---|---|---|
| N01 | P0* | Private-only inbound PHI paths under hospital policy | Public-path deny tests cover UI, APIs, SQL, storage, clients, and applicable administrative endpoints. | C | C | C |
| N02 | P0* | Outbound exfiltration controls under hospital policy | Default-deny/allowlist tests cover network destinations, storage, libraries, sharing, and applications. | C | C | C |
| N03 | P1 | Private connectivity to EHR/on-premises/partner systems | Demonstrate supported connector, DNS, authentication, firewall, gateway, and failover configuration. | C | C | C |
| N04 | P0 | Supported encryption at rest and in transit | Inventory encryption coverage, protocols, certificates, and separately operated storage/integration boundaries. | M | M | M |
| N05 | P0* | Customer-controlled keys for all policy-required PHI artifacts | Identify data, BI models, logs, notebook outputs, and backups; verify CMK scope individually. | C | C | G |
| N06 | P1 | Safe key rotation, revocation, and recovery | Demonstrate rotation and outage/recovery procedures without irreversible loss of required records. | C | C | C |
| N07 | P0 | Secrets and sensitive metadata hygiene | No PHI in resource names, source control, prompts, traces, URLs, or diagnostic literals unless approved. | C | C | C |
| N08 | P0* | Required workloads coexist with the selected network isolation | Prove BI, CI/CD, monitoring, research compute, and required AI work without reopening prohibited paths. | C | C | G |

### Governance, privacy, and research workflows

| ID | Priority | Requirement | Evidence / acceptance criterion | Databricks | Snowflake | Fabric |
|---|---|---|---|---|---|---|
| G01 | P1 | Sensitive-data inventory, classification, and ownership | Detect structured/unstructured PHI and genomic sensitivity; assign ownership and verify coverage. | C | C | C |
| G02 | P1 | Lineage plus historical research provenance | Reconstruct a released cohort/model from input version, transformation, consent, policy, and software versions. | C | C | C |
| G03 | P0* | Consent, purpose, study expiry, and withdrawal handling | Entitlements and dataset changes follow approved rules; validate downstream copies and exceptions. | C | C | C |
| G04 | P0* | Legally defensible de-identification before permitted release | Expert Determination or Safe Harbor process as appropriate; evaluate free text, images, and linkage risks. | C | C | C |
| G05 | P0* | Controlled cross-organization sharing | Recipient approval, legal terms, permitted query scope, replication restrictions, and revocation evidence. | C | C | C |
| G06 | P0* | Research enclave and approved output release | Raw export, clipboard/download, package installation, and researcher endpoints are controlled; outputs reviewed. | C | C | C |
| G07 | P0* | PHI-safe AI/ML, RAG, agents, and copilots | Confirm service eligibility, retrieval authorization, geography, training use, traces, tools, and output safeguards. | C | C | C |
| G08 | P1 | Portable data with equivalent policy on external engines | Demonstrate export/read access without losing authorization, provenance, and deletion controls. | C | C | C |
| G09 | P1 | BI/export/cache/endpoint privacy controls | Test CSV, Excel, PBIX, subscriptions, embedded access, caches, and local endpoint copies as applicable. | C | C | C |
| G10 | P1 | Sustainable healthcare interoperability | Validate required FHIR/HL7/OMOP/DICOM semantics, terminology, quality, and supported ingestion lifecycle. | C | C | C |
| G11 | P0* | Clinical intended-use and model-risk separation | Clinical safety review approves applicable use; research-only work cannot silently become care delivery. | C | C | C |

### Audit, monitoring, and incident evidence

| ID | Priority | Requirement | Evidence / acceptance criterion | Databricks | Snowflake | Fabric |
|---|---|---|---|---|---|---|
| A01 | P0 | Attributable access evidence for all allowed PHI paths | Correlate principal, object, action, time, outcome, and effective policy across a synthetic access suite. | C | C | C |
| A02 | P1 | Preserve human/workload attribution through delegation | Distinguish requesting user from owner/service identity; retain correlation IDs and identity mappings. | C | C | C |
| A03 | P0* | Hospital-required audit completeness and latency | If lossless evidence is required, obtain documented guarantees and reconcile events under load/failure. | U | U | G |
| A04 | P1 | Historical effective-entitlement reconstruction | Answer who could access a named dataset at a prior time using grants, groups, policies, ownership, and changes. | C | C | C |
| A05 | P0 | Independent, retained, tamper-resistant audit evidence | Archive required logs outside analyst control; enforce retention/hold and test privileged deletion scenarios. | C | C | C |
| A06 | P1 | Protect PHI embedded in audit and query logs | Minimize literals and outputs; restrict log access; apply appropriate encryption, retention, and incident controls. | C | C | C |
| A07 | P1 | SOC detection resilient to analytics disruption | Test exfiltration, privilege changes, audit-disable, and collector failure using independent notifications. | C | C | C |
| A08 | P0 | Incident investigation and legally appropriate notification | Rehearse evidence preservation, scope determination, containment, and counsel-led notification. | C | C | C |
| A09 | P1 | Achievable audit collection permissions | Hospital tenant/platform teams approve the actual least-privileged collector and evidence-sharing process. | C | C | C |

### Resilience, integrity, and controlled delivery

| ID | Priority | Requirement | Evidence / acceptance criterion | Databricks | Snowflake | Fabric |
|---|---|---|---|---|---|---|
| R01 | P0 | Restore after error, compromise, or destructive administration | Restore data, policies, code, identities, and configuration from independently protected recovery assets. | C | C | C |
| R02 | P0 | Workload-specific cross-region RPO/RTO and write recovery | Demonstrate full application recovery, not only object replication or read access; measure approved targets. | C | C | C |
| R03 | P1 | Workload isolation and safe contention behavior | Research peaks cannot jeopardize agreed hospital analytics availability; verify quotas and degradation behavior. | C | C | C |
| R04 | P0* | Controlled releases and reproducible regulated workflows | Approved change, versioned policy/code/configuration, automated negative tests, and rollback evidence. | C | C | C |
| R05 | P1 | Platform and dependency security maintenance | Assign patch ownership for SaaS, runtimes, libraries, containers, agents, gateways, and clients. | C | C | C |
| R06 | P0* | Data integrity and clinically/research-relevant validation | Reconcile ingestion, identifiers, units, terminology, provenance, duplicates, and analytic outputs. | C | C | C |
| R07 | P1 | Contract exit, export, and appropriately evidenced deletion | Test usable exports and policy migration; address replicas, historical copies, retention exceptions, and termination. | C | C | C |

### Commercial and operating requirements

| ID | Priority | Requirement | Evidence / acceptance criterion | Databricks | Snowflake | Fabric |
|---|---|---|---|---|---|---|
| O01 | P1 | Fully loaded lifecycle cost | Cost controls, required editions/add-ons, storage/history, DR, networking, assurance, labor, and migration. | C | C | C |
| O02 | P1 | Budget guardrails without unsafe hospital-service shutdown | Prove alerts and workload-specific limits; distinguish warehouse/compute budgets from serverless and AI charges. | C | C | C |
| O03 | P1 | Scalable study/environment provisioning | Provision and retire isolated studies through approved automation; estimate actual tenancy and staffing footprint. | C | C | C |
| O04 | P1 | Supported lifecycle, mature controls, and contractual support | Validate availability/support for every dependency; do not rely on a retirement-bound accelerator or roadmap. | C | C | C |
| O05 | P1 | Sustainable hospital operating skills | Named owners can maintain identity, governance, network, audit, research workflows, recovery, and evidence. | C | C | C |
| O06 | P1 | Realistic licenses, integrations, and organizational privileges | Confirm capacity/edition/Entra/Purview/client needs and central-team approvals before procurement. | C | C | C |
| O07 | P2 | Representative workload and migration benchmarks | Measure approved workloads with synthetic/de-identified data, realistic concurrency, controls, and cost. | C | C | C |

## 6. Control-mapping aids

These identifiers organize evidence; they do **not** assert that a product implements or is certified against an entire baseline. NIST SP 800-66 Rev. 2 is the HIPAA implementation mapping resource. [REG01]

| Requirement family | Illustrative HIPAA / NIST anchors |
|---|---|
| Legal and risk | 45 CFR 164.308(a)(1), 164.308(b), 164.314; RA-3, SA-9 |
| Identity and study boundaries | 164.308(a)(3)-(4), 164.312(a), 164.312(d); AC-2, AC-3, AC-5, AC-6, IA-2 |
| Network, keys, secrets | 164.312(a), 164.312(e); SC-7, SC-12, SC-13, SC-28, IA-5 |
| Governance and research | Applicable Privacy Rule/research obligations; AC-3, AC-4, purpose-specific policies |
| Audit and incidents | 164.308(a)(6), 164.312(b); AU-2, AU-3, AU-5, AU-6, AU-9, AU-12, IR-4 |
| Resilience and integrity | 164.308(a)(7), 164.312(c); CP-9, CP-10, SI-7, CM-3 |
| Operations and suppliers | SA-9, CM-2, CM-3, SI-2; hospital supplier and records policies |

## 7. Selection and approval procedure

### Pass gates before scoring

First determine which P0* rows apply. For each candidate, maintain a closure record containing requirement ID, evidence, remediation owner, due date, residual risk, approver, and decision. Approval requires closed applicable gates, not a favorable average.

The particularly important unresolved gates are **L02/L09** for all candidates, **A03** if the hospital demands a lossless trail, and **N05/N08** for the strict Fabric workspace-private baseline. The companion assessments explain how these differ from manageable configuration work.

### Proposed weighting after gate closure

These weights are **proposed decision preferences**, not measured product scores.

| Category | Hospital BI / warehouse | Research / ML | Strict research enclave |
|---|---|---|---|
| Legal and research obligations | 20% | 18% | 15% |
| Identity and authorization | 18% | 18% | 20% |
| Network and encryption | 15% | 15% | 20% |
| Governance and privacy | 15% | 22% | 20% |
| Audit and incident evidence | 15% | 12% | 15% |
| Resilience and integrity | 12% | 10% | 7% |
| Commercial and operations | 5% | 5% | 3% |

After proof-of-concept, score applicable requirements 0-4 for demonstrated suitability, with separate confidence/evidence fields. Remove nonapplicable requirements from the denominator. A conditional capability receives credit only to the extent its conditions have been funded and demonstrated. Do not manufacture a numerical winner from this desk review.

### Mandatory comparative proof-of-concept

| Test | Pass evidence | Relevant IDs |
|---|---|---|
| Two studies, three environments, distinct reader/writer/admin identities | Deny cross-study access and raw-storage bypass; retain policy and identity results. | I03-I06 |
| Private networking and egress attack simulation | Public and unapproved outbound destinations fail; required BI/CI/CD/monitoring still operate. | N01-N03, N08 |
| Multi-path audit reconciliation | Reconcile known SQL, notebook, API, direct-file, BI, sharing, and denied actions against collected evidence. | A01-A05 |
| Consent/expiry and collaborator termination | Validate approved propagation time, token/session handling, caches, shared copies, and permitted retention exceptions. | L05-L06, G03, I08 |
| De-identification and output review | Validate free text, dates, image metadata, rare diseases, and genomic linkage; retain release approval. | G04-G06 |
| AI/RAG isolation and geography | Unauthorized context is unavailable; prompts, traces, tool calls, and inference regions match the approved scope. | L02, L08, G07 |
| Failure, recovery, key rotation, and budget stress | Restore usable workflows and evidence within approved targets without uncontrolled public access or key loss. | N06, R01-R03, O02 |

The POC should initially use synthetic or appropriately de-identified data. Production PHI cannot be used merely because it is a test.

### Proposed measurable acceptance targets

These are example procurement targets, not vendor guarantees or statutory numbers. The hospital must approve or replace them before the POC.

| Control | Proposed target |
|---|---|
| Authorization and exfiltration denial | 100% of defined prohibited study/engine/network/export test cases denied, with usable deny evidence. |
| Study expiry and collaborator termination | New access prevented within 15 minutes of an approved entitlement change; separately measure active sessions, caches, and retained authorized outputs. |
| Access-evidence reconciliation | 100% of required synthetic operations reconciled, including failure/load tests; 99% of security events available to the SOC within 15 minutes. Lossless policy additionally requires a documented guarantee. |
| Recovery for hospital operational analytics | Demonstrated full-workflow RTO of four hours and data RPO of one hour; research/batch workloads receive separately approved targets. This is not a life-safety-system target. |
| Reproducibility and controlled delivery | Every released regulated analysis has retrievable input/code/policy/consent versions and an approval record; all defined policy regression tests pass before promotion. |
| Data integrity | 100% reconciliation of source counts/control totals and identifier mappings in the agreed test suite; clinically meaningful discrepancies block release. |

## 8. Regulatory and methodology source register

Reviewed September 30, 2026. URLs are provided as copyable source locations. Product registers are in the individual assessment files.

| ID | Primary source | Location / use |
|---|---|---|
| REG01 | NIST, SP 800-66 Rev. 2, *Implementing the HIPAA Security Rule: A Cybersecurity Resource Guide* | `https://csrc.nist.gov/pubs/sp/800/66/r2/final` - risk, responsibilities, safeguards, and control-mapping framework. |
| REG02 | Microsoft Compliance, HIPAA/HITECH overview | `https://learn.microsoft.com/en-us/compliance/regulatory/offering-hipaa-hitech` - BAA framework, shared responsibility, no HHS-approved HIPAA certification; not proof of all Fabric feature eligibility. |
| REG03 | HHS final rule, *Confidentiality of Substance Use Disorder Patient Records*, 89 FR 12472 | `https://www.govinfo.gov/content/pkg/FR-2024-02-16/html/2024-02544.htm` - primary final-rule text and effective/compliance dates. |
| REG04 | HHS OCR, de-identification guidance | `https://www.hhs.gov/hipaa/for-professionals/privacy/special-topics/de-identification/index.html` - Safe Harbor and Expert Determination. |
| REG05 | FDA, *Part 11, Electronic Records; Electronic Signatures - Scope and Application* | `https://www.fda.gov/regulatory-information/search-fda-guidance-documents/part-11-electronic-records-electronic-signatures-scope-and-application` - conditional scope and validation context. |
| REG06 | HHS OCR, research guidance | `https://www.hhs.gov/hipaa/for-professionals/special-topics/research/index.html` - research authorization/waiver and limited-data-set framework. |

**Access limitations:** HHS REG04/REG06 were available through indexed official-source search excerpts; direct HHS page retrieval returned HTTP 403. Federal Register interactive retrieval was blocked, so REG03 was verified through the official Government Publishing Office HTML and Federal Register's published API. No restricted vendor assurance reports or executed hospital agreements were available. Obtain complete legal source/contract review before an approval decision.
