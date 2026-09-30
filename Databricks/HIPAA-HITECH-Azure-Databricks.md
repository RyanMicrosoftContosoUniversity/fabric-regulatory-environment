# Hospital HIPAA/HITECH requirements and Azure Databricks implementation

**Research date:** September 30, 2026  
**Perspective:** Hospital enterprise/data architect using Azure Databricks  
**Status:** Proposed control design; implementation and operating effectiveness have not been assessed  
**Terminology:** HIPAA is the Health Insurance Portability and Accountability Act. HITECH is the Health Information Technology for Economic and Clinical Health Act.

## 1. Executive finding and scope

Azure Databricks is a viable component of a hospital's HIPAA-regulated data architecture, subject to service eligibility, contractual coverage, configuration, and the hospital's compliance program. It is not a substitute for that program. A business associate agreement (BAA), encryption, or a vendor attestation alone does not establish the hospital's compliance. There is no HHS-approved HIPAA certification program for cloud providers. [D01, D19]

This is a comprehensive implementation inventory for the HIPAA/HITECH obligations relevant to a hospital PHI platform: the general requirements in 45 CFR Part 160; Security Rule standards and named implementation specifications in 164.306-164.316; Privacy Rule categories, patient rights, and organizational duties; breach notification; and applicable Part 162 electronic transaction requirements. Conditional obligations are included rather than silently assumed away.

**Boundaries:** This is not a legal opinion or an inventory of every law governing a hospital. It does not replace a state-specific legal analysis, a full 42 CFR Part 2 assessment, human-subject research review, medical-device regulation, or an EHR certification/incentive-program assessment. HIPAA's insurance-portability and other non-PHI-platform provisions require separate benefits/legal review when applicable. Counsel must determine applicability and resolve conflicts, exceptions, and court orders.

Assume a US hospital that is a HIPAA covered entity; production electronic protected health information (ePHI); clinical, operational, billing, and potentially research workloads; Microsoft Entra ID identities; Unity Catalog governance; Azure storage; and hospital-managed endpoints. No actual subscription, region, BAA, workload, tenant policy, or deployed configuration was provided.

### Reading the matrices

- **R:** Required implementation specification in the current Security Rule.
- **A:** Addressable implementation specification. Assess it, implement it when reasonable and appropriate, or document why not and implement an equivalent alternative when reasonable and appropriate. It is not optional.
- **Std:** Mandatory standard without a separately named R/A implementation specification.
- **Conditional:** Applies when the specified activity or organizational role exists; retain a documented applicability decision otherwise.
- **Implementation:** An architectural recommendation unless explicitly identified as a vendor prerequisite. HIPAA generally specifies outcomes, not particular products.
- **Evidence:** Proposed proof of implementation and operation, not a claim that evidence currently exists.

Owners: **Privacy** = privacy officer/HIM; **Security** = security officer/SOC; **Platform** = Azure/Databricks platform team; **Data** = data owners/engineering; **Legal** = legal/procurement; **HR** = workforce administration; **Facilities** = physical security; **Clinical** = clinical operations; **Revenue** = revenue cycle/EDI.

Other abbreviations: **HIM** = health information management; **NPP** = notice of privacy practices; **DUA** = data use agreement; **CMK** = customer-managed key; **RPO/RTO** = recovery point/time objective; **NCC** = network connectivity configuration; **SOC** = security operations center; **EDI** = electronic data interchange.

Source IDs refer to the primary-source register in section 15. Legal citations are the controlling references; product documentation supports the platform mapping.

## 2. Current-law and product distinctions

| Topic | Finding as researched September 30, 2026 | Architectural consequence |
| --- | --- | --- |
| Security Rule cybersecurity proposal | HHS published the NPRM on January 6, 2025. The 2026 Unified Agenda identifies it as a long-term action and projects final action in July 2027. An agenda forecast is not a final rule or compliance deadline. [L21, L22] | Use the current Security Rule for the legal baseline. Treat proposed prescriptive controls as forward planning, not already-effective legal obligations. |
| Addressable controls | The present rule retains required/addressable distinctions. [L03] | Implement encryption and strong authentication for this hospital design; do not mislabel MFA, private endpoints, CMKs, or a particular review frequency as universally prescribed by current HIPAA text. |
| Reproductive-health amendments | HHS reports that the June 18, 2025 court order vacated most of the 2024 reproductive-health rule, including NPP provisions 164.520(b)(1)(ii)(F)-(H). Other NPP modifications remain, with compliance required February 16, 2026. [L19, L20] | Do not represent the vacated attestation/prohibition provisions as operative HIPAA requirements merely because they still appear in codified text. Continue ordinary HIPAA and applicable state-law protections; have counsel monitor litigation. |
| Patient-directed third-party access | HHS's notice of the January 23, 2020 Ciox decision limits the mandatory third-party directive to an electronic copy of PHI in an electronic health record. The HIPAA individual-access fee limitation does not apply to a request to transmit records to a third party. [L28] | Do not apply the older unqualified directive/fee interpretation to every electronic analytic record. Individual access to one's own designated-record-set PHI remains protected; have HIM/Legal classify the request and applicable fees. |
| Substance-use disorder records | The 2024 Part 2 final rule's compliance date was February 16, 2026. Updated HIPAA notices include applicable Part 2 information. [L20, L23] | Identify Part 2 records and implement the additional applicable consent, proceedings, and notice restrictions. Not every SUD diagnosis is automatically a Part 2 record. |
| Claims attachments/electronic signatures | CMS finalized claims-attachment standards; effective May 26, 2026, with compliance 24 months later, May 26, 2028. Prior-authorization attachment standards were not finalized in that rule. [L25] | Include a future-dated EDI implementation workstream, not a claim that every hospital notebook must already implement those signatures. |
| HIPAA product prerequisites | Current Azure Databricks documentation requires the compliance security profile for PHI, the HIPAA standard selection, Premium pricing, and the Enhanced Security and Compliance add-on. [D01-D03] | Make these production admission checks. Do not import AWS Enterprise-tier requirements into an Azure design. |
| VNet encryption | Current profile configuration guidance requires Azure VNet encryption and supported VM types. Enforcement of the VNet-encryption enablement requirement begins February 1, 2027, including existing profile-enabled workspaces. [D03] | Design and validate it now. Distinguish the current product requirement from the later enforcement date and from the HIPAA legal text. |

The eCFR title 45 pages inspected displayed currency through September 28, 2026. HHS guidance and the regulatory agenda were checked during this research. Recheck regulations, litigation, contractual terms, and product support before deployment.

## 3. Contractual and platform admission requirements

These are deployment prerequisites or hospital design decisions, not additional provisions invented for HIPAA.

| ID | Admission requirement | Azure Databricks/Azure implementation | Owner and evidence |
| --- | --- | --- | --- |
| G01 | Determine covered-entity, business-associate, workforce, and PHI boundaries. 160.103; 164.104-.106; 164.302; 164.500-.501. [L01, L02] | Inventory EHR extracts, images, free text, billing, derived datasets, model artifacts, outputs, caches, logs, and external services. Classify which are PHI and which are designated record sets. | Privacy/Legal/Data: approved applicability and data-flow inventory. |
| G02 | Respect applicable more-protective law and HIPAA preemption exceptions. 160.201-.205. [L01] | Maintain state/data-type rules in the authorization and release workflow. Separate Part 2, minors' records, and other specially protected datasets as needed. | Legal/Privacy: jurisdiction and restriction matrix. |
| G03 | Obtain satisfactory BA assurances before PHI processing. 164.502(e), 164.504(e), 164.308(b), 164.314(a). [L02, L04, L07, D19] | Verify the Microsoft agreement, incorporated BAA/DPA, in-scope services, and the actual Azure Databricks purchasing/subprocessor chain. Microsoft describes BAA incorporation into qualifying agreements; do not assume a separate signature is always necessary. Additional direct PHI vendors need appropriate agreements. | Legal: executed/incorporated terms, service-scope review, vendor register. |
| G04 | Configure the supported HIPAA product boundary. [D01-D03] | Premium plus Enhanced Security and Compliance; enable the profile and select HIPAA on every PHI workspace, including test and DR when they contain PHI. Implement VNet encryption and supported VM types as applicable to classic compute. | Platform: settings/IaC exports and region/feature approval. |
| G05 | Meet notebook-result and encryption prerequisites. [D01, D04] | Configure managed-services CMK or Store interactive notebook results in customer account, as required by the HIPAA guide. This design chooses managed-services CMK and separately protects customer storage and disks. Verify each data location; one key setting is not blanket encryption coverage. | Platform/Security: key/data-location mapping and configuration proof. |
| G06 | Admit only supported features and contractual destinations. [D01-D03] | Check the current HIPAA regional feature matrix, release status, actual availability, preview exceptions, connectors, AI providers, support workflows, and DR features. General availability alone is not proof of contractual/PHI eligibility. Keep real PHI out of names, tags, URLs, and other excluded metadata fields. | Legal/Platform: feature allowlist and vendor confirmations where documentation is ambiguous. |

**Important:** Profile/standard selections are intended to be permanent after regulated data has been processed. Plan environment boundaries before enabling them. Do not disable a compliance profile to make an unsupported PHI feature work. [D03]

## 4. Security Rule requirements matrix

### 4.1 General and administrative safeguards

This table covers 164.306 and each named administrative implementation specification in 164.308, together with the mandatory standards. Source basis: [L03, L04]; platform mechanisms: [D02, D03, D05, D08, D11-D18, D21].

| ID | Requirement and legal citation | Class | Proposed implementation | Accountable owner; evidence |
| --- | --- | --- | --- | --- |
| S01 | Confidentiality, integrity, availability, anticipated threats/impermissible disclosures, workforce compliance; risk-sensitive selection and ongoing maintenance. 164.306(a)-(e). | Std | Maintain an end-to-end hospital threat model and control baseline; assess insiders, ransomware, storage bypass, third parties, and clinical outages. Apply every applicable standard, including addressable decisions. | Security: risk/control register and approvals. |
| S02 | Risk analysis. 164.308(a)(1)(ii)(A). | R | Assess all ePHI flows, identities, external locations, control/compute planes, endpoints, logs, AI, exports, backups, and vendors; include likelihood and impact. A configuration scan is only an input. | Security/Data: scoped risk assessment. |
| S03 | Risk management. 164.308(a)(1)(ii)(B). | R | Assign remediation owners/dates; enforce least privilege, restricted networks, encryption, and monitoring; document residual-risk acceptance by authorized hospital leadership. | Security/Platform: remediation and acceptance records. |
| S04 | Workforce sanction policy. 164.308(a)(1)(ii)(C). | R | Define and apply hospital sanctions; use access and incident evidence to investigate violations. Databricks cannot determine disciplinary consequences. | HR/Security: policy and protected case records. |
| S05 | Information-system activity review. 164.308(a)(1)(ii)(D). | R | Review relevant audit, identity, storage, key, network, and incident records; alert on bulk export, privilege changes, unusual reads, disabled logging, and emergency access. | Security: review records and alert dispositions. |
| S06 | Assigned security responsibility. 164.308(a)(2). | Std | Designate the hospital security official and platform/data delegates; define escalation and authority. | Hospital leadership: designation and RACI. |
| S07 | Authorization/supervision. 164.308(a)(3)(ii)(A). | A | Require manager/data-owner approval; assign Entra/account groups and supervised access; control contractors and privileged sessions. | HR/Data: approval records and access tests. |
| S08 | Workforce clearance. 164.308(a)(3)(ii)(B). | A | Determine job-appropriate PHI access before provisioning; verify training and organizational clearance requirements. | HR/Data: clearance and role matrix. |
| S09 | Termination procedures. 164.308(a)(3)(ii)(C). | A | Disable identity and workspace access; remove grants/groups; revoke PATs/OAuth credentials and affected secrets; address active sessions and job ownership. Test real revocation latency. | HR/Platform: termination tickets and denied-access proof. |
| S10 | Isolate clearinghouse functions if present. 164.308(a)(4)(ii)(A). | R, conditional | Separate catalogs, storage identities, workspaces, and duties from unrelated hospital functions. Record non-applicability if the hospital performs no clearinghouse function. | Legal/Revenue/Platform: boundary or applicability decision. |
| S11 | Access authorization. 164.308(a)(4)(ii)(B). | A | Define purpose/role-based access; grant Unity Catalog privileges to approved account groups and service principals rather than broadly to all workspace users. | Data/Platform: approvals and grant exports. |
| S12 | Establish/review/modify access. 164.308(a)(4)(ii)(C). | A | Implement joiner/mover/leaver processes, periodic recertification, and permission-change history. Test workspace ACLs, catalog grants, underlying storage, and connector credentials together. | Data/Platform: recertifications and historical snapshots. |
| S13 | Workforce security awareness/training, including management. 164.308(a)(5)(i). | Std | Train for notebooks, exports, tokens, phishing, metadata, support tickets, sensitive outputs, and incident reporting; use role-specific hospital training. | HR/Security: completion and effectiveness records. |
| S14 | Security reminders. 164.308(a)(5)(ii)(A). | A | Issue periodic reminders and targeted warnings after relevant changes or incidents; do not include PHI in reminder examples. | Security: dated communications. |
| S15 | Malicious-software protection. 164.308(a)(5)(ii)(B). | A | Use profile monitoring on supported compute; control package sources, libraries, init scripts, and deployment privileges; protect hospital endpoints. Review monitoring findings. | Platform/Security: policy, monitoring, response evidence. |
| S16 | Login monitoring. 164.308(a)(5)(ii)(C). | A | Correlate Entra sign-ins and Databricks authentication events; investigate failures, unusual origins, token usage, and service-principal anomalies. | Security: detections and investigations. |
| S17 | Password/credential management. 164.308(a)(5)(ii)(D). | A | Use hospital Entra policy and strong authentication; prefer OAuth over PATs; use managed identities for storage and narrowly authorized secret scopes for unavoidable credentials. | Security/Platform: credential policies and access reviews. |
| S18 | Incident response/reporting. 164.308(a)(6)(i)-(ii). | Std + R | Establish SOC triage, containment, evidence preservation, harmful-effect mitigation, vendor escalation, and Privacy-led breach assessment. Record suspected incidents and outcomes, not only confirmed breaches. | Security/Privacy: playbooks, exercises, incident records. |
| S19 | Data backup plan. 164.308(a)(7)(ii)(A). | R | Maintain retrievable exact copies of ePHI with supported independent backup/copy mechanisms; include Delta data/log consistency, governed metadata, code/configuration, and key recovery. Validate completeness. | Platform/Data: backup manifests and restore evidence. |
| S20 | Disaster recovery plan. 164.308(a)(7)(ii)(B). | R | Define workload-specific RPO/RTO; deploy a compliant recovery environment; use eligible managed DR or engineered replication for uncovered assets. Include external systems, DNS, identities, keys, and reconnecting consumers. | Platform/Clinical: runbooks and measured recovery. |
| S21 | Emergency-mode operations. 164.308(a)(7)(ii)(C). | R | Define safe clinical/operational fallback, recovery priorities, restricted emergency access, and reconciliation after outage. Analytics downtime must not create an undocumented dependency for critical care. | Clinical/Platform: approved emergency procedures. |
| S22 | Contingency testing/revision. 164.308(a)(7)(ii)(D). | A | Exercise restore, failover, failback, key loss, identity outage, ransomware, and critical integration failures; remediate findings. | Platform/Clinical: exercises and revised plans. |
| S23 | Application/data criticality analysis. 164.308(a)(7)(ii)(E). | A | Classify clinical, operational, research, and billing workloads by patient-safety/business impact and restoration order. | Clinical/Data: impact analysis and recovery tiers. |
| S24 | Periodic technical/nontechnical evaluation. 164.308(a)(8). | Std | Evaluate controls periodically and following material environmental/operational changes. Include legal/organizational procedures, not just cloud posture. | Security/Privacy: assessment and change-trigger records. |
| S25 | BA assurances and written agreement. 164.308(b)(1)-(3). | R | Maintain G03 and S48 for all direct PHI service relationships and the subcontractor chain. Ensure the relevant contracting party obtains downstream assurances. | Legal: BAA and subcontractor evidence. |

The overarching workforce-security, information-access-management, security-management, and contingency standards are implemented through their corresponding rows, not just by the presence of an individual feature.

### 4.2 Physical safeguards

Cloud hosting reallocates some physical responsibilities; it does not remove the hospital's workstation, facility, device, or export obligations. Source basis: [L05]; vendor boundary: [D01, D19].

| ID | Requirement and legal citation | Class | Proposed implementation | Accountable owner; evidence |
| --- | --- | --- | --- | --- |
| S26 | Emergency facility access for restoration. 164.310(a)(2)(i). | A | Review cloud-provider facility assurances; maintain hospital access procedures for recovery locations, network rooms, and authorized emergency staff. | Facilities/Platform: recovery-access procedure and exercises. |
| S27 | Facility security plan. 164.310(a)(1), (a)(2)(ii). | Std + A | Review provider physical-control reports; secure hospital locations housing PHI-access systems and supporting infrastructure. | Facilities/Legal: facility plan and provider assurance review. |
| S28 | Physical access validation/visitor control. 164.310(a)(2)(iii). | A | Use hospital badging, visitor escorts, and controlled maintenance access. Databricks login authorization does not satisfy physical visitor control. | Facilities: access/visitor records. |
| S29 | Security-related facility maintenance records. 164.310(a)(2)(iv). | A | Track changes to doors, locks, physical network equipment, and relevant hospital facilities; obtain provider assurance for provider-managed facilities. | Facilities: maintenance/change records. |
| S30 | Workstation use. 164.310(b). | Std | Define appropriate uses and surroundings for PHI-access endpoints; prohibit unattended displays and unmanaged PHI copies; govern remote work and printing. | Security/HR: workstation-use policy. |
| S31 | Workstation physical security. 164.310(c). | Std | Restrict physical access to endpoints; use secured areas, screen locks, device encryption, and managed remote access. Device-compliance checks supplement physical safeguards. | Security/Facilities: endpoint and site evidence. |
| S32 | Device/media disposal. 164.310(d)(1), (d)(2)(i). | Std + R | Sanitize hospital media; review Azure hardware disposal assurances; inventory and retire PHI in exports, local disks, storage versions, caches, and backups under retention/legal-hold policy. | Security/Platform: disposal approvals and certificates. |
| S33 | Media reuse. 164.310(d)(2)(ii). | R | Verify sanitization before reuse of hospital devices/media; require appropriate cloud-provider handling of released compute/storage. | Security/Platform: sanitization procedure and evidence. |
| S34 | Media accountability. 164.310(d)(2)(iii). | A | Track custodians and movement of portable media and PHI exports. Prefer governed storage and prohibit uncontrolled removable-media use. | Security/Data: asset/transfer inventory. |
| S35 | Exact backup before equipment movement when needed. 164.310(d)(2)(iv). | A | Assess PHI on endpoints/appliances being moved; back it up and verify retrievability before movement when required by the risk assessment. | Platform/Security: movement and backup records. |

### 4.3 Technical safeguards

Source basis: [L06]. Mechanisms: [D01-D14, D17, D18, D20-D22]. The hospital must validate behavior across every approved client and execution path.

| ID | Requirement and legal citation | Class | Proposed implementation | Accountable owner; evidence |
| --- | --- | --- | --- | --- |
| S36 | Authorized technical access. 164.312(a)(1). | Std | Enforce Unity Catalog grants, supported filters/masks or governed views, workspace bindings, workspace/compute ACLs, and storage/network restrictions. Separate policy administrators from consumers. | Data/Platform: end-to-end authorization tests. |
| S37 | Unique user identification. 164.312(a)(2)(i). | R | Use individual Entra identities and distinct service principals; prohibit shared interactive logins; record initiating user and run-as identity for automated jobs/applications. | Platform/Security: identity inventory and attribution tests. |
| S38 | Emergency access procedure. 164.312(a)(2)(ii). | R | Provide documented, attributable emergency access with justified scope, tested prerequisites, alerts, and post-use review. Distinguish clinical PHI access from tenant recovery accounts. | Clinical/Security: emergency-access drill and review. |
| S39 | Automatic logoff. 164.312(a)(2)(iii). | A | Configure and test actual idle-session termination in applicable browser/VDI/client paths, supplemented by endpoint lock and reauthentication. If a client lacks required behavior, document and validate an appropriate alternative. Compute auto-termination is not user logoff. | Security/Platform: inactivity tests and addressable decision. |
| S40 | Encryption/decryption. 164.312(a)(2)(iv). | A | Encrypt every PHI location, including managed services, customer ADLS, workspace storage, disks, results, exports, and backups. This design uses managed-services CMK plus scoped Azure storage/disk key controls where supported. | Platform/Security: location/key inventory and restore tests. |
| S41 | Audit controls. 164.312(b). | Std | Record and examine risk-relevant access and administrative activity. Combine Databricks audit sources, query history where covered, application disclosure events, and Azure/identity/storage/key telemetry. Preserve selected evidence independently. | Security: coverage, collection-health, and tamper tests. |
| S42 | Protect against improper alteration/destruction. 164.312(c)(1). | Std | Limit writes and destructive operations; use reviewed deployments, Delta transaction controls, source reconciliation, integrity checks, and independent recovery copies. | Data/Platform: change approvals and integrity tests. |
| S43 | Authenticate ePHI integrity. 164.312(c)(2). | A | Corroborate trusted source identity and data integrity using authenticated transfers, checksums/manifests where appropriate, reconciliation, and versioned provenance. Delta ACID alone does not prove clinical accuracy or an authorized source. | Data/Security: integrity/reconciliation records. |
| S44 | Person/entity authentication. 164.312(d). | Std | Authenticate humans through approved Entra flows; enforce hospital MFA/Conditional Access baseline and managed devices. Authenticate workloads with supported OAuth/managed-identity patterns; restrict credential access and replay risk. | Security/Platform: authentication and negative tests. |
| S45 | Transmission security. 164.312(e)(1). | Std | Inventory and secure every PHI transfer: ingestion, control-plane communication, JDBC/ODBC, APIs, streaming, intra-cluster paths, exports, and external recipients. Restrict ingress/egress by compute plane. | Platform/Security: flow matrix and connection tests. |
| S46 | Transmission integrity. 164.312(e)(2)(i). | A | Require certificate validation and authenticated protocols; use integrity-protected transport plus content validation/reconciliation for clinical exchanges. Reject tampered or untrusted input. | Data/Platform: tamper and certificate-failure tests. |
| S47 | Transmission encryption. 164.312(e)(2)(ii). | A | Require encrypted PHI transport, including customer code and non-Databricks systems; profile communications use TLS 1.2+. Private routing is not a substitute for encryption. | Platform/Security: encryption settings and traffic-path evidence. |

### 4.4 Organizational, policy, and documentation requirements

Source basis: [L07, L08].

| ID | Requirement and legal citation | Class | Proposed implementation | Accountable owner; evidence |
| --- | --- | --- | --- | --- |
| S48 | BA contracts, other arrangements, and subcontractor contracts. 164.314(a)(1)-(2). | R | Agreements must address Security Rule compliance, subcontractor assurances, and security-incident/breach reporting. Review qualifying government/other arrangements rather than assuming exemptions. Pair with the Privacy Rule contract terms in P40. | Legal/Security: approved contracts and incident provisions. |
| S49 | Group-health-plan sponsor safeguards/separation. 164.314(b)(1)-(2). | R, conditional | If the hospital's employee health plan/sponsor receives relevant ePHI, amend plan documents and enforce separation, agent assurances, and incident reporting. Do not expose employee-plan PHI to HR merely because the hospital hosts the platform. | Legal/HR/Platform: plan documents and isolation tests. |
| S50 | Reasonable/appropriate policies and procedures. 164.316(a). | Std | Maintain approved hospital policies and operating procedures; express technical portions as reviewed configuration/IaC and control operating instructions. | Security/Platform: approved policy set. |
| S51 | Written/electronic documentation. 164.316(b)(1). | Std | Retain required policies and records of required documented actions/assessments in a governed evidence repository, with owners and effective dates. | Security: evidence index and access controls. |
| S52 | Documentation retention. 164.316(b)(2)(i). | R | Retain required Security Rule documentation six years from creation or last effective date, whichever is later; apply longer applicable holds/requirements. | Security/Legal: retention settings and sample records. |
| S53 | Documentation availability. 164.316(b)(2)(ii). | R | Make current procedures and necessary evidence available to responsible implementers and authorized reviewers without broadly exposing PHI-bearing evidence. | Security: access and retrieval test. |
| S54 | Documentation updates. 164.316(b)(2)(iii). | R | Review documentation periodically and after changes to technologies, environment, risk, or procedures; maintain version/effective-date history. | Security/Platform: revision and change records. |

## 5. Privacy Rule requirements and patient-rights matrix

The Privacy Rule applies to PHI in electronic, paper, and oral forms. Databricks supports selected data operations; the hospital remains accountable for the legal basis, patient communication, and decisions. Sources: [L02, L09-L15, L17-L20, L28]; mechanisms: [D05-D08, D12-D16, D19-D21].

| ID | Requirement and legal citation | Proposed Databricks implementation and hospital process | Owner; evidence |
| --- | --- | --- | --- |
| P01 | Limit uses/disclosures to permitted or required purposes. 164.502(a). | Associate each dataset, pipeline, consumer, and release with an approved purpose/legal basis. Default-deny unrelated access; a BAA or SQL privilege is not patient authorization. | Privacy/Data: purpose and release register. |
| P02 | Minimum necessary uses, requests, and disclosures. 164.502(b); 164.514(d). | Define role/data-category limits, recurring request/release protocols, and individual review for nonroutine cases. Justify whole-record use where applicable. Encode approved cohort/column restrictions. Preserve the statutory exceptions, including treatment-provider disclosures/requests and individual access. | Privacy/Data: role matrix, protocols, exception tests. |
| P03 | Treatment, payment, health care operations (TPO). 164.506. | Validate that each clinical/payment/operations use fits the rule, including conditions for another entity's operations and an organized health care arrangement. Do not call unrelated research, commercial model training, or marketing TPO by default. | Privacy/Clinical: purpose determinations. |
| P04 | Incidental uses/disclosures only with appropriate underlying permission and safeguards. 164.502(a)(1)(iii); 164.530(c). | Limit PHI in displays, logs, email alerts, shared dashboards, support tickets, and exports. Use synthetic examples; review output and collaboration permissions. | Privacy/Platform: output reviews and safeguards. |
| P05 | Required individual/HHS disclosures. 164.502(a)(2), (a)(4); 160.310. | Enable authorized record production and regulator evidence collection through controlled jobs/repositories; do not use private endpoints or encryption to obstruct lawful access. | HIM/Legal: production and regulator-request records. |
| P06 | Verify recipient identity and authority and required supporting representations. 164.514(h). | Have release workflows verify patients/representatives and external requesters before export, subject to the rule's exceptions. Entra authentication alone does not verify legal authority over a patient's records. | HIM/Privacy: identity/authority verification records. |
| P07 | Personal representatives, minors, and endangerment exceptions. 164.502(g). | Integrate validated representative relationships and state-law minor restrictions into the patient/release system; constrain datasets and views accordingly. Require professional review for abuse/endangerment exceptions. | Privacy/Clinical: authority and exception decisions. |
| P08 | Decedent PHI protection for 50 years after death. 164.502(f). | Maintain death-status/privacy classification for as long as retained PHI is protected; do not automatically make deceased-patient datasets public. This is a protection period, not a universal records-retention mandate. | Privacy/Data: classification and retention rationale. |
| P09 | Valid authorizations and copies to individuals. 164.508(a)-(c). | Use plain-language forms capturing the specific information, discloser, recipient, purpose, expiration, signature/date, representative authority, and required statements in the hospital authorization system. Enforce scope at job/release time; retain signed documents and provide required copies. | HIM/Privacy: approved forms and scope tests. |
| P10 | Defects, revocation, compound authorizations, and conditioning limitations. 164.508(b). | Reject known-invalid/expired/revoked authorizations; propagate status to future processing/releases. Respect reliance and insurance exceptions; have Legal review compound forms and permitted conditioning rather than inventing blanket consent requirements. | Privacy/Legal: revocation tests and form review. |
| P11 | Psychotherapy-note authorization restrictions. 164.508(a)(2). | Identify actual psychotherapy notes using the regulatory definition; isolate from ordinary clinical notes, general analytics, and models. Release only with authorization or a specifically applicable exception. | Privacy/Clinical: classification and authorization tests. |
| P12 | Marketing authorization and remuneration statements. 164.508(a)(3); 164.501. | Separate marketing audiences from care operations; validate exceptions and financial-remuneration language. Do not feed hospital PHI to marketing vendors merely because they have workspace access. | Privacy/Legal: marketing decision and release records. |
| P13 | Sale of PHI and remuneration-specific authorization. 164.502(a)(5)(ii); 164.508(a)(4). | Block commercial sharing/monetization unless an applicable exception or valid authorization is documented. Assess indirect remuneration; do not assume a sharing feature confers permission. | Privacy/Legal: transaction and authorization review. |
| P14 | Facility directory agreement/objection. 164.510(a). | Feed directory extracts only from approved directory preferences and permitted fields/recipients; apply incapacity/emergency rules through clinical procedures. A full hospital analytics dataset is not a directory. | Clinical/Privacy: preferences and feed tests. |
| P15 | Family/caregiver involvement, notifications, disaster relief. 164.510(b). | Limit release to relevant information using agreement/non-objection or applicable professional-judgment rules; respect known preferences for decedents. Use purpose-specific outputs, not unrestricted caregiver access. | Clinical/HIM: decisions and release evidence. |
| P16 | Safe Harbor de-identification. 164.514(a), (b)(2). | Implement removal/generalization of all 18 identifier categories, including text/images, dates, age and geography rules, plus the no-actual-knowledge condition. Validate the final release; hashing names/MRNs alone is not Safe Harbor. | Privacy/Data: transformation tests and release sign-off. |
| P17 | Expert Determination de-identification. 164.514(b)(1). | Use a qualified expert's documented methods/results and very-small re-identification-risk determination for the intended recipient/context. Implement the approved transformations and release conditions in pipelines. | Privacy/expert/Data: expert report and pipeline validation. |
| P18 | Re-identification-code safeguards. 164.514(c); 164.502(d). | If using the regulatory code mechanism, use a code not derived from individual information and protect the mechanism; separate mapping keys from analytic consumers. Ordinary pseudonymization/tokenization is not automatically de-identification. | Privacy/Data: code design and separation tests. |
| P19 | Limited data sets and data use agreements. 164.514(e). | Remove required direct identifiers; restrict use to research, public health, or operations; obtain the DUA with purpose, recipients, safeguards, reporting, agent, and no-reidentification/contact terms. Cure violations; discontinue/report when required. Limited data sets remain PHI. | Privacy/Legal: DUA and release validation. |
| P20 | Fundraising scope and opt-out. 164.514(f); 164.520(b)(1)(iii). | Restrict extracts to eligible fields and recipients, include the NPP statement, provide compliant opt-out, suppress opted-out recipients, and do not condition treatment/payment on participation. Apply additional Part 2 rules where relevant. | Privacy/foundation: suppression and notice tests. |
| P21 | Health-plan genetic-underwriting prohibition and underwriting-purpose limits. 164.502(a)(5)(i); 164.514(g). | If health-plan functions exist, prevent prohibited genetic-information use/disclosure for underwriting and restrict unsuccessful enrollment/placement data to permitted purposes. Preserve the long-term-care issuer exception where applicable. | Legal/health plan/Data: purpose and prohibition tests. |
| P22 | NPP content, actual practices, revisions, applicable Part 2 statements. 164.520(b); 164.502(i). | Hospital publishes a plain-language notice describing uses, rights, duties, breach notification, complaints, contact, and effective date. Match data uses to it; include applicable February 16, 2026 updates, not vacated reproductive provisions. | Privacy/Legal: current NPP and consistency review. |
| P23 | NPP delivery, acknowledgment, posting, electronic/paper availability and documentation. 164.520(c)-(e). | Use hospital intake/portal/site processes: first-service delivery, good-faith written acknowledgment with failure documentation, emergency timing, posting, requested paper copies, and version retention. Apply plan-specific delivery rules if relevant. | HIM/Privacy: notices, acknowledgments, posting evidence. |
| P24 | Requested/agreed restrictions, emergency exceptions, and termination rules. 164.522(a). | Accept requests; encode agreed restrictions in governed eligibility/release tables and downstream extracts. Permit only applicable emergency/other exceptions, record decisions, and implement lawful prospective termination rules. | Privacy/Data: restriction register and tests. |
| P25 | Mandatory restriction for fully self-paid services. 164.522(a)(1)(vi). | When requested and conditions apply, exclude those service details from health-plan payment/operations releases unless disclosure is otherwise required by law. Enforce at encounter/service level across billing, data lake, and payer extracts. | Revenue/Privacy/Data: payment/restriction and exclusion tests. |
| P26 | Reasonable confidential communications. 164.522(b). | Honor hospital-provider requests for alternative means/locations without requiring a reason; account for different health-plan conditions. Keep communications preferences out of general notebook/email outputs and apply them to patient communications. | HIM/Privacy: preference and delivery tests. |
| P27 | Access to PHI in designated record sets; timely action and documentation. 164.524(a), (b), (e). | HIM determines record-set scope, including analytics used to make individual decisions; retrieve applicable electronic PHI in time. Act within 30 days; only one additional 30-day extension with timely written reason/date. | HIM/Data: record-set inventory and access-request tracking. |
| P28 | Access format, copies, cost-based fees, limited denials and independent review. 164.524(c)-(d). | Provide requested readily producible form or agreed readable alternative; supply other accessible information when partially denied and handle required denial/review notices. Do not bill search/retrieval as permissible copy labor. Apply third-party direction/fee rules with the Ciox limitation, not the unqualified older text. | HIM/Legal: export, fee schedule, denial/review records. |
| P29 | Right to amendment, grounds for denial, and timely action. 164.526(a)-(b), (f). | Maintain HIM request workflow with 60-day action and one 30-day written extension. A clinician/HIM decision, not a pipeline, determines whether amendment is appropriate. | HIM/Clinical: request/decision tracking. |
| P30 | Accepted/disputed amendments and propagation. 164.526(c)-(e). | Append/link amendments; inform individuals and appropriate recipients/BAs; process incoming amendments. Link disagreements/rebuttals and required future-disclosure material. Reconcile downstream tables, scores, exports, and relevant model inputs. | HIM/Data: linked amendments and downstream reconciliation. |
| P31 | Accounting timing, charges, exceptions and documented responsibility. 164.528(a), (c)-(d). | Provide an accounting within 60 days, with one documented 30-day extension; first request in 12 months is free. Apply current regulatory exclusions and lawful suspensions; have Legal review the statutory EHR accounting distinction in section 7. | HIM/Privacy: workflow, fee, and exception tests. |
| P32 | Accounting content and six-year lookback. 164.528(b), (d). | Maintain a protected disclosure ledger with date, recipient/address if known, PHI description, purpose, and relevant BA disclosures. Support applicable repeated-disclosure/research alternatives. Internal query logs alone do not supply the required patient-specific accounting. | Privacy/Data: six-year ledger and sample accounting. |
| P33 | Privacy official and complaint/information contact. 164.530(a). | Designate and document hospital personnel; publish contact channels. Databricks admins are not automatically the privacy official. | Hospital leadership: designations. |
| P34 | Role-appropriate privacy/breach training and records. 164.530(b). | Train new workforce members within a reasonable period and affected staff after material changes; document delivery. Include patient rights and disclosure handling, not only cybersecurity. | HR/Privacy: training records. |
| P35 | Privacy safeguards, including incidental exposure. 164.530(c). | Protect oral/paper PHI and electronic screens, reports, prints, notebook outputs, shared content, and emails. Govern downstream BI and local copies separately. | Privacy/Security: operational safeguards. |
| P36 | Complaint process and dispositions. 164.530(d). | Maintain a hospital complaint channel and protected case records; collect relevant audit evidence without granting complainants broad system-table access. | Privacy: complaint register. |
| P37 | Apply appropriate sanctions and mitigate harmful effects. 164.530(e)-(f). | Investigate and document sanctions, respecting protected-disclosure exceptions; contain improper sharing, notify appropriate parties, and mitigate known harm. | HR/Privacy/Security: protected case/action evidence. |
| P38 | No prohibited retaliation/intimidation or required waiver of rights. 164.530(g)-(h); 160.316; 164.502(j). | Preserve lawful whistleblower/crime-victim reporting pathways; do not sanction protected actions or make treatment depend on waiving HIPAA rights. | Legal/HR/Privacy: policy and case review. |
| P39 | Policies, legal/practice changes, written documentation and retention. 164.530(i)-(j). | Approve and version privacy/breach procedures and required records; coordinate NPP changes before affected practice changes where required. Retain required documentation six years from creation/last effectiveness, whichever is later. | Privacy/Legal: policy history and retention evidence. |
| P40 | Privacy Rule BA contract duties and response to material violations. 164.504(e). | Contracts address permitted uses, safeguards, improper-use/breach reporting, subcontractors, access, amendments, accounting, delegated duties, HHS records access, return/destruction or continuing protection if infeasible, and termination. Take required cure/termination steps for known material patterns. | Legal/Privacy: contract review and vendor monitoring. |
| P41 | Hybrid/affiliated entities, multiple functions, group plans and OHCA boundaries. 164.105; 164.504(f)-(g); 164.506; 164.520(a); 164.530(k). | Document covered components and qualifying arrangements. Isolate health-plan/clearinghouse/research/employment functions where required; amend plan documents and restrict sponsor uses. Apply conditional notice/administrative exceptions, not blanket organizational sharing. | Legal/Privacy/Platform: boundary/plan documents and access tests. |
| P42 | Legacy permissions and compliance-date provisions. 164.532-.534; 164.318. | If relying on legacy research permissions, verify their limited transitional conditions and later events affecting reliance. Historic compliance dates do not waive present compliance; retain a documented legacy applicability decision. | Legal/HIM: legacy-permission register or non-applicability decision. |

**No general HIPAA erasure right:** Patient amendments usually require appending/linking and appropriate propagation, not indiscriminate record deletion. Medical-record retention periods arise from other applicable requirements and hospital policy; the HIPAA six-year documentation rule is not a universal six-year medical-record or raw-log retention mandate. [L08, L13-L15]

### 5.1 Disclosure permissions requiring specific legal/clinical conditions

These are **conditional permissions**, not requirements to disclose every time a requester asks. A separate law can require a disclosure. The hospital must verify the applicable conditions, recipient authority, allowed scope, and restrictions before releasing data. None of these permissions grants a requester unrestricted Databricks access. Source: 164.512 [L02, L09]; court-order caveat: [L19].

| ID | Category and citation | Hospital decision and platform implementation | Owner; evidence |
| --- | --- | --- | --- |
| DCL01 | Required by law. 164.512(a). | Limit the release to the law's actual requirement; apply associated abuse/judicial/law-enforcement conditions where required. Generate an approved, scoped extract. | Legal/HIM: authority and extract record. |
| DCL02 | Public health. 164.512(b). | Validate reporting authority and destination for disease, child abuse, vital events, FDA safety/recall activities, exposures, and qualifying occupational surveillance. For school immunization proof, obtain/document the required agreement. | Clinical/Privacy: authority, agreement where applicable, feed validation. |
| DCL03 | Abuse, neglect, domestic violence. 164.512(c). | Apply legal/consent/professional-judgment criteria and required individual notice with applicable safety exceptions; release only approved information. | Clinical/Legal: protected decision and notice record. |
| DCL04 | Health oversight. 164.512(d). | Verify authorized oversight and exclusions; provide approved audit/licensure/regulatory records, not general-purpose investigator accounts. | Legal/Privacy: authority and disclosure record. |
| DCL05 | Judicial/administrative proceedings. 164.512(e). | Review orders and their scope; for qualifying non-order process, obtain the necessary notice/protective-order assurances or make required reasonable efforts. Track return/destruction conditions. | Legal/HIM: order/assurances and scoped export. |
| DCL06 | Law enforcement. 164.512(f). | Assess the applicable process, identification, victim, decedent, premises-crime, or emergency exception. Identification/location releases have limited fields and exclude specified biological/DNA data. | Legal/Clinical: basis and restricted extract. |
| DCL07 | Coroners, medical examiners, funeral directors. 164.512(g). | Confirm duties/authority and necessary scope; implement purpose-specific release templates. | HIM/Legal: requester and release record. |
| DCL08 | Cadaveric organ/eye/tissue donation. 164.512(h). | Validate procurement/transplant purpose and recipient; restrict transfer to the approved workflow. | Clinical/Privacy: purpose and transfer evidence. |
| DCL09 | Research without individual authorization. 164.512(i). | Obtain documented IRB/privacy-board waiver/alteration meeting all criteria, or qualifying preparatory/decedent representations. Preparatory review prohibits researcher removal of PHI. Bind datasets, study access, outputs, and retention to the approval. | Research/Privacy: signed waiver or representations and study tests. |
| DCL10 | Serious threats to health/safety. 164.512(j). | Document good-faith, legally/ethically permitted professional judgment and the appropriate recipient/scope; observe special law-enforcement restrictions. | Clinical/Legal: decision and scoped release. |
| DCL11 | Specialized government functions. 164.512(k). | Individually assess military/veterans, national security, protective services, State Department suitability, custody, public-benefits coordination, and qualifying NICS functions. Record non-applicability for hospital roles that do not qualify. | Legal/Privacy: authority/role assessment and release record. |
| DCL12 | Workers' compensation. 164.512(l). | Validate the applicable law and permitted necessity; use approved injury/benefit extracts with appropriate scope. | Legal/Revenue: legal basis and release evidence. |

Research waiver implementation must retain the board identity, approval date, signature, review procedure, approved PHI scope, privacy-risk findings, identifier-protection/destruction plan, reuse/disclosure assurances, and impracticability findings. Implementing a study catalog does not itself satisfy those criteria. [L09]

### 5.2 De-identification implementation details

Build separate **PHI**, **limited-data-set**, and **validated de-identified** release paths. Do not classify data as de-identified merely because the UI masks it. The underlying data and recoverable identifier mappings may remain PHI. [L09, L18]

For Safe Harbor, the pipeline's validation must cover the following 18 identifier categories for the individual and relevant relatives, employers, or household members:

| # | Identifier category | Proposed pipeline action |
| --- | --- | --- |
| 1 | Names | Remove from structured fields and free text. |
| 2 | Geography below state level | Remove; apply the specific three-digit ZIP population exception and 000 substitution rule where eligible. |
| 3 | Individual-related date elements other than year; ages over 89 | Generalize dates and aggregate ages 90+; address date/year elements revealing those ages. |
| 4 | Telephone numbers | Remove, including narrative occurrences. |
| 5 | Fax numbers | Remove. |
| 6 | Email addresses | Remove. |
| 7 | Social Security numbers | Remove. |
| 8 | Medical-record numbers | Remove. |
| 9 | Health-plan beneficiary numbers | Remove. |
| 10 | Account numbers | Remove. |
| 11 | Certificate/license numbers | Remove. |
| 12 | Vehicle identifiers/serials/license plates | Remove. |
| 13 | Device identifiers/serials | Remove from clinical/device/imaging metadata. |
| 14 | URLs | Remove. |
| 15 | IP addresses | Remove. |
| 16 | Biometric identifiers | Remove finger/voiceprint and equivalent identifying features. |
| 17 | Full-face photographs/comparable images | Remove or transform under an approved methodology; inspect image pixels as well as headers. |
| 18 | Other unique identifiers/characteristics/codes | Remove unless the specific re-identification-code provision is satisfied. |

Also check the no-actual-knowledge condition. Validate narrative text, DICOM/header content, embedded images, rare combinations, and downstream releases; consider Expert Determination when useful dates, geography, or image features cannot meet Safe Harbor. The actual transformations and release validation are customer-developed controls, not a Databricks HIPAA certification.

## 6. Breach Notification Rule matrix

Source basis: 164.400-.414 [L16]. Logs and queries support investigation; the hospital/vendor process performs assessment and required notification.

| ID | Requirement and legal citation | Proposed implementation | Owner; evidence |
| --- | --- | --- | --- |
| B01 | Breach definition, exceptions, presumption, risk assessment. 164.402. | Treat impermissible PHI acquisition/access/use/disclosure as presumed breach unless an applicable exception or documented low-probability assessment supports otherwise. Assess PHI/identifiers, recipient, actual access/acquisition, and mitigation. | Privacy/Legal/Security: four-factor assessment and decision. |
| B02 | Determine whether PHI was unsecured. 164.402; HHS guidance. | Evaluate qualifying encryption/destruction and whether keys or decrypted data were exposed. At-rest encryption does not eliminate breach duties for compromised authenticated access or plaintext exports. | Security/Legal: key/access and encryption analysis. |
| B03 | Discovery and notice timing. 164.404(a)-(b). | Record first actual or reasonable-diligence discovery, including workforce/agent knowledge under the rule. Start the clock then; do not wait for forensic completion. Notify without unreasonable delay, no later than 60 calendar days, subject to lawful delay. | Privacy/Legal: chronology and notifications. |
| B04 | Individual notice content and method. 164.404(c)-(d). | Hospital sends plain-language required event/date, PHI-type, protective-step, mitigation, and contact information by first-class mail or agreed email; handle deceased individuals, substitute notice, and urgent additional contact as applicable. | Privacy/HIM: approved notices and delivery records. |
| B05 | Substitute notice thresholds. 164.404(d)(2). | For fewer than 10 unreachable people, use an allowed alternative; for 10+, use qualifying 90-day web/media notice and a toll-free number active at least 90 days. Validate next-of-kin exceptions. | Privacy/communications: posting and phone evidence. |
| B06 | Media notice. 164.406. | For **more than 500 residents of a state/jurisdiction**, notify prominent media serving it without unreasonable delay and within 60 calendar days. This threshold differs from HHS's 500-or-more threshold. | Privacy/communications: geography counts and notice. |
| B07 | HHS reporting. 164.408. | For **500 or more individuals**, report contemporaneously with individual notice in the required HHS manner. For fewer than 500, log and report within 60 days after the end of the year of discovery. Individual notice is not deferred for small breaches. | Privacy/Legal: counts, breach log, submission proof. |
| B08 | BA-to-covered-entity notice. 164.410. | Require vendor reporting without unreasonable delay, no later than 60 calendar days, with affected individuals and available information; negotiate shorter operational incident notification where possible without confusing it with the statutory ceiling. | Legal/Security: contract and vendor notification record. |
| B09 | Law-enforcement delay. 164.412. | Implement only an authorized documented delay: specified written period, or documented oral request limited to 30 days unless written support follows. Internal investigation is not such a delay. | Legal: official request and revised clock. |
| B10 | Administrative requirements and burden of proof. 164.414; 164.530. | Retain proof of required notices or why the event was not a breach; include training, complaints, sanctions, nonretaliation, policies, and documentation. Freeze relevant evidence under legal hold. | Privacy/Legal: complete protected case file. |

## 7. HITECH obligations and effects

HITECH is not a separate Databricks switch. Many operative requirements are already reflected in the amended HIPAA rules above. [L02, L04, L16, L27, L31-L33]

| ID | HITECH provision/effect | Implementation mapping |
| --- | --- | --- |
| H01 | Direct BA Security Rule responsibility and liability; section 13401. | G03, S25, S48, P40: verify agreements, scope, and supplier controls; do not assume all responsibility transfers to Microsoft or Databricks. |
| H02 | Breach notification; section 13402. | B01-B10: implement discovery, assessment, vendor escalation, patient/media/HHS notice, and evidentiary procedures. |
| H03 | Privacy-contract compliance by BAs; section 13404. | P01, P40: constrain vendor purposes and delegated functions; flow down applicable restrictions to subcontractors. |
| H04 | Patient-requested fully self-paid restrictions, minimum necessary, EHR disclosure accounting, electronic access, remuneration limits; section 13405. | P02, P13, P25, P27-P32: encode service-level payer suppression, scoped access, electronic production, sale controls, and accounting. Apply Ciox-limited third-party rules and the accounting distinction below. [L33] |
| H05 | Marketing/fundraising changes; section 13406. | P12, P20: authorization/remuneration handling and reliable fundraising opt-out suppression. [L33] |
| H06 | Enforcement, audits, and penalties; sections 13409-13411 and Part 160. | Maintain operational evidence, cooperate with authorized investigations/audits, and correct failures. Do not hard-code civil penalty amounts, which change through adjustments. [L01, L33] |
| H07 | Recognized security practices; 2021 amendment, section 13412, codified at 42 USC 17941. | Preserve evidence of actual implementation over the prior 12 months for potential enforcement consideration. Use NIST/HHS practices to strengthen the program; this is not immunity or a mandatory product certification. [L26, L29, L31] |
| H08 | Separate health-IT/PHR program obligations where applicable. | Determine whether hospital EHR programs or separate non-HIPAA PHR products trigger additional provisions. Databricks is not automatically a certified EHR, and the FTC PHR breach regime is not a replacement for the hospital's HIPAA breach duties. Assess outside this PHI-platform baseline. [L32] |

**EHR accounting distinction:** HITECH section 13405(c), codified at 42 USC 17935(c), addresses expanded EHR disclosure accounting. The current 164.528 regulation still contains the TPO accounting exclusion. This matrix's operational baseline follows the current regulation; counsel must evaluate the statutory provision and HHS implementation status for the hospital rather than treating the statutory language as merely a proposal. No finalized expanded accounting rule was identified in this research. Do not equate that distinction with permission to omit risk-appropriate activity evidence or assume that every Databricks query is itself a reportable patient disclosure. [L14, L33]

## 8. Electronic transactions, identifiers, and regulatory cooperation

Conditional to the hospital's actual functions and interfaces. Databricks may transform/analyze the data, while a hospital EDI/EHR/clearinghouse system remains the standards-compliant transaction endpoint. [L01, L24, L25, L30]

| ID | Requirement | Implementation and evidence |
| --- | --- | --- |
| A01 | Standard transactions and operating rules; Part 162 transaction subparts. | Preserve/validate applicable claims or encounters, eligibility, referral authorization, claim status, enrollment/disenrollment, payment/remittance, premium payment, and coordination-of-benefits exchanges. Use qualified EDI components; keep conformance/error evidence. |
| A02 | Medical/nonmedical code sets and identifiers. Part 162, including code-set, provider, and employer requirements. | Maintain effective-dated terminology and required NPI/EIN fields where applicable; validate mapping/version changes before release. Databricks tables do not automatically validate licensed X12/code-set implementation guides. |
| A03 | Trading-partner agreements and clearinghouse responsibilities. 162.915, 162.923, 162.930 and applicable standards. | Do not use agreements/custom formats to defeat standard transactions or operating rules. Validate translation and partner interfaces; retain partner conformance and responsibility records. |
| A04 | Finalized claims attachments/electronic signatures, future compliance date. [L25] | Plan applicable X12 275/277 Version 6020, adopted HL7 C-CDA/attachments guides, and the claims-attachment electronic-signature standard for May 26, 2028 compliance. Limit this requirement to its actual transaction scope. |
| A05 | HHS investigation cooperation, records, and access. 160.310; enforcement provisions. | Produce requested reports, records, and compliance evidence through Legal-approved channels; preserve legal holds and required access. Use a protected evidence repository rather than broad regulator/admin credentials. |
| A06 | Complaints/nonretaliation and remediation under Part 160. 160.306, 160.316 and applicable enforcement provisions. | Provide lawful complaint/cooperation channels and corrective-action handling. Retain evidence supporting claims of compliance and any corrective actions; do not advertise policy-dashboard scores as legal findings. |

## 9. Recommended reference architecture

This is a proposed architecture, not a statement that HIPAA mandates this topology.

```text
Hospital EHR / imaging / laboratory / revenue systems
  -> authenticated, encrypted, approved ingestion
  -> restricted Azure storage landing/quarantine boundary
  -> HIPAA-profile Azure Databricks workspace
       -> Unity Catalog PHI catalogs and controlled storage identities
       -> reviewed transformation jobs
       -> purpose-scoped clinical / operations / approved research datasets
       -> validated limited-data-set or de-identified release pipeline
  -> authorized hospital applications / BI / recipient release endpoint

Cross-cutting:
  Entra identities + strong authentication + managed hospital endpoints
  separate policy/admin, workload, data-consumer, and key-management roles
  private/restricted connectivity appropriate to each compute plane
  Databricks + Azure + application evidence -> protected monitoring/archive
  independent recovery copies + compliant DR environment + tested runbooks
  hospital consent/restrictions, rights, disclosure, and incident workflows
```

| Layer | Proposed design | Basis and limitation |
| --- | --- | --- |
| Environment separation | Separate production PHI, nonproduction synthetic/de-identified, and recovery boundaries. Any environment receiving PHI gets the same applicable protections. Use dedicated subscriptions/workspaces/storage identities when risk warrants. | Workspace separation alone does not remove inherited catalog/storage access. [D05] |
| Identity | Individual Entra identities, account-level provisioning, narrow service principals, approved OAuth automation, hospital MFA/Conditional Access, device controls, and monitored emergency identities. | Choose supported account-level SCIM or approved automatic identity management; do not blindly deploy both or rely on legacy workspace SCIM. Test API/token paths as well as browser login. [D17, D18, D22] |
| Governance | Unity Catalog object privileges, workspace bindings, restricted ownership/policy-management roles, table-level filters/masks or governed views; optional approved ABAC for scaling. | Runtime, access-mode, tag, and interface requirements matter. Users with direct storage credentials or policy-changing authority can defeat intended governance. [D05-D08] |
| Storage | Dedicated governed ADLS locations and scoped Access Connector managed identities. Avoid direct analyst storage RBAC, account keys, broad SAS, legacy mounts, and production PHI in DBFS root. | Storage access uses the managed identity on behalf of Unity Catalog users. Consequently, do not assume a storage credential identifies the individual analyst; correlate and test attribution with Databricks activity records. [D08] |
| Classic compute networking | VNet injection, secure cluster connectivity/no public IP, private workspace/control-plane connectivity as applicable, private data endpoints, controlled DNS/routes/firewall egress. | Approved dependency endpoints remain necessary; validate each flow rather than claiming everything runs inside the hospital VNet. [D09] |
| Serverless networking | Admit only supported region/features; configure account NCC private endpoints to customer resources and enforce restrictive serverless egress policies. | Serverless runs in a Databricks-managed compute plane, not the injected hospital VNet. Classic firewall rules do not govern it. Private endpoints alone do not block all outbound internet access. [D10, D11] |
| Encryption and keys | Managed-services CMK, protected customer storage/workspace storage, supported disk encryption, transport encryption, scoped key permissions, key recovery, rotation procedures, and recovery tests. | CMK scope is feature-specific; classic managed-disk CMK does not apply to serverless disks. Verify all PHI-bearing locations rather than a single workspace checkbox. [D04] |
| Compute/code | Supported compliant runtimes/VMs; enforce approved compute policies, access modes, dependencies/init scripts, deployment identities, and maintenance windows. | Remove unrestricted creation where inappropriate. Updating a policy does not automatically update existing compute; enforce and verify changes. [D02, D03, D23] |
| Monitoring | Protect and collect risk-relevant platform, storage, identity, key, network, workload, and disclosure records; perform regular review and collection-health monitoring. | Audit and system data can themselves contain PHI. Restrict and encrypt any approved export/archive, and validate source coverage. [D12-D14] |
| Recovery | Use eligible managed DR where it fits; engineer coverage for external data/services and unsupported assets; maintain independent point-in-time recovery copies. | Replication may propagate deletion/corruption. DR, storage redundancy, Delta history, and shallow clone are not equivalent to independent recoverable backups. [D15] |
| Serving/export | Govern clinical/BI/app identities and purpose-scoped extracts; verify recipient authority and constraints at release time; maintain disclosure evidence and amendment propagation. | Databricks authorization stops being sufficient once a recipient holds an export, BI cache, model artifact, or separate application database. |
| Geography | Select approved US regions for this design; review provider control-plane locations, identities, telemetry, support, subprocessors, and DR destinations. | US-only residency is a hospital contractual/risk decision here, not a blanket HIPAA rule. Cloud use abroad requires appropriate legal/risk/contract analysis. [L17, D02] |

## 10. Platform limitations and failure modes to address

| Area | What must not be assumed | Required design response |
| --- | --- | --- |
| Legal authorization | A user with `SELECT` necessarily has a lawful reason to see every patient. | Purpose-based grants plus patient/service restrictions and hospital approval; consider application-level patient-context enforcement. |
| Administrative power | Filters, masks, and encryption prevent privileged administrators from changing policies or granting themselves access. | Separate duties; narrowly assign ownership/MANAGE/admin/storage/key roles; monitor privileged changes; test break-glass and evidence protection. [D05] |
| Direct storage | Unity Catalog controls data read through arbitrary external credentials. | Remove unnecessary direct storage access and keys/SAS; scope connector identity and networks; validate attempted raw-path/client bypass. [D06, D08] |
| Serverless egress precision | An allowed FQDN guarantees access only to that exact approved service. | Current documentation warns that FQDN filtering can allow other domains sharing the same IP and that some destinations are implicitly allowed. Review effective policy, shared-IP exposure, private endpoint changes, and approved sharing destinations; account for required restarts/propagation delays and test attempted exfiltration rather than trusting the allowlist's appearance. [D11] |
| Filters and ABAC | Every runtime/API/sharing/AI path enforces the same table policy. | Test each approved path against documented limitations. Source-table ABAC does not automatically apply to AI Search indexes; build separately governed indexes/releases. [D06, D07] |
| Labels and tags | A PHI classification tag itself blocks access or legally de-identifies the data. | Pair classifications with enforceable policies and validation; avoid actual patient information in tags/names. [D02, D05] |
| Browser/session controls | MFA sign-in frequency or cluster auto-termination equals idle user-session termination. | Measure actual idle behavior across browser, VDI, JDBC/ODBC, APIs, and applications; document the addressable choice. |
| Diagnostics | Azure Monitor diagnostic settings include all account/workspace audit events. | The reference explicitly identifies coverage differences and omission of account-level events from Azure diagnostics. Select additional approved sources and quantify coverage. [D12] |
| Audit system table | `system.access.audit` is GA, permanent, or sufficient by itself for accounting. | Current docs label it Public Preview, with a baseline 365-day free retention absent configurable-retention Beta; deleting a workspace removes events older than 14 days from that table. Verify HIPAA eligibility before PHI-bearing use and preserve required evidence before source retention/deletion. Some SQL-definition fields require account-admin or `databricks_pii_access` group membership; govern that access separately. [D13, D14] |
| Configurable system-table retention | A longer retention setting guarantees complete, recoverable evidence. | Current Beta offers a 395-day default and configurable 30-3,650-day retention for supported tables; documentation lists HIPAA among eligible standards. Confirm feature/region eligibility and actual settings. Increasing retention does not backfill deleted history, some schemas are excluded, and total-loss recovery of the full retained data is not guaranteed. Preserve independently recoverable required evidence and monitor retention changes. [D14] |
| Detection latency | System tables supply real-time SOC monitoring. | Current documentation says data updates throughout the day and does not support real-time monitoring. Use approved timely event sources for time-sensitive detection, measure latency/coverage, and monitor collection health; system tables can support retrospective analysis. [D14] |
| Query history | `system.query.history` records every Spark/RDD/file read or every affected patient. | Its documented scope covers SQL warehouses and serverless notebook/job queries. Add workload/storage/app events where needed; do not infer patient-level disclosures from query text alone. [D14] |
| Audit completeness | A platform event proves every row read or identifies the legal recipient/purpose. | Build protected application/release ledgers where patient-level disclosure evidence is needed; test source gaps and ingestion failure. HIPAA does not prescribe logging every cell, but controls must record/examine activity appropriate to risk. |
| Lineage | Automatic lineage is a complete immutable chain of custody across all systems. | Validate supported paths; register/map external dependencies separately; retain necessary provenance and version history. Lineage is not a substitute for audit/disclosure evidence. [D16] |
| Secret redaction | Secret scopes make privileged readers unable to retrieve credentials. | Readers/admins/creators may access secret values, and output redaction is not complete protection. Prefer managed identities; narrow scope ACLs and use separate compatible vaults where needed. [D21] |
| Key Vault scopes | A Databricks Key Vault-backed secret scope supports any Key Vault permission/network model. | Current documentation requires the vault access-policy model for those scopes, not Azure RBAC. Avoid weakening a hospital vault to accommodate the integration; choose a compatible isolated design. [D21] |
| Downloads/screenshots | Private Link, table masks, or SQL privileges prevent an authorized user copying data. | Restrict unnecessary exports/features; manage endpoints and downstream tools; supervise privileged output; use de-identified working data when possible. |
| Deletion | SQL `DELETE` physically removes every PHI copy immediately. | For authorized disposal, account for deletion vectors, retained files/time travel, caches, clones, indexes, exports, and backups. Use applicable `REORG ... APPLY (PURGE)` and `VACUUM` with safe retention/holds; separately address other copies. [D20] |
| AI/ML and partners | HIPAA workspace settings authorize all external LLMs, assistants, MCP servers, embeddings, prompts, or training uses. | Require feature/region/BAA/purpose approval; isolate artifacts and inference logs; block unapproved provider egress. Separately review outputs for disclosure and re-identification. |
| Emergency access | A break-glass tenant admin account ensures necessary clinical PHI is available. | Test actual clinical workflows, storage/keys, private routing, user authority, and downtime alternatives during representative failures. |
| Backups | Geo-redundancy, continuous replication, time travel, or a shallow clone guarantee recoverability after ransomware. | Protect independent recovery copies and credentials; prove recovery from logical deletion, corruption, key loss, and source loss. [D15] |
| Certification | SOC 2, HITRUST, a CSP profile, or an Azure Policy score proves the hospital meets all HIPAA rules. | Treat attestations/settings as evidence for particular controls; assess hospital procedures, contractual scope, patient rights, and actual operation separately. [D19] |

**Preview decision:** A public-preview system table is not automatically prohibited if explicitly approved for the HIPAA boundary, but it is not automatically permitted either. The current HIPAA documentation refers to a regional support matrix and warns that some entries may precede release. Capture the applicable approval/status and actual availability before depending on a preview feature. If documentation does not clearly cover a necessary feature, obtain vendor confirmation or use another supported control path; record any unresolved gap as a go-live blocker. [D01-D03, D13]

## 11. Deadline and retention reference

| Obligation | Current rule / design treatment |
| --- | --- |
| Individual record access | 30 days; one extension of up to 30 days with timely written reason/completion date. [L12] |
| Amendment request | 60 days; one extension of up to 30 days with timely written reason/completion date. [L13] |
| Accounting request | 60 days; one extension of up to 30 days; first accounting in a 12-month period free. [L14] |
| Disclosure accounting lookback | Six years, with current regulatory exclusions. [L14] |
| Required Security/Privacy documentation | Six years from creation or last effective date, whichever is later. [L08, L15] |
| Individual/BA breach notification | Without unreasonable delay and no later than 60 calendar days after discovery, subject to lawful delay. [L16] |
| Media breach notice | More than 500 residents of a state/jurisdiction; same 60-calendar-day ceiling. [L16] |
| HHS large-breach report | 500 or more individuals; contemporaneous with individual notification. [L16] |
| HHS smaller-breach report | Within 60 days after year-end for the calendar year of discovery. [L16] |
| Qualifying substitute breach notice | For 10+ unreachable individuals: specified 90-day posting/media and toll-free contact provisions. [L16] |
| Decedent privacy protection | 50 years after death; not a blanket retention requirement. [L09] |
| Medical-record and general raw-log retention | Determine separately from applicable law, contracts, legal holds, patient-rights needs, and risk. Do not label six years universally mandated by HIPAA. |
| Proposed hospital audit-evidence retention | Preserve audit evidence needed to substantiate required documented controls and investigations for the applicable periods. A conservative six-year selected-evidence archive is a design choice; minimize unnecessary PHI and validate costs/needs. |
| Databricks audit/query/lineage system retention | Baseline table-specific free retention commonly is 365 days. Eligible configurable-retention Beta changes supported tables to a 395-day default and permits 30-3,650 days. Verify the actual setting and the limitations in section 10; neither option automatically satisfies every hospital retention/recovery obligation. [D13, D14] |
| Part 2 / applicable updated NPP compliance | February 16, 2026, already in the past at the research date. [L20, L23] |
| Claims-attachment/electronic-signature compliance | May 26, 2028, for the finalized rule's scope. [L25] |

No fixed hospital RPO/RTO, universal annual HIPAA risk-assessment deadline, mandatory seven-year retention, US-only storage requirement, or mandatory particular MFA/CMK product is asserted by this report. Such baselines should come from the hospital's risk, clinical-safety, legal, and contractual decisions. Review frequencies and recovery targets must be explicitly approved and tested.

## 12. Production-readiness acceptance checklist

Use synthetic data first. Treat each applicable failed item as an unresolved control gap, not as compliant because a feature exists.

| Gate | Acceptance condition | Evidence / responsible party |
| --- | --- | --- |
| Contract and scope | Relevant BA relationships, in-scope services/features/regions and hospital purposes are documented; conditional obligations have approved applicability decisions. | Legal/Privacy: G01-G06, P40-P42. |
| Profile and prerequisites | HIPAA standard/profile, Premium/add-on, monitoring/update settings, supported VM types and applicable VNet encryption are validated; compliant DR/nonproduction PHI boundaries exist. | Platform: deployed settings/IaC. |
| Legal use and restrictions | Test valid, expired/revoked, self-paid, minor/representative, psychotherapy, Part 2, and study-waiver scenarios across all serving/release paths. | Privacy/Clinical/Data: expected allow/deny evidence. |
| Least privilege | Ordinary consumers cannot read raw PHI, unassigned cohorts, direct storage, keys/secrets, policy-admin objects, or unintended workspace catalogs. Test privileged change attribution separately. | Platform/Data/Security: negative and privilege tests. |
| Identity lifecycle | Unique identity/run-as attribution, leaver/mover revocation, browser/API/token controls, and emergency access behave as approved, including active sessions. | HR/Security: measured end-to-end tests. |
| Networking | Test intended private/restricted paths and attempted unapproved ingress/egress for classic and serverless separately. No enforcement rule is left in dry-run. | Platform/Security: flow and denial evidence. |
| Encryption and recovery keys | Each PHI location/transfer has verified protection; a key rotation/recovery exercise preserves required access and backup restorability. | Platform/Security: data/key map and tests. |
| Audit | Required source coverage is demonstrated, preview eligibility resolved, collection failure detected, privileged alterations monitored, and protected evidence retrievable beyond source retention. | Security: event matrix and archive tests. |
| Patient rights | Produce a correct patient-specific access export, amendment propagation, disclosure accounting and confidential communication within approved workflow deadlines. | HIM/Privacy/Data: synthetic end-to-end cases. |
| De-identification | Privacy approves the actual Safe Harbor or Expert Determination release; validate text/images, geography/date/age, reidentification, and linkage risks. | Privacy/Data/expert: signed release evidence. |
| Resilience | Restore consistent data, metadata, permissions, pipelines, keys, integrations, and clinical fallback within hospital-approved RPO/RTO; test logical deletion/ransomware separately from regional failover. | Platform/Clinical: measured exercises. |
| Incident and breach | Exercise discovery, assessment, vendor escalation, individual/media/HHS thresholds, substitute notices, and evidence preservation. Verify small breaches do not defer individual notices. | Security/Privacy/Legal: tabletop and chronology. |
| Hospital operations | Current policies, officers/contacts, training, sanctions, complaints, facility/workstation controls, NPP delivery, evidence retention, and review duties operate. | Hospital leadership/Privacy/HR/Facilities: operating records. |
| External recipients / AI | Approved BAA/DUA/authorization/purpose and feature-specific control paths exist for every downstream BI/app/vendor/model/AI destination; exports cannot silently bypass restriction logic. | Legal/Privacy/Data: approved release inventory. |

## 13. Implementation order

1. **Scope and govern:** inventory PHI/data flows and covered functions; appoint accountable officials; review BAAs, state/Part 2 rules, purposes, and designated record sets; complete the initial risk assessment.
2. **Establish the platform boundary:** choose approved regions/features and compliant environment separation; configure the profile/add-on, identity, network, storage, encryption, and key recovery prerequisites.
3. **Implement least privilege and legal-purpose enforcement:** configure Unity Catalog/storage/compute/ACL boundaries; integrate authorization, restrictions, study and service-level payer exclusions.
4. **Implement evidence and rights:** validate audit source coverage/retention and protected disclosure ledgers; deliver access, amendments, accounting, notices, and confidential-communication workflows.
5. **Operationalize and prove:** enforce code/package maintenance, workforce/facility controls, backup/DR/emergency operations, incident/breach response, and all applicable acceptance gates.

Do not ingest real PHI until the admission prerequisites and applicable go-live gates are satisfied. No cloud resources or permissions were changed in producing this report.

## 14. Regulatory coverage map

This map makes the inventory's scope auditable without pretending that every conditional permission applies to every hospital.

| Regulatory area | Covered by |
| --- | --- |
| Part 160 applicability/definitions/preemption | G01-G03; P41; section 1 boundaries. |
| Part 160 complaints, investigation cooperation, enforcement/nonretaliation | P05, P38; H06; A05-A06. |
| Part 162 administrative transactions, codes, identifiers, agreements | A01-A04; Revenue/EDI implementation, not an automatic Databricks capability. |
| 164.102-.106 general/organizational provisions | G01; P41. |
| 164.302-.306 Security Rule scope/general requirements | G01; S01. |
| 164.308 administrative safeguards and BA assurances | S02-S25. |
| 164.310 physical safeguards | S26-S35. |
| 164.312 technical safeguards | S36-S47. |
| 164.314 organizational safeguards | S48-S49. |
| 164.316 policies/documentation | S50-S54. |
| 164.318 historic Security Rule compliance dates | P42. |
| 164.400-.414 breach notification | B01-B10. |
| 164.500-.501 Privacy Rule scope/definitions | G01; P01/P03/P11/P12/P27. |
| 164.502 general use/disclosure restrictions | P01-P08, P13, P18, P21, P24-P26, P38, P40. |
| 164.504 organizational/BA/plan provisions | P40-P41; S48-S49. |
| 164.506 TPO | P03; P41. |
| 164.508 authorizations | P09-P13. |
| 164.509 and affected 2024 amendments | Section 2 and L19: vacatur caveat; not treated as an operative new attestation mandate. |
| 164.510 agreement/objection permissions | P14-P15. |
| 164.512 public-interest/special-purpose permissions | DCL01-DCL12, including conditional subcategories. |
| 164.514 de-identification, minimum necessary, limited data, fundraising, verification, underwriting | P02, P06, P16-P21; section 5.2. |
| 164.520 NPP | P22-P23; Part 2 updates and vacatur caveats. |
| 164.522 restrictions/confidential communications | P24-P26. |
| 164.524 access | P27-P28, including Ciox limitation. |
| 164.526 amendment | P29-P30. |
| 164.528 accounting | P31-P32. |
| 164.530 administration | P33-P41; B10. |
| 164.532-.534 historic/transitional Privacy Rule provisions | P42. |
| HITECH operative PHI protections/enforcement | H01-H07 and referenced Security/Privacy/Breach rows; H08 flags separate program applicability. |

## 15. Primary-source register

Sources were researched September 30, 2026. URLs are retained for independent verification. Product documentation is mutable; preserve the version used for an actual implementation/assessment. eCFR text may retain provisions affected by litigation, so read it together with the HHS court-order guidance.

### 15.1 Law, regulator guidance, and authoritative implementation guidance

| ID | Source | URL |
| --- | --- | --- |
| L01 | eCFR, 45 CFR Part 160 | `https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-160` |
| L02 | eCFR, 45 CFR Part 164 | `https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164` |
| L03 | eCFR, 164.306 general Security Rule requirements | `https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-C/section-164.306` |
| L04 | eCFR, 164.308 administrative safeguards | `https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-C/section-164.308` |
| L05 | eCFR, 164.310 physical safeguards | `https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-C/section-164.310` |
| L06 | eCFR, 164.312 technical safeguards | `https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-C/section-164.312` |
| L07 | eCFR, 164.314 organizational requirements | `https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-C/section-164.314` |
| L08 | eCFR, 164.316 policies/documentation | `https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-C/section-164.316` |
| L09 | eCFR, Privacy Rule general/authorization/disclosure/de-identification provisions, 164.500-.514 | `https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-E` |
| L10 | eCFR, 164.520 NPP | `https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-E/section-164.520` |
| L11 | eCFR, 164.522 privacy protections | `https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-E/section-164.522` |
| L12 | eCFR, 164.524 access | `https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-E/section-164.524` |
| L13 | eCFR, 164.526 amendments | `https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-E/section-164.526` |
| L14 | eCFR, 164.528 accounting | `https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-E/section-164.528` |
| L15 | eCFR, 164.530 administrative requirements | `https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-E/section-164.530` |
| L16 | eCFR, Part 164 Subpart D, breach notification | `https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-D` |
| L17 | HHS, HIPAA and cloud computing | `https://www.hhs.gov/hipaa/for-professionals/special-topics/health-information-technology/cloud-computing/index.html` |
| L18 | HHS, de-identification guidance | `https://www.hhs.gov/hipaa/for-professionals/special-topics/de-identification/index.html` |
| L19 | HHS, reproductive health and June 18, 2025 partial vacatur | `https://www.hhs.gov/hipaa/for-professionals/special-topics/reproductive-health/index.html` |
| L20 | HHS, model NPPs revised for February 2026 | `https://www.hhs.gov/hipaa/for-professionals/privacy/guidance/model-notices-privacy-practices/index.html` |
| L21 | HHS, Security Rule NPRM and January 6, 2025 proposed rule | `https://www.hhs.gov/hipaa/for-professionals/security/hipaa-security-rule-nprm/index.html`; `https://www.govinfo.gov/content/pkg/FR-2025-01-06/pdf/2024-30983.pdf` |
| L22 | OMB/OIRA, 2026 agenda entry for RIN 0945-AA22 | `https://www.reginfo.gov/public/do/eAgendaViewRule?RIN=0945-AA22&pubId=202510` |
| L23 | HHS, Part 2 and its current compliance date | `https://www.hhs.gov/hipaa/part-2/index.html` |
| L24 | CMS, HIPAA Administrative Simplification resources | `https://www.cms.gov/training-education/look-up-topics/hipaa-administrative-simplification` |
| L25 | CMS, 2026 claims attachments/electronic signatures final-rule fact sheet | `https://www.cms.gov/newsroom/fact-sheets/administrative-simplification-adoption-standards-health-care-claims-attachments-transactions` |
| L26 | NIST SP 800-66 Rev. 2, HIPAA Security Rule implementation resource/crosswalk | `https://csrc.nist.gov/pubs/sp/800/66/r2/final` |
| L27 | HHS, Security Rule summary including HITECH amendments and direct BA responsibility | `https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html` |
| L28 | HHS, Ciox right-of-access court-order notice and electronic access FAQ | `https://www.hhs.gov/hipaa/court-order-right-of-access/index.html`; `https://www.hhs.gov/hipaa/for-professionals/faq/under-the-hipaa-privacy-rule-do-individuals/index.html` |
| L29 | HHS, Security Rule guidance and 2021 recognized-security-practices amendment | `https://www.hhs.gov/hipaa/for-professionals/security/guidance/index.html` |
| L30 | eCFR, 45 CFR Part 162 | `https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-162` |
| L31 | US House Office of the Law Revision Counsel, 42 USC 17941, recognized security practices | `https://usc-cdn.house.gov/view.xhtml?edition=prelim&num=0&req=granuleid%3AUSC-prelim-title42-section17941` |
| L32 | US House Office of the Law Revision Counsel, 42 USC 17937, non-HIPAA PHR breach notification; FTC reporting guidance | `https://usc-cdn.house.gov/view.xhtml?req=granuleid%3AUSC-prelim-title42-section17937&num=0&edition=prelim`; `https://www.ftc.gov/business-guidance/health-breach-form` |
| L33 | US House Office of the Law Revision Counsel, HITECH section 13405 / 42 USC 17935; section 13406 / 42 USC 17936; section 13411 / 42 USC 17940 | `https://usc-cdn.house.gov/view.xhtml?req=granuleid%3AUSC-prelim-title42-section17935&num=0&edition=prelim`; `https://usc-cdn.house.gov/view.xhtml?req=granuleid%3AUSC-prelim-title42-section17936&num=0&edition=prelim`; `https://usc-cdn.house.gov/view.xhtml?req=granuleid%3AUSC-prelim-title42-section17940&num=0&edition=prelim` |

Where HTML automated access was limited, detailed regulatory text was read from the official eCFR versioner API, using the displayed September 28, 2026 snapshot. Example: `https://www.ecfr.gov/api/versioner/v1/full/2026-09-28/title-45.xml?part=164&section=164.514`. The API is a retrieval mechanism, not an additional legal authority.

### 15.2 Official Azure Databricks and Microsoft product documentation

| ID | Source | URL |
| --- | --- | --- |
| D01 | Azure Databricks HIPAA responsibilities and regional feature support | `https://learn.microsoft.com/en-us/azure/databricks/security/privacy/hipaa` |
| D02 | Compliance security profile | `https://learn.microsoft.com/en-us/azure/databricks/security/privacy/security-profile` |
| D03 | Configure enhanced security/compliance; licensing, permanence, VNet encryption | `https://learn.microsoft.com/en-us/azure/databricks/security/privacy/enhanced-security-compliance` |
| D04 | CMK scope by data type and compute plane | `https://learn.microsoft.com/en-us/azure/databricks/security/keys/customer-managed-keys` |
| D05 | Unity Catalog access-control models and workspace restrictions | `https://learn.microsoft.com/en-us/azure/databricks/data-governance/unity-catalog/access-control/` |
| D06 | Row filters and column masks, including limitations | `https://learn.microsoft.com/en-us/azure/databricks/data-governance/unity-catalog/filters-and-masks/` |
| D07 | ABAC requirements/limitations, including AI Search policy noninheritance | `https://learn.microsoft.com/en-us/azure/databricks/data-governance/unity-catalog/abac/requirements` |
| D08 | Access Connector / Azure managed identities for Unity Catalog storage | `https://learn.microsoft.com/en-us/azure/databricks/connect/unity-catalog/cloud-storage/azure-managed-identities` |
| D09 | Private Link concepts and distinct connectivity paths | `https://learn.microsoft.com/en-us/azure/databricks/security/network/concepts/private-link` |
| D10 | Serverless NCC private connectivity to Azure resources | `https://learn.microsoft.com/en-us/azure/databricks/security/network/serverless-network-security/serverless-private-link` |
| D11 | Serverless egress network policies and enforcement | `https://learn.microsoft.com/en-us/azure/databricks/security/network/serverless-network-security/manage-network-policies` |
| D12 | Diagnostic log reference, events and Azure diagnostics coverage differences | `https://learn.microsoft.com/en-us/azure/databricks/admin/account-settings/audit-logs` |
| D13 | Audit system table, preview status and deletion/retention caveats | `https://learn.microsoft.com/en-us/azure/databricks/admin/system-tables/audit-logs` |
| D14 | System tables, scope and retention | `https://learn.microsoft.com/en-us/azure/databricks/admin/system-tables/` |
| D15 | Disaster recovery and corruption/replication distinctions | `https://learn.microsoft.com/en-us/azure/databricks/admin/disaster-recovery` |
| D16 | Unity Catalog lineage and supported capture paths | `https://learn.microsoft.com/en-us/azure/databricks/data-governance/unity-catalog/data-lineage` |
| D17 | Entra SCIM, account-level provisioning, automatic identity management distinctions | `https://learn.microsoft.com/en-us/azure/databricks/admin/users-groups/scim/` |
| D18 | OAuth recommendation and PAT monitoring/revocation | `https://learn.microsoft.com/en-us/azure/databricks/admin/access-control/tokens` |
| D19 | Azure HIPAA/BAA shared responsibility and certification caveats | `https://learn.microsoft.com/en-us/azure/compliance/offerings/offering-hipaa-us` |
| D20 | Vacuum, retained files, soft deletion and purge | `https://learn.microsoft.com/en-us/azure/databricks/tables/operations/vacuum` |
| D21 | Secret management, privileged access/redaction and Key Vault scope requirements | `https://learn.microsoft.com/en-us/azure/databricks/security/secrets/` |
| D22 | Microsoft Entra Conditional Access | `https://learn.microsoft.com/en-us/entra/identity/conditional-access/overview` |
| D23 | Compute policies and explicit enforcement after changes | `https://learn.microsoft.com/en-us/azure/databricks/admin/clusters/policies` |

**Final assessment boundary:** This report identifies obligations and an implementable control approach. Actual compliance requires hospital-specific applicability decisions, deployment, operating evidence, technical and nontechnical evaluation, and legal/privacy approval. Unsupported features, uncovered vendors, missing patient-rights workflows, and untested recovery remain gaps even when the Databricks workspace is HIPAA-profile enabled.
