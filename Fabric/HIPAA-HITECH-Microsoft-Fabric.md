# Hospital HIPAA/HITECH requirements and Microsoft Fabric implementation

**Research date:** September 30, 2026  
**Perspective:** Hospital enterprise/data architect using Microsoft Fabric  
**Status:** Proposed requirements and control design; no tenant, contract, deployment, or operating effectiveness assessment  
**Reviewed local material:** `HIPAA-Controls-README.md` and all 57 numbered statements in `regulatory-requirements\requirements.txt`  
**Terminology:** HIPAA, not HIPPA, is the Health Insurance Portability and Accountability Act. HITECH is the Health Information Technology for Economic and Clinical Health Act.

## 1. Executive finding and scope

Microsoft Fabric can support a hospital's HIPAA-regulated analytics architecture, but product eligibility, contracts, configuration, hospital procedures, and operating evidence must all be established. Microsoft explicitly names Fabric in its Azure Core Services terms and publishes a security scenario involving patient EHR, laboratory, insurance, and wearable data. These establish a relevant product/architecture basis, not certification of this hospital's implementation. A BAA, encryption, HITRUST/SOC report, or security dashboard alone does not establish hospital compliance. [L17, F01-F04]

This inventory covers the PHI-platform-relevant provisions of 45 CFR Parts 160, 162, and 164: Security Rule standards and named implementation specifications; Privacy Rule uses, disclosures, patient rights, and administrative duties; breach notification; organizational/contractual requirements; and applicable electronic transactions. Its **138 mapped entries use the same G/S/P/DCL/B/H/A identifiers as the accompanying Databricks report** for cross-platform traceability. The 57 existing Fabric notes are reviewed separately in section 13.

**Boundaries:** This is not a legal opinion or a list of every hospital law. Counsel must separately assess state laws, 42 CFR Part 2, research/human-subject requirements, medical-device rules, EHR certification/program duties, and HIPAA's non-PHI-platform insurance/benefits provisions. Conditional duties require an applicability decision, not automatic implementation in every workload.

Assume a hypothetical US hospital covered entity, production electronic PHI (ePHI), clinical/operations/billing and possibly research analytics, Entra identities, and hospital-managed endpoints. No actual BAA, tenant, region, study authorization, architecture artifact, or deployed policies were assessed. The numbered notes are **generic product assumptions and design checks**, not evidence of any customer's actual configuration.

### How to read this report

- **R:** Required Security Rule implementation specification.
- **A:** Addressable specification: assess it; implement when reasonable/appropriate, or document why not and implement an equivalent alternative when reasonable/appropriate. It is not optional.
- **Std:** Mandatory standard without a separately named R/A specification.
- **Conditional:** Applies only to the relevant activity/entity role; retain a non-applicability decision otherwise.
- **Implementation/evidence:** Proposed architectural controls and proof, not a statement that either exists.

Owners: **Privacy** = privacy officer/HIM; **Security** = security officer/SOC; **Platform** = Fabric/identity/network team; **Data** = data owners/engineering; **Legal** = legal/procurement; **HR** = workforce administration; **Facilities** = physical security; **Clinical** = clinical operations; **Revenue** = billing/EDI.

Abbreviations: HIM = health information management; NPP = notice of privacy practices; DUA = data use agreement; RLS/CLS/OLS = row/column/object-level security; CMK = customer-managed key; BYOK = bring your own key; SSO = single sign-on; OAP = outbound access protection; MPE = managed private endpoint; RPO/RTO = recovery point/time objective. Source IDs resolve in section 16.

## 2. Current-law and product distinctions

| Topic | Finding at the research date | Design consequence |
| --- | --- | --- |
| Security Rule proposal | NPRM published January 6, 2025. The OIRA entry inspected identifies publication year 2026, long-term action, and projected final action July 2027, notwithstanding its older URL parameter. An agenda forecast is not final law. [L21, L22] | Use the current Security Rule, including required/addressable distinctions. Proposed prescriptions are forward planning, not current compliance deadlines. |
| Reproductive-health rule litigation | HHS reports partial vacatur on June 18, 2025, including NPP paragraphs 164.520(b)(1)(ii)(F)-(H). Other applicable NPP changes had a February 16, 2026 compliance date. [L19, L20] | Do not treat vacated restrictions/attestations as operative simply because eCFR text still displays them. Preserve ordinary HIPAA and applicable state protections; obtain counsel review. |
| Part 2 | The 2024 final rule's compliance date was February 16, 2026, already past. [L23] | Identify qualifying Part 2 records; apply additional consent, proceedings, and notice requirements. Not every SUD diagnosis is automatically a Part 2 record. |
| Ciox access decision | January 23, 2020 decision limits mandatory third-party direction to electronic copies of PHI in an EHR; the individual-access fee limitation does not apply to third-party transmission requests. [L28] | Preserve individual access to designated-record-set PHI, while HIM/Legal classifies third-party requests and fees correctly. |
| Claims attachments | Final rule effective May 26, 2026; compliance May 26, 2028. It did not finalize prior-authorization attachment standards. [L25] | Plan the applicable EDI changes; do not prescribe those signatures for every Fabric notebook. |
| Fabric versus Databricks | Fabric is SaaS; no Fabric equivalent of a Databricks HIPAA-profile/Premium/Enhanced Security and Compliance prerequisite was established. [F01-F05] | Verify Fabric contracts, exact workloads, region, capacity, and selected features. Do not transplant another platform's prerequisites. |
| Preview eligibility | Current Core Online Services terms exclude Previews; specific governing terms/express exceptions matter. [F02] | Default to no PHI in an unapproved preview. GA alone also does not establish complete contractual eligibility. |
| Healthcare managed solution lifecycle | Downloadable customer-managed package available August 3, 2026. Starting October 1, 2026, only existing customers can deploy the current managed solution. Managed-solution support ends December 31, 2027. [F31] | The first deployment restriction is tomorrow relative to this report, not an unspecified October 1. This is a solution-delivery transition, not a general Fabric PHI prohibition or platform retirement. |
| Monitoring changed | Current September 29, 2026 documentation distinguishes the new Monitoring Item from legacy monitoring: Private Link support and configurable retention, with a 30-day default. [F19] | Version the architecture and avoid repeating legacy limitations as universal facts. Enabling collection is still necessary; no backfill. |

The legal baseline is consistent with the companion Databricks assessment researched on the same date. Product documentation is mutable. Capture governing contracts and the actual documentation/version used before production; a search-engine cached page can differ from the current official page.

## 3. Contractual and platform admission requirements

| ID | Requirement | Fabric implementation | Owner; evidence |
| --- | --- | --- | --- |
| G01 | Determine covered entity, BA, workforce, PHI, and designated-record-set boundaries. 160.103; 164.104-.106; 164.302; 164.500-.501. [L01, L02] | Inventory sources, OneLake files/tables, warehouses, models, notebooks, outputs, logs, caches, gateways, exports, AI, and recipients. Analytics used for individual decisions may belong to a designated record set. | Privacy/Data: approved scope and data-flow inventory. |
| G02 | Apply HIPAA preemption exceptions and more-protective applicable law. 160.201-.205. [L01] | Maintain jurisdiction/data-type restrictions in hospital release workflows; isolate specially protected records and incompatible purposes. | Legal/Privacy: applicability/restriction matrix. |
| G03 | Obtain satisfactory BA assurances before PHI processing. 164.502(e), 164.504(e), 164.308(b), 164.314(a). [L02, L04, L07, F02-F04] | Verify the hospital's Microsoft agreement, incorporated BAA/DPA, Fabric/Power BI and ancillary service scope, subprocessors, support, and third-party PHI vendors. A separately signed BAA is not always the contracting mechanism. | Legal: governing agreement and service-scope register. |
| G04 | Select a supported Fabric deployment boundary. [F05, F10-F14, F24] | Record tenant/capacity region, actual item types, licenses, inbound/outbound topology, encryption scope, monitoring, and recovery. F-SKU prerequisites apply to specific features, not automatically every HIPAA obligation. Reject unsupported item/control combinations. | Platform: configuration/IaC and compatibility decisions. |
| G05 | Protect every PHI-bearing location and identity. [F01, F06-F09, F24, F25, F36] | Use default at-rest/transport encryption plus hospital-approved key choices; narrow workspace/item/OneLake/SQL/model access and workload connections. Include downstream files, gateway hosts, imported models, and evidence stores. | Security/Platform: data/key/identity map and access tests. |
| G06 | Admit only approved features, destinations, and purposes. [F02, F05, F23, F31, F35, F37] | Maintain a feature/region/preview allowlist, actual contract coverage, connector approvals, AI/support-data decisions, and vendor confirmations for conflicts. Keep patient information out of names, tags, Git metadata, URLs, and unapproved telemetry. | Legal/Platform: admissions and unresolved blockers. |

There is no basis here to declare an entire tenant or workspace HIPAA-compliant by enabling a single switch. A preview dependency or tenant-level control that has not been approved remains a gap even if the application team cannot configure it.

## 4. Security Rule requirements matrix

### 4.1 General and administrative safeguards

Legal basis: [L03, L04]. Fabric mechanisms and limitations: [F06-F23, F26-F29, F35, F36].

| ID | Requirement and legal citation | Class | Implementation | Owner; evidence |
| --- | --- | --- | --- | --- |
| S01 | Confidentiality, integrity, availability; threats, impermissible disclosures, workforce compliance, and maintained risk-sensitive safeguards. 164.306(a)-(e). | Std | Maintain an end-to-end hospital control baseline, including privileged insiders, alternate access paths, clinical outages, exports, and third parties. Document each addressable decision. | Security: risk/control register. |
| S02 | Risk analysis. 164.308(a)(1)(ii)(A). | R | Assess all ePHI locations and flows, identities, permissions, network/engine modes, derived models, logs, endpoints, backups, and vendors. A compliance template or posture scan is only an input. | Security/Data: scoped assessment. |
| S03 | Risk management. 164.308(a)(1)(ii)(B). | R | Assign remediation and due dates; implement approved controls and escalate unsupported combinations. Obtain authorized residual-risk decisions, not unilateral engineer acceptance. | Security/Platform: remediation/acceptance records. |
| S04 | Sanction policy. 164.308(a)(1)(ii)(C). | R | Define/apply hospital sanctions using protected investigation evidence; Fabric cannot make disciplinary decisions. | HR/Security: policy/case records. |
| S05 | Information-system activity review. 164.308(a)(1)(ii)(D). | R | Review multi-source identity, platform, SQL, OneLake, workload, release, and key events; investigate bulk access, privilege changes, failed collection, and emergency use. | Security: review and alert dispositions. |
| S06 | Assigned security responsibility. 164.308(a)(2). | Std | Designate the hospital security official and tenant/workspace/data/SOC delegates; document authority and escalation. | Leadership: designation/RACI. |
| S07 | Authorization/supervision. 164.308(a)(3)(ii)(A). | A | Require manager/data-owner approval and supervised access. Ordinary PHI consumers should not receive broad Contributor/Member/Admin roles for convenience. | HR/Data: approvals and role tests. |
| S08 | Workforce clearance. 164.308(a)(3)(ii)(B). | A | Determine job-appropriate PHI access, training, and clearance before provisioning, including contractors and guest researchers. | HR/Data: clearance/role records. |
| S09 | Termination procedures. 164.308(a)(3)(ii)(C). | A | Disable identities; remove group, workspace, item, sharing, connection, SQL, and model grants; transfer jobs safely and address active tokens/sessions. Measure revocation rather than assuming CAE. | HR/Platform: termination and denied-access tests. |
| S10 | Isolate clearinghouse functions. 164.308(a)(4)(ii)(A). | R, conditional | Separate clearinghouse data, workspaces, workload identities, and duties from unrelated hospital functions, or document non-applicability. | Legal/Revenue: boundary decision. |
| S11 | Access authorization. 164.308(a)(4)(ii)(B). | A | Define purpose/cohort/column permissions across workspace/item, OneLake, SQL, and semantic-model layers. Review default grants and alternate raw-data access. | Data/Platform: approved grant matrix. |
| S12 | Establish/review/modify access. 164.308(a)(4)(ii)(C). | A | Implement joiner/mover/leaver and recertification processes; retain group/policy/grant snapshots and change events to reconstruct past effective access. | Data/Platform: recertifications/history. |
| S13 | Security awareness/training, including management. 164.308(a)(5)(i). | Std | Train for PHI in notebooks, model outputs, exports, credentials, screenshots, support tickets, guest collaboration, and incident reporting. | HR/Security: training/effectiveness records. |
| S14 | Security reminders. 164.308(a)(5)(ii)(A). | A | Send periodic/incident-specific reminders using synthetic examples and approved communication channels. | Security: dated communications. |
| S15 | Malicious-software protection. 164.308(a)(5)(ii)(B). | A | Protect hospital endpoints/gateway hosts; review packages, environments, deployment code, and creation privileges. Obtain provider assurance for SaaS-managed infrastructure; do not claim customer malware agents run everywhere in Fabric. | Platform/Security: dependency/endpoint/provider evidence. |
| S16 | Login monitoring. 164.308(a)(5)(ii)(C). | A | Correlate Entra sign-ins with Fabric/service-principal activity; detect failed/unusual sign-ins, guest anomalies, and workload-identity misuse. | Security: detections/investigations. |
| S17 | Password/credential management. 164.308(a)(5)(ii)(D). | A | Apply hospital identity policy; prefer supported credential-free workspace identity or approved service-principal connections. Restrict who can assume connection identities; protect unavoidable secrets. | Security/Platform: credential/access reviews. |
| S18 | Security-incident procedures, response, reporting, and documentation. 164.308(a)(6)(i)-(ii). | Std + R | SOC contains incidents, preserves evidence, mitigates harm, and escalates to Privacy/Legal and vendors. Record suspected events and outcomes, not only confirmed breaches. | Security/Privacy: playbooks/exercises/cases. |
| S19 | Data backup plan. 164.308(a)(7)(ii)(A). | R | Maintain retrievable exact ePHI copies through supported independent backup/copy methods; preserve consistent data/transaction metadata, definitions, credentials/configuration recovery, and manifests. Git alone contains no complete data backup. | Platform/Data: manifests and restore proof. |
| S20 | Disaster recovery plan. 164.308(a)(7)(ii)(B). | R | Approve RPO/RTO by workload; configure eligible capacity DR and independent recovery paths. Rebuild unsupported items, connections, security, keys, schedules, and downstream bindings. | Platform/Clinical: measured recovery/runbooks. |
| S21 | Emergency-mode operations. 164.308(a)(7)(ii)(C). | R | Define safe clinical downtime alternatives, restoration priorities, necessary emergency data access, and post-outage reconciliation. An analytics outage must not silently remove critical care capability. | Clinical/Platform: downtime procedures. |
| S22 | Contingency testing/revision. 164.308(a)(7)(ii)(D). | A | Exercise deletion, corruption, ransomware, region/tenant/identity/key failures, failover/failback, and consumer recovery; remediate findings. | Platform/Clinical: drill results/revisions. |
| S23 | Application/data criticality analysis. 164.308(a)(7)(ii)(E). | A | Assign patient-safety/business impact and restore order for clinical, revenue, operations, and study workloads. | Clinical/Data: impact/recovery tiers. |
| S24 | Periodic technical/nontechnical evaluation. 164.308(a)(8). | Std | Evaluate controls periodically and after material legal, product, permission, topology, or workflow changes. Include hospital procedures, not only technical settings. | Security/Privacy: evaluations/change triggers. |
| S25 | BA assurances/written agreements. 164.308(b)(1)-(3). | R | Maintain G03/S48 for PHI processors and relevant downstream subcontractors; allocate who obtains each assurance. | Legal: contract/vendor evidence. |

The corresponding overarching security-management, workforce-security, information-access-management, and contingency standards are covered by these rows together, not by a single product feature.

### 4.2 Physical safeguards

Legal basis: [L05]. Microsoft-managed facilities are only one part of the boundary. [L17, F03]

| ID | Requirement and legal citation | Class | Implementation | Owner; evidence |
| --- | --- | --- | --- | --- |
| S26 | Emergency facility access for restoration. 164.310(a)(2)(i). | A | Review provider facility assurances; maintain hospital access to recovery sites, gateway/network rooms, and authorized emergency staff. | Facilities/Platform: access procedures/drills. |
| S27 | Facility security plan. 164.310(a)(1), (a)(2)(ii). | Std + A | Review service assurance and secure hospital facilities supporting PHI access and infrastructure. | Facilities/Legal: plans/assurance review. |
| S28 | Access validation/visitor control. 164.310(a)(2)(iii). | A | Use hospital badges, visitor escorts, and controlled maintenance access; an Entra login does not validate a physical visitor. | Facilities: access/visitor records. |
| S29 | Facility maintenance records. 164.310(a)(2)(iv). | A | Record security-relevant doors/locks/network-room changes; review assurance for provider-managed facilities. | Facilities: maintenance/change records. |
| S30 | Workstation use. 164.310(b). | Std | Define approved uses and surroundings for browser, Desktop, SQL, notebook, VDI, remote-work, and printing paths. | Security/HR: workstation policy. |
| S31 | Workstation physical security. 164.310(c). | Std | Secure endpoints/locations; lock screens and manage devices. Device-compliance checks supplement, not replace, physical protection. | Security/Facilities: endpoint/site evidence. |
| S32 | Device/media control and disposal. 164.310(d)(1), (d)(2)(i). | Std + R | Sanitize hospital media; review cloud hardware disposal assurance; account for PHI in exports, caches, retained files, models, and backups under holds/retention. | Security/Platform: approved disposal records. |
| S33 | Media reuse. 164.310(d)(2)(ii). | R | Validate sanitization before device/media reuse and provider handling of released infrastructure. | Security: sanitization evidence. |
| S34 | Media accountability. 164.310(d)(2)(iii). | A | Track PHI export/media movement and custodians; prohibit uncontrolled removable-media use. | Security/Data: asset/transfer inventory. |
| S35 | Exact backup before equipment movement when needed. 164.310(d)(2)(iv). | A | Assess PHI on moved endpoints/appliances; create/validate a retrievable copy when appropriate. | Platform/Security: movement/backup records. |

### 4.3 Technical safeguards

Legal basis: [L06]. Platform details must be applied to the actual execution paths, not inferred from a workspace label.

| ID | Requirement and legal citation | Class | Implementation | Owner; evidence |
| --- | --- | --- | --- | --- |
| S36 | Authorized technical access. 164.312(a)(1). | Std | Combine narrow workspace/item grants, supported OneLake roles, SQL/semantic-model rules, and restrictions on raw files, sharing, connections, and APIs. Test policy administrators and privileged bypass paths separately. [F06-F09] | Data/Platform: positive/negative path tests. |
| S37 | Unique user identification. 164.312(a)(2)(i). | R | Use individual Entra identities and distinct workload principals; record initiator, effective query identity, storage identity, and recipient. Shared backend identity requires correlation, not shared interactive logins. [F07, F09, F36] | Platform/Security: attribution inventory/tests. |
| S38 | Emergency access procedure. 164.312(a)(2)(ii). | R | Define attributable, scoped emergency PHI access with tested identity/network/key prerequisites, alerts, and post-use review. Tenant break-glass is not sufficient clinical access. | Clinical/Security: emergency drill/review. |
| S39 | Automatic logoff. 164.312(a)(2)(iii). | A | Test actual idle behavior in browser, Desktop, VDI, SQL, APIs, and applications; combine supported termination, locks, and reauthentication. Document alternatives where needed. Spark session shutdown or sign-in frequency alone is not user idle logoff. | Security/Platform: inactivity tests/decision. |
| S40 | Encryption/decryption. 164.312(a)(2)(iv). | A | Inventory default encryption, exports, gateways, models, logs, and recovery stores. Add workspace CMK or Power BI BYOK only within their documented scopes if hospital policy calls for them. [F01, F24, F25] | Security/Platform: location/key map and recovery. |
| S41 | Audit controls. 164.312(b). | Std | Enable appropriate sources and correlate SQL, OneLake, identity, Fabric audit, monitoring, and release/application events. Protect selected evidence independently and monitor completeness/latency. SQL audit alone cannot cover all PHI reads. [F17-F22] | Security: coverage/collection/tamper tests. |
| S42 | Protect against improper alteration/destruction. 164.312(c)(1). | Std | Limit writes/destructive administration; use reviewed deployments, supported transaction controls, source reconciliation, validation, and independent recovery. Redundancy/encryption alone do not prove integrity. | Data/Platform: change/integrity/restore proof. |
| S43 | Mechanism to authenticate ePHI integrity. 164.312(c)(2). | A | Validate source identity, authenticated transfers, checksums/manifests where suitable, completeness, clinical-code/unit mappings, and versioned provenance. Lineage is supporting metadata, not clinical accuracy proof. [F29] | Data/Security: integrity/reconciliation records. |
| S44 | Person/entity authentication. 164.312(d). | Std | Use supported Entra authentication; enforce hospital MFA/device/Conditional Access baseline and narrow workload credentials/identity assumption. Test relevant downstream resource and token paths. [F15, F16, F36] | Security/Platform: authentication/negative tests. |
| S45 | Transmission security. 164.312(e)(1). | Std | Inventory client, OneLake, SQL, connector, gateway, Spark, cross-workspace, API, and recipient transfers; secure ingress and egress separately. [F10-F14, F35] | Platform/Security: approved flow matrix. |
| S46 | Transmission integrity. 164.312(e)(2)(i). | A | Require certificate validation and authenticated integrity-protected protocols; reject tampered/untrusted inputs and reconcile clinical feeds. | Data/Platform: tamper/certificate tests. |
| S47 | Transmission encryption. 164.312(e)(2)(ii). | A | Fabric inbound endpoints enforce TLS 1.2 minimum and negotiate TLS 1.3 where possible. Validate connectors, customer code, gateways, and exports separately; private routing is not encryption. [F01] | Platform/Security: transport settings/path evidence. |

### 4.4 Organizational, policy, and documentation duties

Legal basis: [L07, L08].

| ID | Requirement and legal citation | Class | Implementation | Owner; evidence |
| --- | --- | --- | --- | --- |
| S48 | BA/subcontractor contracts and qualifying other arrangements. 164.314(a)(1)-(2). | R | Require Security Rule compliance, subcontractor assurances, and incident/breach reporting; pair with P40 Privacy Rule terms. Review qualifying arrangements rather than assuming exemptions. | Legal/Security: approved contracts. |
| S49 | Group-health-plan sponsor safeguards/separation. 164.314(b)(1)-(2). | R, conditional | Amend applicable plan documents; restrict sponsor/agent uses and establish separation/reporting. Do not expose employee-plan PHI to ordinary HR analytics. | Legal/HR: plan/isolation evidence. |
| S50 | Reasonable/appropriate policies and procedures. 164.316(a). | Std | Maintain approved hospital procedures, technical configuration baselines, and operating instructions with assigned owners. | Security/Platform: policy set. |
| S51 | Written/electronic documentation. 164.316(b)(1). | Std | Preserve required policies and documented actions/assessments in a governed evidence repository, with authors, versions, and effective dates. | Security: evidence index. |
| S52 | Documentation retention. 164.316(b)(2)(i). | R | Retain required Security Rule documentation six years from creation or last effective date, whichever is later; apply additional holds/requirements. | Security/Legal: retention/retrieval proof. |
| S53 | Documentation availability. 164.316(b)(2)(ii). | R | Make current procedures/evidence available to responsible implementers and authorized reviewers without broad PHI-bearing log access. | Security: access/retrieval tests. |
| S54 | Documentation updates. 164.316(b)(2)(iii). | R | Review periodically and after material technology, risk, product-support, workflow, or legal changes; preserve history. | Security/Platform: revisions/change records. |

## 5. Privacy Rule and patient-rights requirements

The Privacy Rule covers electronic, paper, and oral PHI. Fabric can implement selected data operations; the hospital owns authority, purposes, rights, communications, and decisions. Legal basis: [L02, L09-L15, L17-L20, L28]. Relevant platform mechanisms: [F06-F09, F17-F23, F29, F37].

| ID | Requirement and legal citation | Fabric implementation and hospital process | Owner; evidence |
| --- | --- | --- | --- |
| P01 | Permitted/required uses and disclosures. 164.502(a). | Associate each dataset, model, pipeline, consumer, and release with an approved legal basis/purpose. A BAA or technical permission is not patient authorization. | Privacy/Data: purpose/release register. |
| P02 | Minimum necessary. 164.502(b); 164.514(d). | Define role/data-category limits and recurring/nonroutine release protocols; enforce approved cohorts/columns and justify whole-record uses. Preserve exceptions, including treatment-provider requests/disclosures and individual access. | Privacy/Data: protocols and scope tests. |
| P03 | Treatment, payment, operations. 164.506. | Validate TPO purpose and conditions for other entities/OHCAs. Research, commercial model training, and marketing are not automatically TPO. | Privacy/Clinical: purpose decisions. |
| P04 | Incidental disclosures only with appropriate permission/safeguards. 164.502(a)(1)(iii); 164.530(c). | Minimize PHI in notebook outputs, visual titles, logs, alerts, emails, collaboration, and support. Use synthetic examples. | Privacy/Platform: output/safeguard reviews. |
| P05 | Required individual/HHS disclosures. 164.502(a)(2), (a)(4); 160.310. | Produce authorized records and regulator evidence through controlled jobs/releases; network isolation must not obstruct lawful access. | HIM/Legal: request/production records. |
| P06 | Identity/authority verification. 164.514(h). | Verify requesters/representatives and required representations before release, subject to exceptions. Entra authentication does not establish patient-representation authority. | HIM/Privacy: verification records. |
| P07 | Personal representatives/minors/endangerment exceptions. 164.502(g). | Integrate validated relationships and state minor rules; restrict release views and require professional exception decisions. | Privacy/Clinical: authority/exception records. |
| P08 | Decedent protection. 164.502(f). | Protect retained decedent PHI for 50 years after death; do not automatically publish it. This is not a universal 50-year retention requirement. | Privacy/Data: classification/retention rationale. |
| P09 | Valid authorizations and required copies. 164.508(a)-(c). | Hospital forms capture information, discloser, recipient, purpose, expiration, signature/date, authority, and required statements. Enforce scope at processing/release; preserve signed versions and provide copies. | HIM/Privacy: forms/scope tests. |
| P10 | Defects, revocation, compound forms, conditioning limits. 164.508(b). | Reject known-invalid/expired/revoked authority; propagate status to future releases. Legal reviews reliance/insurance exceptions and permitted conditioning. | Privacy/Legal: revocation/form evidence. |
| P11 | Psychotherapy-note restrictions. 164.508(a)(2). | Classify actual psychotherapy notes correctly; isolate from general analytics/modeling and release only under applicable authorization/exception. | Privacy/Clinical: classification/release tests. |
| P12 | Marketing authorization/remuneration statements. 164.508(a)(3); 164.501. | Separate audiences from care analytics; validate exceptions and required remuneration language before vendor releases. | Privacy/Legal: marketing decisions. |
| P13 | Sale of PHI. 164.502(a)(5)(ii); 164.508(a)(4). | Block monetization/sharing absent an applicable exception or valid remuneration-specific authorization, including indirect arrangements. | Privacy/Legal: transaction/authorization review. |
| P14 | Facility directory preferences. 164.510(a). | Populate only approved fields/recipients from agreement/objection preferences and applicable emergency procedures; a full lakehouse is not a directory. | Clinical/Privacy: preferences/feed tests. |
| P15 | Family/caregiver, notifications, disaster relief. 164.510(b). | Release relevant information under agreement/non-objection or professional judgment; respect decedent preferences and constrain outputs. | Clinical/HIM: decision/release evidence. |
| P16 | Safe Harbor de-identification. 164.514(a), (b)(2). | Build/test removal/generalization for all 18 categories, dates, geography, age, text/images, and no-actual-knowledge condition. Masking/hash replacement alone is insufficient. | Privacy/Data: validated release/sign-off. |
| P17 | Expert Determination. 164.514(b)(1). | Obtain a qualified expert's documented methods/results and very-small-risk determination for the recipient/context; implement approved transformations and conditions. | Privacy/expert/Data: expert report/tests. |
| P18 | Re-identification codes. 164.514(c); 164.502(d). | Use a qualifying code not derived from individual information; protect the mechanism separately. Reversible pseudonymization is not automatically de-identification. | Privacy/Data: design/separation tests. |
| P19 | Limited data sets/DUAs. 164.514(e). | Remove required direct identifiers; restrict purposes to qualifying research/public health/operations; require DUA safeguards, agent/reporting/no-contact/no-reidentification terms and violation response. Limited data sets remain PHI. | Privacy/Legal: DUA/release validation. |
| P20 | Fundraising/opt-out. 164.514(f); 164.520(b)(1)(iii). | Limit eligible fields/recipients, NPP statement, opt-out and suppression; do not condition treatment/payment. Apply additional Part 2 rules where relevant. | Privacy/foundation: suppression/notice tests. |
| P21 | Health-plan genetic underwriting/proposed enrollment limits. 164.502(a)(5)(i); 164.514(g). | For applicable plan functions, prevent prohibited genetic underwriting and constrain unsuccessful enrollment data; preserve relevant issuer exceptions. | Legal/plan/Data: prohibition tests. |
| P22 | NPP content, actual practices, revisions. 164.520(b); 164.502(i). | Hospital publishes accurate plain-language uses/rights/duties/breach/complaints/contact/effective date and applicable February 16, 2026 updates, not vacated reproductive provisions. | Privacy/Legal: NPP/practice review. |
| P23 | NPP delivery, acknowledgment, posting and availability. 164.520(c)-(e). | Intake/portal processes cover first-service delivery, good-faith acknowledgment and failure records, emergency timing, posting, paper copies, versions, and applicable plan rules. | HIM/Privacy: notices/acknowledgments. |
| P24 | Requested/agreed restrictions. 164.522(a). | Maintain request/decision register and enforce agreed restrictions in eligibility tables, model refreshes, extracts, and APIs; record lawful exceptions/termination. | Privacy/Data: restriction tests. |
| P25 | Fully self-paid service restriction. 164.522(a)(1)(vi). | On qualifying request, suppress service details from health-plan payment/operations disclosures unless otherwise required by law. Reconcile billing and analytics at encounter/service level. | Revenue/Privacy: payer exclusion tests. |
| P26 | Confidential communications. 164.522(b). | Honor reasonable provider requests for alternate means/locations without requiring a reason; distinguish plan rules and apply preferences to actual communications. | HIM/Privacy: delivery/preference tests. |
| P27 | Designated-record-set access and timely action. 164.524(a), (b), (e). | HIM scopes records, including individual-decision analytics; retrieve ePHI within 30 days, with only one timely documented additional 30-day extension. | HIM/Data: scope/request tracker. |
| P28 | Access formats, fees, denials/review. 164.524(c)-(d). | Provide requested readily producible or agreed readable form; handle partial access, permitted cost-based fees, denial/review notices, and Ciox-qualified third-party requests. Do not bill search/retrieval as copy labor. | HIM/Legal: exports/fees/review records. |
| P29 | Amendment and timely decisions. 164.526(a)-(b), (f). | Hospital decides amendment/denial; act within 60 days, with one timely documented 30-day extension. A pipeline must not decide clinical correctness. | HIM/Clinical: requests/decisions. |
| P30 | Amendments/disagreements and propagation. 164.526(c)-(e). | Append/link accepted amendments, disagreements/rebuttals and required disclosure material; notify appropriate recipients/BAs and reconcile downstream tables, scores, exports, and relevant model inputs. | HIM/Data: propagation/reconciliation proof. |
| P31 | Accounting timing, charges, exceptions/responsibility. 164.528(a), (c)-(d). | Act within 60 days, with one documented 30-day extension; first request in 12 months free. Apply current exclusions/suspensions and obtain counsel review of the HITECH statutory distinction in section 7. | HIM/Privacy: workflow/exception tests. |
| P32 | Accounting content/six-year lookback. 164.528(b), (d). | Maintain a protected patient-specific disclosure ledger: date, recipient/address if known, PHI description, purpose, and relevant BA disclosures; support applicable repeated/research alternatives. Query logs alone are insufficient. | Privacy/Data: ledger/sample accounting. |
| P33 | Privacy official/contact. 164.530(a). | Designate hospital personnel and complaint/information channels; workspace administration is not privacy-officer appointment. | Leadership: designations. |
| P34 | Privacy/breach training. 164.530(b). | Train new workforce within a reasonable period and affected staff after material changes; document privacy/rights/disclosure training as well as security. | HR/Privacy: training records. |
| P35 | Privacy safeguards. 164.530(c). | Protect oral/paper PHI, screens, prints, notebook/BI outputs, local files, and collaboration. Classification labels alone do not implement every safeguard. | Privacy/Security: operational evidence. |
| P36 | Complaints/dispositions. 164.530(d). | Maintain protected hospital complaint cases and relevant investigation evidence without broad log access. | Privacy: complaint register. |
| P37 | Sanctions/mitigation. 164.530(e)-(f). | Investigate, apply appropriate sanctions, contain improper sharing, and mitigate known harm while respecting protected disclosures. | HR/Privacy/Security: case/actions. |
| P38 | No prohibited retaliation/waiver. 164.530(g)-(h); 160.316; 164.502(j). | Preserve protected reporting and avoid making treatment dependent on waiving HIPAA rights; review exceptions before sanctions. | Legal/HR: policy/case review. |
| P39 | Policies, changes, records/retention. 164.530(i)-(j). | Version privacy/breach practices and notices; retain required documentation six years from creation or last effectiveness, whichever is later. | Privacy/Legal: versions/retention. |
| P40 | Privacy BA contracts/material violations. 164.504(e). | Include uses, safeguards, reporting, subcontractors, access/amendments/accounting, delegated duties, HHS access, return/destruction or continuing protection, and termination; cure/terminate known material patterns as required. | Legal/Privacy: contracts/vendor actions. |
| P41 | Hybrid/affiliated entities, multiple functions, group plans/OHCAs. 164.105; 164.504(f)-(g); 164.506; 164.520(a); 164.530(k). | Document covered components/qualifying arrangements and separate research/employment/plan/clearinghouse functions where required; do not allow blanket organizational sharing. | Legal/Privacy: boundary/plan/access proof. |
| P42 | Transitional/legacy provisions. 164.532-.534; 164.318. | Verify any legacy research reliance and events affecting it; historical compliance dates do not excuse current duties. | Legal/HIM: applicability/legacy register. |

**No general HIPAA erasure right:** Amendment commonly means appending/linking and appropriate propagation, not deleting records. Six-year HIPAA documentation retention is not a universal medical-record or raw-log retention rule. [L08, L13-L15]

### 5.1 Conditional disclosure permissions

These are permissions under specific conditions, not mandatory disclosures merely because someone requests data. A separate law can require disclosure. No permission grants unrestricted Fabric access. Legal basis: 164.512 [L09]; litigation caveat [L19].

| ID | Category and citation | Hospital decision/Fabric implementation | Owner; evidence |
| --- | --- | --- | --- |
| DCL01 | Required by law. 164.512(a). | Verify actual legal duty, scope, and applicable associated conditions; generate an approved extract. | Legal/HIM: authority/release record. |
| DCL02 | Public health. 164.512(b). | Verify agency/purpose for disease, abuse, vital events, FDA/exposure/occupational reporting; document required school-immunization agreement. | Clinical/Privacy: authority/feed tests. |
| DCL03 | Abuse/neglect/domestic violence. 164.512(c). | Apply law/consent/professional judgment and notice/safety exceptions; limit release. | Clinical/Legal: protected decision/notice. |
| DCL04 | Health oversight. 164.512(d). | Validate authorized oversight/exclusions and provide scoped records, not general investigator accounts. | Legal/Privacy: authority/release record. |
| DCL05 | Judicial/administrative proceedings. 164.512(e). | Review order scope or qualifying notice/protective-order assurances/reasonable efforts; track disposal restrictions. | Legal/HIM: order/assurances/export. |
| DCL06 | Law enforcement. 164.512(f). | Apply the relevant process/victim/decedent/crime/emergency conditions; identification/location releases have limited fields and specified biological/DNA exclusions. | Legal/Clinical: basis/field validation. |
| DCL07 | Coroners/examiners/funeral directors. 164.512(g). | Verify duty/authority and necessary scope; use purpose-specific releases. | HIM/Legal: request/release record. |
| DCL08 | Organ/eye/tissue donation. 164.512(h). | Validate procurement/transplant purpose and recipient; restrict approved transfer. | Clinical/Privacy: purpose/transfer proof. |
| DCL09 | Research without authorization. 164.512(i). | Require qualifying IRB/privacy-board waiver/alteration or preparatory/decedent representations; enforce study scope and prohibit researcher removal in preparatory review. | Research/Privacy: approvals/access/output tests. |
| DCL10 | Serious threats. 164.512(j). | Record legally/ethically permitted good-faith professional judgment, appropriate recipient/scope, and special restrictions. | Clinical/Legal: decision/release record. |
| DCL11 | Specialized government functions. 164.512(k). | Assess military/veteran, national-security/protective, State Department, custody, benefits, and NICS circumstances individually; document non-applicability where appropriate. | Legal/Privacy: authority/role decision. |
| DCL12 | Workers' compensation. 164.512(l). | Verify applicable law and permitted necessity; generate approved injury/benefit extracts. | Legal/Revenue: authority/scope tests. |

A research waiver requires the documented board identity, approval date/signature/procedure, approved PHI, privacy-risk findings, identifier protection/destruction plan, reuse/disclosure assurances, and impracticability findings. A domain, study workspace, or dataset label is not that approval. [L09]

### 5.2 De-identification implementation

Use distinct **PHI**, **limited-data-set**, and **validated de-identified** release paths. Fabric notebooks/pipelines can execute customer-developed transformations; neither Fabric RLS nor an external de-identification service automatically establishes Safe Harbor or Expert Determination. Separate any external service's contractual/region/purpose review. [L09, L18]

Safe Harbor validation covers these 18 categories for the individual and relevant relatives, employers, and household members:

| # | Identifier category | Pipeline validation/action |
| --- | --- | --- |
| 1 | Names | Remove from structured fields and free text. |
| 2 | Geography smaller than a state | Remove street/city/county/precinct and ZIP details; retain qualifying first three ZIP digits only under the population rule, otherwise use 000. |
| 3 | Individual-related dates other than year; ages over 89 | Remove/generalize date elements and age-revealing dates; aggregate ages 90 and older. |
| 4 | Telephone numbers | Remove structured/narrative occurrences. |
| 5 | Fax numbers | Remove. |
| 6 | Email addresses | Remove. |
| 7 | Social Security numbers | Remove. |
| 8 | Medical-record numbers | Remove. |
| 9 | Health-plan beneficiary numbers | Remove. |
| 10 | Account numbers | Remove. |
| 11 | Certificate/license numbers | Remove. |
| 12 | Vehicle identifiers/serials/license plates | Remove. |
| 13 | Device identifiers/serials | Remove, including clinical/imaging metadata. |
| 14 | URLs | Remove. |
| 15 | IP addresses | Remove. |
| 16 | Biometric identifiers | Remove fingerprints, voiceprints, and comparable identifiers. |
| 17 | Full-face photos/comparable images | Remove or use an approved methodology; inspect pixels as well as headers. |
| 18 | Other unique identifying characteristics/codes | Remove unless the specific regulatory re-identification-code conditions apply. |

Apply the no-actual-knowledge condition, inspect free text/DICOM/images and downstream releases, and consider Expert Determination when useful detail cannot meet Safe Harbor. Labels, hashing, tokenization, and masking are not independent legal de-identification determinations. [L18]

## 6. Breach Notification Rule

Legal basis: 164.400-.414 [L16]. Hospital/vendor processes perform assessment and notification; logs support them.

| ID | Requirement and citation | Implementation | Owner; evidence |
| --- | --- | --- | --- |
| B01 | Definition/exceptions/presumption/risk assessment. 164.402. | Presume impermissible acquisition/access/use/disclosure is a breach absent a qualifying exception or documented low-probability assessment; assess PHI, recipient, actual access/acquisition, and mitigation. | Privacy/Legal/Security: four-factor decision. |
| B02 | Unsecured PHI. 164.402; HHS guidance. | Assess qualifying encryption/destruction and exposure of keys/decrypted data. At-rest encryption does not excuse compromised authorized access or plaintext exports. | Security/Legal: exposure/encryption assessment. |
| B03 | Discovery/timing. 164.404(a)-(b). | Record actual/reasonable-diligence discovery, including workforce/agent knowledge; notify without unreasonable delay and within 60 calendar days, subject to lawful delay. Do not wait for forensics to finish. | Privacy/Legal: chronology/notices. |
| B04 | Individual notice content/method. 164.404(c)-(d). | Send required plain-language event/date, PHI type, protective steps, mitigation, and contacts by required mail/agreed email; address decedents and urgent contact. | Privacy/HIM: notices/delivery records. |
| B05 | Substitute notice. 164.404(d)(2). | Apply permitted alternatives for fewer than 10 unreachable individuals; for 10+, qualifying 90-day web/media notice and toll-free contact active at least 90 days, with applicable next-of-kin exceptions. | Privacy/communications: posting/phone proof. |
| B06 | Media notice. 164.406. | For more than 500 residents of a state/jurisdiction, notify appropriate prominent media without unreasonable delay and within 60 calendar days. | Privacy/communications: geography/count/notices. |
| B07 | HHS reporting. 164.408. | For 500 or more individuals, report contemporaneously with individual notice; smaller breaches within 60 days after discovery-year end. Do not defer individual notice for smaller events. | Privacy/Legal: log/count/submission proof. |
| B08 | BA-to-hospital notification. 164.410. | Require notice without unreasonable delay, maximum 60 calendar days, affected identities and available information; negotiate shorter operational escalation where suitable. | Legal/Security: contracts/vendor notices. |
| B09 | Law-enforcement delay. 164.412. | Use specified written delay or documented oral request limited to 30 days unless writing follows; internal investigation is not an authorized delay. | Legal: official request/revised timing. |
| B10 | Administrative duties/burden of proof. 164.414; 164.530. | Preserve notice proof or documented non-breach basis, training/policy/sanction records, and investigation evidence under holds. | Privacy/Legal: protected case file. |

## 7. HITECH obligations and effects

HITECH is not a Fabric switch. Many effects are incorporated into the HIPAA rules above. [L02, L04, L16, L27, L31-L33]

| ID | Provision/effect | Implementation |
| --- | --- | --- |
| H01 | Direct BA security responsibility/liability; section 13401. | G03/S25/S48/P40: verify contracts and supplier controls; SaaS does not transfer all hospital responsibility. |
| H02 | Breach notification; section 13402. | Implement B01-B10 across Microsoft, partners, and the hospital. |
| H03 | BA privacy-contract compliance; section 13404. | P01/P40: constrain purposes/delegated duties and flow restrictions downstream. |
| H04 | Self-paid restrictions, minimum necessary, EHR accounting, electronic access, remuneration; section 13405. | Implement P02/P13/P25/P27-P32, including service-level payer suppression, scoped electronic release, and the statutory accounting distinction below. [L33] |
| H05 | Marketing/fundraising changes; section 13406. | P12/P20: authorization/remuneration handling and reliable opt-out suppression. [L33] |
| H06 | Enforcement/audits/penalties; sections 13409-13411 and Part 160. | Preserve operating evidence, cooperate with lawful oversight, and remediate failures. Do not hard-code inflation-adjusted penalties. [L01, L33] |
| H07 | Recognized security practices; section 13412 / 42 USC 17941. | Evidence of actual implementation in the preceding 12 months may affect enforcement/audit/remedy consideration. Use NIST/HHS practices; this is not immunity or mandatory product certification. [L26, L29, L31] |
| H08 | Separate health-IT/PHR program duties where applicable. | Assess hospital EHR programs and non-HIPAA PHR products separately. Fabric is not automatically a certified EHR; FTC PHR notification is not a substitute for hospital HIPAA duties. [L32] |

**EHR accounting distinction:** HITECH 13405(c), 42 USC 17935(c), addresses expanded EHR disclosure accounting; current 164.528 retains TPO exclusions. This operational matrix follows the current regulation, with counsel review of the statute and implementation status. The statutory language is not merely a proposal. No finalized expanded accounting rule was identified in the legal research. Neither distinction justifies omitting risk-appropriate activity evidence or treating every internal query as a reportable disclosure. [L14, L33]

## 8. Electronic transactions and regulatory cooperation

Fabric may transform/analyze exchanges while qualified hospital EHR/EDI/clearinghouse components provide standards-compliant endpoints. Apply only the hospital's actual transaction/functions. [L01, L24, L25, L30]

| ID | Requirement | Implementation/evidence |
| --- | --- | --- |
| A01 | Standard transactions/operating rules; Part 162. | Validate applicable claims/encounters, eligibility, referrals/authorization, status, enrollment/disenrollment, payment/remittance, premiums, and coordination of benefits; retain conformance/error records. |
| A02 | Code sets/provider/employer identifiers; Part 162. | Maintain effective-dated terminology, NPI/EIN and applicable code sets; validate mappings and required licensed implementation guides. Fabric tables do not automatically provide EDI conformance. |
| A03 | Trading-partner/clearinghouse responsibilities. 162.915, 162.923, 162.930. | Avoid agreements/custom formats that defeat standards; validate translations and partner responsibilities. |
| A04 | Claims attachments/electronic signatures. [L25] | Plan applicable X12 275/277 Version 6020, adopted HL7 C-CDA/attachments guides, and signature requirements for May 26, 2028; limit to finalized transaction scope. |
| A05 | HHS cooperation/records/access. 160.310. | Produce required evidence through Legal-approved channels; preserve holds and authorized access without blanket regulator credentials. |
| A06 | Complaints/nonretaliation/remediation. 160.306, 160.316. | Maintain lawful cooperation/complaint channels, corrective actions, and supporting records; dashboard scores are not legal findings. |

## 9. Proposed architecture and product guardrails

This topology is a hospital design recommendation, not a HIPAA-mandated product arrangement.

```text
EHR / laboratory / imaging / billing / approved study sources
  -> approved authenticated/encrypted ingestion + quality checks
  -> restricted PHI engineering workspaces
       OneLake / Warehouse + reviewed pipelines/notebooks
       narrow workload identities + supported source-layer policies
       approved inbound restrictions + workload-specific outbound controls
  -> approved purpose/cohort release boundary
       clinical/operations application or protected reporting topology
       separate limited-data-set and validated de-identified release paths
  -> verified recipients / hospital patient-rights and disclosure workflows

Cross-cutting:
  Entra + hospital MFA/device policies + controlled privileged access
  classified data + explicitly supported protection/DLP policies
  identity/platform/SQL/OneLake/workload/release evidence -> SOC/archive
  independent recovery data + code/configuration + tested restoration
  hospital policies, legal authority, training, notices, rights, incident handling
```

### 9.1 Engineering and reporting are different admission decisions

| Boundary | Design | Important limitation |
| --- | --- | --- |
| PHI engineering | Use only item types compatible with selected workspace Private Link, OAP, and optional CMK; restrict public access explicitly and remove broad default/raw grants. | An endpoint alone does not deny public access. Unsupported items can block configuration. Source-layer policy must fit the chosen engine. [F05-F14, F24] |
| PHI reporting | Select a supported tenant-private-link or approved alternate reporting topology; scope report consumers separately from engineering admins. | Current detailed support excludes Power BI semantic models from workspace-level Private Link and from workspace CMK. This does not mean Power BI lacks all private connectivity or encryption. [F11, F12, F24, F25] |
| SSO reporting | Effective user must be authorized at the relevant source layer as well as the semantic model. | Model-only RLS/OLS cannot restrict a user who independently queries the source through a broader grant. [F09] |
| Fixed-identity reporting | A narrowly scoped connection identity can serve consumers without granting them direct source access; govern the connection and model separately. | Backend storage audit may identify that identity, requiring model/application correlation. Users with model Write permission do not receive ordinary model RLS/OLS protection. [F09] |
| Research releases | Enforce study approval, cohort/fields, recipient, expiration, extraction rules, and output review. Use synthetic/de-identified nonproduction data unless PHI is specifically approved. | Neither a domain nor a per-study workspace proves research authority or prevents screenshots/downloads. [L09, F30] |
| Geography/recovery | Choose hospital-approved regions and destinations; inventory telemetry, support, keys, and replicas. | US-only residency is not a universal HIPAA requirement; it can be a hospital contract/risk requirement. Region choice is not automatic full-service recovery. [L17, F27, F28] |

Where hospital policy requires **every PHI artifact to use workspace CMK and workspace-level Private Link**, a PHI semantic model/report cannot simply be placed in that boundary under the documented support constraints. Obtain an approved supported topology/control alternative, serve only validated de-identified data, or use another supported serving approach. Do not silently waive the policy or call a public endpoint inherently noncompliant: the actual legal/risk requirements and compensating controls must be assessed.

### 9.2 Authorization and identity guardrails

| Area | Current behavior | Required response |
| --- | --- | --- |
| OneLake roles/defaults | Grant-only roles; no Deny-role mechanism. Lakehouse/mirrored-database DefaultReader tracks ReadAll; mirrored-catalog DefaultReader can track Read. ReadWrite roles cannot carry RLS/CLS. [F06] | Remove/narrow broad grants and unioned role memberships; separate ordinary readers from writers/policy administrators. |
| Privileged roles | Admin/Member/Contributor generally have elevated write/data access, but current SQL user-identity documentation applies RLS even to these roles. Administrators can still alter policy or use other access paths. [F06, F07] | Avoid both false claims: neither universal privilege-proof security nor universal bypass of every engine rule is established. Test actual paths. |
| SQL delegated identity | New SQL analytics endpoints default to delegated mode: storage uses owner identity; SQL-layer policy authorizes users rather than their OneLake policies. [F07] | Use properly administered SQL controls, or adopt user-identity mode when OneLake enforcement is the intended control. Do not assume policy inheritance. |
| SQL user identity | OneLake table authorization applies; nondata SQL objects retain SQL permissions. Synchronization can take up to five minutes. Mode switching affects workspace endpoints/queries and can remove roles/inline objects. [F07] | Change-control, preserve definitions/grants, validate propagation and deny tests before PHI access. Check ownership-chain and cached-token hazards. |
| Warehouse shortcuts | Warehouse SQL policies are not automatically raw OneLake shortcut policies. [F07] | Test source SQL and raw/shortcut access separately; restrict independent file grants. |
| Direct Lake | SSO is the default interactive identity; fixed identities are explicit options. Direct Lake on SQL and on OneLake use different permission paths. SQL RLS can cause DirectQuery fallback; on-OneLake has no DirectQuery fallback. [F09] | Record model mode, effective identity, framing/refresh identity, source rules, fallback, and independent source grants. |
| Engines/status | Current OneLake secured-data guidance lists GA filtering for several native paths; Eventhouse RLS and authorized third-party integration remain preview. Unauthorized engines are handled as direct user access and unsafe filtered reads can be blocked. [F08] | Admit exact engine/version/feature combinations; do not label all OneLake security preview or all third-party reads prohibited. |
| Metadata | Column/table names can remain discoverable through errors, model metadata, Git, or privileged access. [F06, F07, F09] | Never place patient identifiers in schema/item names; restrict metadata-bearing repositories and Build/Write permissions. |
| Workspace identity | Automatically managed service principal, GA; authorized creators/users can assume it. It is not identical in lifecycle/governance to an Azure managed identity. [F36] | Grant only necessary source access; control connection users and preserve recovery/rebinding procedures. |
| Conditional Access | Resource targeting should include documented Fabric dependencies; Fabric does not support CAE session control. General CA requires Entra P1/equivalent entitlement; risk-based policies require P2. [F15, F16] | Confirm staff/guest and workload policies, licensing, exceptions, real revocation latency, and API/client behavior. No promise of workspace-specific CA targeting. |

### 9.3 Network, keys, labeling, and compatibility

| Area | Guardrail |
| --- | --- |
| Tenant versus workspace Private Link | Different scope and support lists. Workspace restrictions do not govern admin APIs or the network-communication-policy API in the same way; tenant restrictions remain relevant. SQL private connection strings/FQDNs exist, including workspace-specific routing/DNS requirements. [F10-F12] |
| Cross-workspace traffic | A client endpoint to two workspaces does not establish server-to-server connectivity. Use a documented MPE or appropriate gateway path for the actual workload; do not promise arbitrary Direct Lake topology or insist every path must become DirectQuery. [F13] |
| Outbound protection | Inbound Private Link does not replace outbound controls. Apply the supported workload mechanism, destinations and granularity; portal configuration of data connection rules is limited under Private Link and APIs are needed. [F14, F35] |
| CMK | Workspace CMK is additional envelope-key control, not HIPAA's universal required key model. Unsupported items cannot be mixed into a CMK workspace; particular metadata/caches/libraries remain outside CMK scope. Rotation/revocation need clinical/recovery testing. [F24] |
| Power BI BYOK | Separate capacity-level control for supported imported semantic-model data, with exclusions. It does not establish CMK coverage of every report, Direct Lake file, connection, or artifact. [F25] |
| Labels/protection | Classification alone is not authorization/de-identification. Applicable Purview protection policies can add access restrictions for supported tenant scenarios; publishing policies protect supported PBIX/export paths. CSV/text and cross-tenant paths do not inherit universal label access protection. [F37] |
| DLP | Validate supported items, content, triggers, policies, and licensing. Access restriction is preview; semantic-model evaluation has documented service-principal gaps. DLP does not universally prevent downloads or screenshots. [F23] |
| Private-network UX | Current tenant Private Link documentation excludes Copilot, Capacity Metrics, and the Govern tab in relevant private-network scenarios and identifies information-protection/desktop dependencies. Use approved monitoring/admin alternatives, not temporary unapproved public exposure. [F12] |
| Source conflicts | The availability matrix and detailed pages differ for examples such as default semantic models/Dataflow variants and Power BI OAP. The data-connection-rules page still labels its UI section preview while the matrix lists some supported workloads GA. [F05, F11, F14, F35] |

**Conflict handling:** Preserve the source/date and exact item variant, obtain Microsoft confirmation where the conflict affects a required control, and demonstrate the supported path with synthetic data. Until contractual eligibility and behavior are established, record the dependency as unresolved; do not choose whichever documentation statement is more convenient.

## 10. Audit, attribution, integrity, and recovery design

HIPAA requires mechanisms to record/examine activity appropriate to risk, not an invented universal requirement to log every cell. Nevertheless, proving effective access, investigating incidents, and providing required patient disclosure accountings require evidence beyond a single query log. [L04, L06, L14]

| Evidence source | Use | Gap/control response |
| --- | --- | --- |
| Entra identity/group/change records | Sign-ins, user/guest lifecycle, group membership and workload identity events. | Archive approved history and correlate immutable principal IDs; current membership alone cannot answer past authorization. |
| Fabric/Purview audit | Supported item/user/admin activities and changes. | Verify enablement, operation coverage, roles, licensing, retention, and tenant-controlled delivery. Current Purview permits Audit Reader/Manager roles; Exchange roles still matter for particular cmdlets/enablement. [F20-F22] |
| Warehouse SQL audit | Enabled authentication, permissions, schema/query/action groups. | Off by default; high load can lose configured events. Predicates filter before write and only affect enabled actions. Preserve required events without PHI-heavy overcollection. [F17] |
| SQL endpoint audit | Supported SQL actions/query events. | Lakehouse mutations through other engines are not SQL DML audit. Underlying Lakehouse XEL browsing/download is unsupported; use documented `sys.fn_get_audit_file_v2`. [F17] |
| OneLake diagnostics | UI/API storage operations and engine-related access events. | Engine events may represent temporary grants/shared backend identities; correlate with workload evidence. Not a guaranteed per-patient read/disclosure ledger. [F18] |
| Current Monitoring Item | Supported workload operational logs/metrics and investigation. | Collection initially off, no backfill; default 30-day retention can be changed. Same-region centralized destinations and read-only databases do not make all telemetry an immutable security ledger. [F19] |
| Permission snapshots plus changes | Historical workspace/item/OneLake/SQL/model/group/connection access reconstruction. | Retain effective-dated snapshots and events across layers, including identity mode and policy definition. A SQL grant event alone is not a full historical authorization graph. |
| Release/application ledger | Patient, legal recipient, scope, purpose, authority, time, restrictions, and actual release. | Protect as PHI; support accounting and breach analysis. Internal reads and external disclosures are not interchangeable. [L14] |
| Independent evidence archive | Required documented reviews, investigations, releases and selected underlying evidence. | Protect against deletion/retention changes, validate ingestion, redact unnecessary PHI, and test recovery; choose lawful retention/holds. |

**Protect the logs:** Warehouse audit files are readable by specified workspace roles, including Viewer with ReadAll, and statement text can contain PHI. Deleting a warehouse can delete its audit files. `VIEW DATABASE SECURITY AUDIT` can support narrower query access. OneLake diagnostic JSON immutability does not prevent deletion of its workspace/lakehouse container; guard privileged administration and retain required independently recoverable evidence. [F17, F18]

**Measure latency and losses:** OneLake diagnostics can take up to an hour to start collection. SQL user-identity synchronization and delegated-owner token caching introduce distinct authorization delays. Tests must show initiating user, model/query identity, storage identity, action, object, result, and available correlation IDs for each supported path, including failed collection and capacity pressure. [F07, F18, F19]

**Recovery is not redundancy:** OneLake replication, soft deletion, time travel, snapshots, Multi-Geo, and Git each cover particular failure modes. They are not an independent complete ransomware backup. Capacity DR uses supported paired regions and asynchronous replication; tenant home-region dependencies and unsupported definitions/items still matter. Rebuild notebooks/models/GraphQL/configuration where required, restore consistent data, reapply permissions/connections, and validate downstream consumers. [F26-F28]

Use supported consistent snapshot/copy/export methods for the chosen item; copying live Delta files without transaction consistency is not proof of an exact recoverable copy. Preserve code, schemas, security definitions, environment settings, schedules, network/DNS configuration, key recovery, and external dependencies separately. Restored shared-item permissions and warehouse snapshots are not universally reinstated by item recovery. [F26, F27]

## 11. Deadlines and retention

| Obligation/source | Current treatment |
| --- | --- |
| Individual access | 30 days; one additional 30-day extension with timely written reason/date. [L12] |
| Amendment | 60 days; one additional 30-day extension with timely written reason/date. [L13] |
| Accounting | 60 days; one additional 30-day extension; first accounting in 12 months free; current six-year lookback/exclusions. [L14] |
| Required Security/Privacy documentation | Six years from creation or last effective date, whichever is later. [L08, L15] |
| Individual/BA breach notice | Without unreasonable delay; maximum 60 calendar days after discovery, subject to lawful delay. [L16] |
| Media/HHS large breach | Media: more than 500 residents in a state/jurisdiction. HHS: 500 or more individuals, contemporaneous with individual notices. [L16] |
| HHS small breach | Within 60 days after discovery-year end; individual notices remain timely. [L16] |
| Decedent protection | 50 years after death, not a mandatory record-retention period. [L09] |
| Part 2/applicable NPP updates | February 16, 2026, already past. [L20, L23] |
| Claims attachments | May 26, 2028 compliance for finalized scope. [L25] |
| Purview Audit | Standard default 180 days; longer retention is workload/policy/license-dependent. Premium's automatic one-year policy for specified workloads does not prove Fabric records have that retention. Archive required evidence before expiration. [F21, F22] |
| Current workspace monitoring | Default 30 days, configurable via Eventhouse retention policies. Increasing a policy does not create activity never collected. [F19] |
| Workspace/item recovery | Collaborative workspace default 7 days, configurable 7-90; My workspace 30. Supported item default now 3 days, configurable 3-90, preserving explicit prior settings; not all items supported. [F26] |
| OneLake deleted files | Built-in seven-day soft deletion is a limited recovery mechanism, not six-year evidence retention or independent backup. [F28] |
| Medical records/general raw logs | Determine from applicable laws, contracts, holds, rights, and risk; no universal HIPAA raw-log/medical-record six-year or seven-year rule is asserted. |

A conservative selected-evidence archive aligned to required six-year documented-control obligations is a **hospital design choice**, not a requirement to keep every PHI-bearing raw log six years. HIPAA does not universally prescribe a particular MFA/CMK/private-link product, annual risk assessment, US-only hosting, or fixed RPO/RTO. Approve review frequencies and recovery targets based on actual risk and clinical safety. [L03-L08, L17]

## 12. Review/update of the original HIPAA controls table

The original root file is a useful starter, not a complete compliance inventory. This section supplies its corrections without overwriting the original reference. It also replaces placeholder references and incomplete table formatting in this deliverable.

| Original control | Updated interpretation/implementation | Cross-reference |
| --- | --- | --- |
| At-rest encryption | Default encryption is a mechanism; assess all data locations. Workspace CMK and Power BI BYOK have different scopes, exclusions, and compatibility. | S40; F01, F24, F25 |
| Transit encryption | Fabric inbound TLS does not validate every connector/customer-code/recipient path. Require certificate validation and end-to-end flow review. | S45-S47; F01 |
| Unique identification | Use individual and separate workload identities; distinguish initiator/query/storage identities and restrict shared connections. | S37, S44; F07, F09, F36 |
| Audit controls | Validate Purview/Fabric audit separately from SQL auditing, OneLake diagnostics, and operational monitoring. Enable necessary sources, scope privileges, archive evidence, and test gaps. | S05, S41; section 10 |
| Integrity | Encryption, redundancy, and lineage are insufficient alone. Add write/change control, source reconciliation, data validation, manifests, and independently recoverable copies. | S42-S43, S19 |
| Person/entity authentication | Entra MFA/device controls are recommended hospital safeguards; validate workload and downstream resource authentication rather than claiming MFA is universally prescribed by current HIPAA. | S44; F15, F16, F36 |
| Risk management | Actual all-ePHI assessment and remediation are required. Defender/Compliance Manager templates do not automatically inspect every Fabric control or certify compliance. | S01-S03, S24 |
| Access management | Workspace RBAC/labels/DLP are not universal source-layer cohort protection. Validate OneLake/SQL/model policies, supported label protection, defaults, writer power, and alternate paths. | S11-S12, S36; F06-F09, F23, F37 |
| Workforce security | Clearance/approval, supervision, termination, sanctions, training, and recertification are hospital processes; measure revocation delays. | S04, S07-S17 |
| Incidents | SOC tooling supports detection; hospital Privacy/Legal determines breach and performs notices. Preserve evidence and test vendor escalation. | S18; B01-B10 |
| Contingency | Multi-Geo/redundancy is not a tested complete DR/backup plan. Preserve consistent data and definitions/configuration; exercise clinical fallback and reconstruction. | S19-S23; F26-F28 |
| Facility controls | Provider facility assurances cover provider infrastructure, not hospital offices, gateway/network rooms, visitor controls, or endpoints. | S26-S29 |
| Workstation controls | Managed-device/session controls help, but DLP does not universally prevent local copies, screenshots, or unsupported exports. | S30-S31, S39; F23, F37 |
| Device/media controls | Hospital retains export/portable-media/sanitization/accountability duties; SQL deletion is not proof that caches, retained files, and backups are gone. | S32-S35, P39-P40 |

Missing original coverage is supplied by the contracting, Privacy Rule/patient-rights, conditional disclosure, breach, HITECH, transaction, retention, and readiness sections.

## 13. Review of all 57 existing Fabric statements

**Status key:** Confirmed = supported within the stated scope; Qualified = partly correct but overbroad; Updated = current documentation changes the statement; Unresolved = source conflict or insufficient verification; Design check = a generic requirement needing deployment-specific evidence. These statuses are product/research findings and assessment questions, not a live tenant assessment.

| Review ID / original # | Status | Corrected finding and required action | Basis |
| --- | --- | --- | --- |
| R01 / 1 | Confirmed; dated | October 1 means October 1, 2026. New-customer delivery changes; existing managed-solution support ends December 31, 2027. Customer-managed package available August 3, 2026. | F31 |
| R02 / 2 | Confirmed; scope | Detailed guidance excludes Power BI semantic models from workspace-level Private Link. Do not generalize this to tenant-level Private Link or all Power BI private access. Matrix/default-model conflicts need resolution. | F05, F11, F12 |
| R03 / 3 | Updated | New Monitoring Item supports Private Link; legacy monitoring does not. Do not disable production private restrictions casually to follow a troubleshooting suggestion. | F19 |
| R04 / 4 | Confirmed; scope | Copilot is not supported in the documented private/closed network scenarios. Verify exact tenant/workspace feature eligibility; do not move PHI to public/unapproved AI to work around it. | F12 |
| R05 / 5 | Updated | SQL private FQDN/connection strings exist. Workspace routing uses the documented z-segment/privateLinkType and DNS configuration; actual Databricks/client connectivity still needs proof. | F10, F11 |
| R06 / 6 | Qualified | Arbitrary cross-private-boundary Direct Lake is not guaranteed; a gateway/DirectQuery path is an option, not universal proof that every Direct Lake/private topology is impossible. Validate exact workload and supported MPE/gateway path. | F09, F13 |
| R07 / 7 | Updated | Current monitoring defaults to 30 days; retention is configurable. This is not a fixed maximum. | F19 |
| R08 / 8 | Qualified | Deployment pipelines have documented workspace-private-link restrictions; Git is supported. fabric-cicd is not the sole possible deployment mechanism: supported APIs/approved tooling can also fit. | F11 |
| R09 / 9 | Confirmed; scope | Workspace-private-link item sharing is unsupported and existing shared links can cease working. Review consumer access before enabling restrictions. | F11 |
| R10 / 10 | Qualified | Delegated SQL storage access uses owner identity and does not apply the end user's OneLake policy. SQL/model effective-user authorization and audit correlation still exist; attribution is not automatically wholly lost. | F07, F09 |
| R11 / 11 | Qualified | Broad workspace roles retain substantial privileged power, but current SQL user-identity RLS applies even to Admin/Member/Contributor. Avoid the absolute claim that every rule is always bypassed. | F06, F07 |
| R12 / 12 | Confirmed; scope | Lakehouse/mirrored-database DefaultReader tracks ReadAll; mirrored catalogs use Read. Inspect/remove broad defaults and unioned role grants. | F06 |
| R13 / 13 | Qualified | User-identity mode is needed for end-user OneLake table policy enforcement at that SQL endpoint. Delegated mode can use properly configured SQL-layer controls; switching modes needs change-control. | F07 |
| R14 / 14 | Confirmed | ReadWrite OneLake roles cannot contain RLS/CLS constraints. Separate governed readers from writers. | F06 |
| R15 / 15 | Updated | Permission-change evidence exists, including SQL audit. A complete historical effective-access graph is not automatically supplied; preserve multi-layer snapshots and change events. | F17, F20 |
| R16 / 16 | Confirmed | Warehouse/SQL endpoint auditing is off by default and must be enabled/configured. | F17 |
| R17 / 17 | Confirmed | SQL auditing can miss configured events under high load. Do not promise a mathematically complete all-engine access ledger. | F17 |
| R18 / 18 | Qualified | Warehouse XEL roles include Admin/Member/Contributor and Viewer with ReadAll; statements can contain PHI, not necessarily always. SQL endpoint file-browsing limitations differ. | F17 |
| R19 / 19 | Qualified | Direct Lake reads Delta through OneLake; SQL auditing does not cover all those reads. SQL permission/metadata checks or DirectQuery fallback can still occur. | F09, F17 |
| R20 / 20 | Qualified | Owner/delegated engine access can appear under backend identity or temporary grants. Correlate diagnostics with query/model/initiator evidence; do not assume every event has identical attribution. | F07, F09, F18 |
| R21 / 21 | Confirmed | SQL audit predicates act before write; excluded events cannot be recovered from that audit stream. | F17 |
| R22 / 22 | Confirmed | Audit predicates apply only to enabled actions/action groups. | F17 |
| R23 / 23 | Qualified | Native lineage displays relationships; no complete immutable historical provenance ledger is established. Capture required versions/history and external relationships rather than claiming no history exists anywhere. | F29 |
| R24 / 24 | Qualified; scope | Fabric tracking guidance names Exchange Audit Logs, while current Purview allows Audit Reader/Manager roles for search/export; Exchange roles remain relevant to cmdlets/enablement. Establish approved least-privilege access or central SOC evidence delivery for the chosen route. | F20, F21 |
| R25 / 25 | Qualified | Roles and OAuth scopes are distinct requirements, not universally interchangeable alternatives. The documented preview List Items admin API requires an admin user with delegated scopes or appropriately authorized app identity. Follow each endpoint's actual permissions/support. | F32 |
| R26 / 26 | Unresolved; version-specific | Earlier monitoring guidance prohibited simultaneous Log Analytics configuration; current Monitoring Item overview omits that limitation. Omission does not prove support. Confirm chosen generation/configuration with Microsoft and test before relying on coexistence. | F19 |
| R27 / 27 | Qualified; legacy | No log-type/category ingestion filtering is documented for legacy monitoring. Do not extrapolate that restriction to every current workload's collection controls. | F19 |
| R28 / 28 | Confirmed | Monitoring consumes Fabric capacity; size/cost it as a PHI-bearing operational dependency. | F19 |
| R29 / 29 | Confirmed; scope | Power BI/Activator consumers respect capacity throttling; monitoring ingestion/Eventhouse queries/Real-Time Dashboards have separately documented behavior. | F19 |
| R30 / 30 | Qualified; legacy | Member/Admin sharing statement is specifically documented for legacy monitoring; Contributor query access is separately documented. Verify current item permissions without promoting users unnecessarily. | F19 |
| R31 / 31 | Qualified; legacy | Read-only monitoring database remains documented. Approximate 15-minute recreation wait belongs to legacy monitoring, not a general current recovery SLA. | F19 |
| R32 / 32 | Qualified | Semantic models/reports are outside workspace CMK scope. Supported import-model Power BI BYOK is separate and narrower; neither justifies labeling an entire reporting workspace CMK-protected. | F24, F25 |
| R33 / 33 | Qualified | Fabric guidance does not establish workspace/domain CA targeting. CA has user/group, resource, location/device/risk/session conditions, not only group scoping. Use complementary workspace/data/network controls. | F15, F16 |
| R34 / 34 | Confirmed | Fabric does not support CAE session control. Measure revocation and apply appropriate alternatives. | F15 |
| R35 / 35 | Confirmed; scope | General CA requires P1/equivalent entitlement; risk-based policies require P2, and related device/workload/session controls need their own licensing. | F16 |
| R36 / 36 | Confirmed | Domain assignment does not determine item visibility/access. Domains organize/delegate governance; they are not a security boundary. | F30 |
| R37 / 37 | Qualified | Tenant-administered labels, DLP, and audit do not mean every policy has indiscriminate tenant-wide applicability or every consumer has tenant-wide evidence access. Validate published users/groups, locations/items, policy scope, and roles. | F20-F23, F37 |
| R38 / 38 | Confirmed; scope | Governance/admin experiences consolidate in OneLake Govern; tenant Private Link documentation says the Govern tab is unavailable in that scenario. Maintain an approved alternative evidence/admin path. | F12, F33 |
| R39 / 39 | Confirmed | Capacity Metrics app has documented Private Link limitations. Select an approved monitoring alternative. | F12 |
| R40 / 40 | Qualified | Managed VNet disables starter pools in the documented scenario. Three-to-five-minute startup is not an established SLA; measure actual startup and clinical impact. | F12 |
| R41 / 41 | Confirmed; scope | Private Link limits portal configuration of OAP data connection rules; use appropriate cloud/gateway REST APIs, with required identity and network controls. | F14, F35 |
| R42 / 42 | Confirmed; scope | SQL database lacks workspace-level Private Link support in current detailed guidance; tenant-level Private Link is supported. | F11, F12 |
| R43 / 43 | Unresolved; conflicting status | Data-connection-rules instructions still label the UI preview; availability matrix lists several workload paths GA and others preview. Confirm the exact feature/version/BAA eligibility, not a blanket all-preview or all-GA claim. | F05, F14, F35 |
| R44 / 44 | Confirmed; scope | Current secured-engine table labels Eventhouse RLS and authorized third-party integration preview; several native enforcement paths are GA. | F08 |
| R45 / 45 | Confirmed as default contract caution | Core Online Services exclude Previews. Inspect governing DPA/BAA and any explicit feature exception; do not admit PHI based on preview availability or a generic compliance statement. | F02 |
| R46 / 46 | Qualified | Nonauthorized engines are treated as direct user access; unsafe RLS/CLS data reads are blocked where the engine cannot safely filter. Not every unfiltered/unrestricted third-party read is universally prohibited. | F08 |
| R47 / 47 | Confirmed | OneLake/data-model metadata is not guaranteed hidden; names can appear in errors and metadata APIs/Git. Keep PHI out of schema names. | F06, F07, F09 |
| R48 / 48 | Qualified | Absence from a generic page is not an exclusion test. Current Product Terms explicitly list Microsoft Fabric in Azure Core Services; healthcare compliance documentation names Fabric/BAA. Verify actual governing scope. | F02-F04 |
| R49 / 49 | Qualified | No turnkey Fabric feature is established here as automatic legal de-identification. Fabric transformations can implement validated methods; an external service is optional and independently reviewed. | L09, L18 |
| R50 / 50 | Qualified | This research did not establish a complete Fabric-specific TRE/airlock blueprint. Azure TRE is a Microsoft-hosted accelerator with best-effort community/maintainer support, not a Fabric certification or guaranteed commercial service. | F34 |
| R51 / 51 | Updated | Microsoft publishes a Fabric security scenario explicitly using US patient EHR/lab/insurance/wearable data. That refutes the absolute absence claim, but not the need for a hospital-specific approved PHI/TRE design. | F01 |
| R52 / 52 | Design check | Obtain serving-workspace public-access policy, memberships, OneLake grants, model/source permissions, and identity mode. If model-only RLS is proposed, verify consumers have no independent broader source access. | Required configuration evidence; F06-F09 |
| R53 / 53 | Design check; technical qualification | Verify key coverage per artifact. Workspace CMK cannot be assumed for all reporting artifacts; distinguish source CMK, model BYOK, default encryption, and any protection designation's actual scope. | Required artifact/settings map; F24, F25 |
| R54 / 54 | Design check | Require precise NIST control/revision, applicable requirement, evidence, scope, and equivalent alternatives in architecture comparisons. HIPAA does not automatically mandate every 800-53 control. | Required assessment/crosswalk; L26 |
| R55 / 55 | Design check | Inventory shared workspaces, study/environment units, per-unit multipliers, and separately counted recovery overhead. Compare complete footprints with consistent assumptions; do not confuse per-unit counts with estate totals. | Required inventory/counting method |
| R56 / 56 | Design check | Require author/owner, version, date, sources, exact item variants, assumptions, and approved change history before relying on version-sensitive architecture claims. | Required document control |
| R57 / 57 | Design check | Verify approved staff/guest CA, any required Defender for Cloud Apps session policy, label/protection assignments, licenses, owners, and tests. Required unavailable safeguards remain blockers until supported alternatives are approved. | Required approvals/settings/tests; F15, F16, F37 |

These reusable design checks identify evidence an implementation assessment must obtain. They do not report customer-specific topology, portfolio counts, proposal defects, or institutional approval decisions.

## 14. Production-readiness gates and implementation order

Use synthetic data first. An applicable failed gate is unresolved, not compliant merely because a feature exists.

| Gate | Acceptance condition | Owner/evidence |
| --- | --- | --- |
| Contract/scope | Covered relationships, governing BAA/DPA, services/features/regions, and conditional applicability approved. | Legal/Privacy: G01-G06, P40-P42. |
| Tenant dependencies | Required CA, audit delivery, device/session policies, labels/protection/DLP and licenses actually granted or supported alternatives approved. | Tenant/Security: approvals/settings/tests. |
| Artifact compatibility | Exact engineering/reporting items work with intended private restrictions, OAP, keys, monitoring, and deployment; documentation conflicts resolved. | Platform: item inventory/vendor proof. |
| Legal use/restrictions | Positive/negative tests for purpose, authorization expiry/revocation, self-paid encounters, representatives/minors, psychotherapy, Part 2, and study approvals. | Privacy/Clinical/Data: expected results. |
| Least privilege | Ordinary consumers cannot bypass cohort/column restrictions through SQL, Spark, files, shortcuts, APIs, models/XMLA, exports, or credentials. Privileged changes monitored. | Data/Platform: path/role matrix. |
| Identity/revocation | Each path shows initiator/effective/backend identities; termination tests measure caches/sessions/propagation and prevent ongoing unauthorized access. | Security/Platform: attribution/revocation tests. |
| Network/egress | Public-denial, private DNS/routing, server-to-server paths, gateway destinations, and blocked exfiltration verified for each admitted workload. | Platform: allowed/denied path evidence. |
| Keys/exports | PHI locations mapped to actual encryption/key controls; rotation/revocation/recovery and supported labeled exports tested. No blanket workspace claims. | Security/Platform: key/location results. |
| Evidence | Sources enabled before PHI; activity correlation, access history, latency, overload/loss, permissions, collection failure, retention, and protected recovery tested. | SOC: section 10 evidence. |
| Recovery/clinical fallback | Exact consistent data recovery plus definitions/security/connections/keys/consumers demonstrated within approved RPO/RTO; shared grants validated after restore. | Platform/Clinical: measured drill. |
| Rights/disclosure | Real HIM process can retrieve, amend, propagate, account, and securely release with lawful timing/fees/authority; ledgers complete for their scope. | HIM/Privacy: end-to-end cases. |
| Hospital operations | Officers, policies, sanctions, training, physical/media controls, complaints, vendor incidents, breach communications, and periodic evaluation in operation. | Leadership/HR/Privacy/Security: operating records. |

Implementation order: settle legal/contract/feature scope and tenant dependencies; choose supported engineering/reporting boundaries; implement identity/data/network/key controls; establish evidence and rights/release processes; engineer independent recovery and clinical fallback; complete synthetic-data drills and approval before admitting production PHI. Reassess after material changes.

## 15. Coverage map and assessment boundary

| Regulatory area | Mapped entries |
| --- | --- |
| General applicability/preemption/contracts/platform admission | G01-G06, S25, S48, P40-P42 |
| Security general/administrative/physical/technical/organizational/documentation | S01-S54 |
| Privacy purposes/authority/de-identification/rights/administration | P01-P42 |
| Conditional 164.512 disclosure categories | DCL01-DCL12 |
| Breach Notification Rule | B01-B10 |
| HITECH effects and separate-program applicability | H01-H08 |
| Part 162 transactions/Part 160 cooperation | A01-A06 |
| Review of existing product/project claims | R01-R57; not additional legal requirements |

**Assessment boundary:** This report is a comprehensive PHI-platform control inventory and an updated review, not a finding that a deployed hospital environment complies. The hospital must resolve applicability, contracts, documentation conflicts, technical implementation, operating effectiveness, rights, recovery, and Legal/Privacy approval. Unsupported features and unavailable required tenant controls remain gaps.

## 16. Primary-source register

Sources researched September 30, 2026. Preserve governing versions for an actual assessment. Legal source IDs intentionally match the Databricks report. Read eCFR together with current HHS litigation guidance; the same-date legal research inspected the official September 28, 2026 eCFR snapshot where automated HTML access was limited.

### 16.1 Law, regulator guidance, and NIST

| ID | Source | URL |
| --- | --- | --- |
| L01 | eCFR, 45 CFR Part 160 | `https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-160` |
| L02 | eCFR, 45 CFR Part 164 | `https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164` |
| L03 | eCFR, 164.306 general security | `https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-C/section-164.306` |
| L04 | eCFR, 164.308 administrative safeguards | `https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-C/section-164.308` |
| L05 | eCFR, 164.310 physical safeguards | `https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-C/section-164.310` |
| L06 | eCFR, 164.312 technical safeguards | `https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-C/section-164.312` |
| L07 | eCFR, 164.314 organizational duties | `https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-C/section-164.314` |
| L08 | eCFR, 164.316 documentation | `https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-C/section-164.316` |
| L09 | eCFR, Privacy Rule Subpart E | `https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-E` |
| L10 | eCFR, 164.520 notices | `https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-E/section-164.520` |
| L11 | eCFR, 164.522 privacy protections | `https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-E/section-164.522` |
| L12 | eCFR, 164.524 access | `https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-E/section-164.524` |
| L13 | eCFR, 164.526 amendments | `https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-E/section-164.526` |
| L14 | eCFR, 164.528 accounting | `https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-E/section-164.528` |
| L15 | eCFR, 164.530 administration | `https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-E/section-164.530` |
| L16 | eCFR, Breach Notification Rule Subpart D | `https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-D` |
| L17 | HHS, HIPAA/cloud computing | `https://www.hhs.gov/hipaa/for-professionals/special-topics/health-information-technology/cloud-computing/index.html` |
| L18 | HHS, de-identification guidance | `https://www.hhs.gov/hipaa/for-professionals/special-topics/de-identification/index.html` |
| L19 | HHS, reproductive rule/partial vacatur | `https://www.hhs.gov/hipaa/for-professionals/special-topics/reproductive-health/index.html` |
| L20 | HHS, February 2026 model NPPs | `https://www.hhs.gov/hipaa/for-professionals/privacy/guidance/model-notices-privacy-practices/index.html` |
| L21 | HHS, Security Rule NPRM/January 6, 2025 proposed text | `https://www.hhs.gov/hipaa/for-professionals/security/hipaa-security-rule-nprm/index.html`; `https://www.govinfo.gov/content/pkg/FR-2025-01-06/pdf/2024-30983.pdf` |
| L22 | OIRA, RIN 0945-AA22 agenda entry; displayed 2026 publication | `https://www.reginfo.gov/public/do/eAgendaViewRule?RIN=0945-AA22&pubId=202510` |
| L23 | HHS, Part 2 | `https://www.hhs.gov/hipaa/part-2/index.html` |
| L24 | CMS, Administrative Simplification | `https://www.cms.gov/training-education/look-up-topics/hipaa-administrative-simplification` |
| L25 | CMS, claims attachments/signatures final rule | `https://www.cms.gov/newsroom/fact-sheets/administrative-simplification-adoption-standards-health-care-claims-attachments-transactions` |
| L26 | NIST SP 800-66 Rev. 2, Security Rule guidance/crosswalk | `https://csrc.nist.gov/pubs/sp/800/66/r2/final` |
| L27 | HHS, Security Rule/HITECH summary | `https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html` |
| L28 | HHS, Ciox access court-order notice | `https://www.hhs.gov/hipaa/court-order-right-of-access/index.html` |
| L29 | HHS, security guidance/recognized practices | `https://www.hhs.gov/hipaa/for-professionals/security/guidance/index.html` |
| L30 | eCFR, 45 CFR Part 162 | `https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-162` |
| L31 | US House OLRC, 42 USC 17941 recognized practices | `https://usc-cdn.house.gov/view.xhtml?edition=prelim&num=0&req=granuleid%3AUSC-prelim-title42-section17941` |
| L32 | US House OLRC, 42 USC 17937; FTC PHR breach guidance | `https://usc-cdn.house.gov/view.xhtml?req=granuleid%3AUSC-prelim-title42-section17937&num=0&edition=prelim`; `https://www.ftc.gov/business-guidance/health-breach-form` |
| L33 | US House OLRC, 42 USC 17935/17936/17940 | `https://usc-cdn.house.gov/view.xhtml?req=granuleid%3AUSC-prelim-title42-section17935&num=0&edition=prelim`; `https://usc-cdn.house.gov/view.xhtml?req=granuleid%3AUSC-prelim-title42-section17936&num=0&edition=prelim`; `https://usc-cdn.house.gov/view.xhtml?req=granuleid%3AUSC-prelim-title42-section17940&num=0&edition=prelim` |

### 16.2 Official Microsoft/Fabric product and contract sources

| ID | Source | URL |
| --- | --- | --- |
| F01 | Fabric security/US healthcare scenario | `https://learn.microsoft.com/en-us/fabric/security/security-scenario` |
| F02 | Product Terms: Core Online Services, Fabric, preview exclusions, DPA link | `https://www.microsoft.com/licensing/terms/product/PrivacyandSecurityTerms/all` |
| F03 | Azure HIPAA/BAA/shared responsibility | `https://learn.microsoft.com/en-us/azure/compliance/offerings/offering-hipaa-us` |
| F04 | Healthcare data solutions compliance | `https://learn.microsoft.com/en-us/industry/healthcare/healthcare-data-solutions/compliance` |
| F05 | Fabric security feature availability matrix | `https://learn.microsoft.com/en-us/fabric/security/security-feature-availability` |
| F06 | OneLake data-access control model | `https://learn.microsoft.com/en-us/fabric/onelake/security/data-access-control-model` |
| F07 | SQL endpoint OneLake identity/security modes | `https://learn.microsoft.com/en-us/fabric/onelake/security/sql-analytics-endpoint-onelake-security` |
| F08 | OneLake secured-data engines/status | `https://learn.microsoft.com/en-us/fabric/onelake/security/read-secured-data` |
| F09 | Direct Lake security/identities/policy scope | `https://learn.microsoft.com/en-us/fabric/fundamentals/direct-lake-security-integration` |
| F10 | Workspace-level Private Link overview | `https://learn.microsoft.com/en-us/fabric/security/security-workspace-level-private-links-overview` |
| F11 | Workspace Private Link supported scenarios | `https://learn.microsoft.com/en-us/fabric/security/security-workspace-level-private-links-support` |
| F12 | Tenant Private Link/limitations | `https://learn.microsoft.com/en-us/fabric/security/security-private-links-overview` |
| F13 | Cross-workspace network communication | `https://learn.microsoft.com/en-us/fabric/security/security-cross-workspace-communication` |
| F14 | Workspace OAP/compatibility/granularity | `https://learn.microsoft.com/en-us/fabric/security/workspace-outbound-access-protection-overview` |
| F15 | Fabric Conditional Access/CAE limitation | `https://learn.microsoft.com/en-us/fabric/security/security-conditional-access` |
| F16 | Entra Conditional Access/licensing | `https://learn.microsoft.com/en-us/entra/identity/conditional-access/overview` |
| F17 | Warehouse/SQL endpoint auditing | `https://learn.microsoft.com/en-us/fabric/data-warehouse/sql-audit-logs` |
| F18 | OneLake diagnostics/immutability | `https://learn.microsoft.com/en-us/fabric/onelake/onelake-diagnostics-overview` |
| F19 | Current and legacy workspace monitoring | `https://learn.microsoft.com/en-us/fabric/fundamentals/workspace-monitoring-overview` |
| F20 | Fabric audit/user-activity tracking | `https://learn.microsoft.com/en-us/fabric/admin/track-user-activities` |
| F21 | Purview Audit setup/roles/default retention | `https://learn.microsoft.com/en-us/purview/audit-get-started` |
| F22 | Purview audit retention policies/licenses | `https://learn.microsoft.com/en-us/purview/audit-log-retention-policies` |
| F23 | Purview DLP for Fabric/Power BI | `https://learn.microsoft.com/en-us/purview/dlp-powerbi-get-started` |
| F24 | Workspace CMK supported scope/limitations | `https://learn.microsoft.com/en-us/fabric/security/workspace-customer-managed-keys` |
| F25 | Power BI BYOK | `https://learn.microsoft.com/en-us/fabric/enterprise/powerbi/service-encryption-byok` |
| F26 | Workspace/item retention and recovery | `https://learn.microsoft.com/en-us/fabric/admin/retention-recovery` |
| F27 | Experience-specific DR and reconstruction | `https://learn.microsoft.com/en-us/fabric/security/experience-specific-guidance` |
| F28 | OneLake redundancy/DR/soft delete | `https://learn.microsoft.com/en-us/fabric/onelake/onelake-disaster-recovery` |
| F29 | Fabric lineage | `https://learn.microsoft.com/en-us/fabric/governance/lineage` |
| F30 | Domains and access-boundary limitations | `https://learn.microsoft.com/en-us/fabric/governance/domains` |
| F31 | Healthcare data solutions delivery lifecycle | `https://learn.microsoft.com/en-us/industry/healthcare/healthcare-data-solutions/overview` |
| F32 | Example admin API: List Items permissions and preview warning | `https://learn.microsoft.com/en-us/rest/api/fabric/admin/items/list-items` |
| F33 | Governance/compliance/OneLake Govern | `https://learn.microsoft.com/en-us/fabric/governance/governance-compliance-overview` |
| F34 | Microsoft-hosted Azure TRE accelerator/support statement | `https://github.com/microsoft/AzureTRE` |
| F35 | OAP data connection rules/API instructions/preview UI label | `https://learn.microsoft.com/en-us/fabric/security/workspace-outbound-access-protection-allow-list-connector` |
| F36 | Workspace identity, assumption/lifecycle/governance | `https://learn.microsoft.com/en-us/fabric/security/workspace-identity` |
| F37 | Information protection: supported protection and export paths | `https://learn.microsoft.com/en-us/fabric/governance/information-protection` |

The source register is a verification aid, not a substitute for retaining the hospital's actual contractual terms, vendor confirmations, deployed settings, approvals, and operating evidence.
