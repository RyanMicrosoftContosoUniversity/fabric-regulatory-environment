# Understanding Healthcare Data Platforms

## From HIPAA obligations to Microsoft Fabric, Azure Databricks, and Snowflake architecture

**Document type:** Educational research synthesis  
**Version:** 1.1

**As of:** September 30, 2026  
**Audience:** A reader with no prior background in this repository, healthcare privacy, or cloud data platforms  
**Research basis:** All eleven source files present in the reviewed repository snapshot, supplemented by targeted checks of primary regulatory and product documentation  
**Assessment boundary:** No hospital environment, protected dataset, executed contract, architecture artifact, or operating control was inspected. Examples and design checks are generic, not customer-specific findings. This article is not a legal opinion, compliance certification, benchmark, or production authorization.

## Abstract

A healthcare organization needs more than a place to store and analyze data. It must know why information may be used, who may access it, how restrictions follow it through different systems, what evidence is available afterward, and how essential services recover when something fails. Cloud platforms offer important capabilities, but their features do not replace these organizational responsibilities.

This repository examines that problem from three perspectives: a collection of 57 Microsoft Fabric concerns; a comparison of Microsoft Fabric, Azure Databricks, and Snowflake on Azure against 60 selection requirements; and two detailed hospital HIPAA/HITECH reports using the same 138 admission, security, privacy, disclosure, breach, and administrative identifiers. One report proposes Databricks mechanisms; the other proposes Fabric mechanisms and reviews the original controls and concerns. These are 138 shared catalog positions, not 276 independent legal requirements. The central finding is consistent across them: select and approve an evidenced architecture, not a product name.

This article connects the documents into one reading path. It explains the vocabulary, the legal baseline, the technical boundaries, the platform-specific tradeoffs, and the approval process. Complete requirement catalogs and a stable numbered-entry map preserve the detail behind the narrative. Documented product limitations, hypothetical stricter policies, proposed designs, and generic validation questions are kept separate throughout.

## Reading path

| Part | What you will learn |
|---|---|
| 1-3 | The problem, basic vocabulary, and the distinction between law, policy, capability, and evidence |
| 4-6 | Healthcare obligations, patient rights, research releases, and incident handling |
| 7 | A common architecture and a worked two-study example |
| 8-10 | How Fabric, Azure Databricks, and Snowflake differ |
| 11-13 | The ten general gaps, platform selection, and the operating model |
| Appendix A | A consolidated glossary |
| Appendix B | Every one of the 60 comparative requirements and its repository ratings |
| Appendix C | Every one of the 138 shared hospital-framework positions, with platform-specific admission mechanisms |
| Appendix D | Coverage of all eleven files, all 57 original Fabric entries, and their detailed review statuses |
| References | Repository provenance and selected primary sources |

## 1. The problem this repository is trying to solve

Imagine a hospital and research organization collecting information from electronic health records, laboratory systems, imaging systems, billing systems, and clinical studies. Internal staff need operational reports. Data engineers need to prepare datasets. Investigators need approved study cohorts. External collaborators may need carefully restricted analyses. Some teams also want machine learning or AI-assisted search.

These needs pull in different directions. Researchers benefit from flexible tools and rich data. Privacy requires justified uses and restricted disclosures. Security requires controlled identities, paths, and privileges. Clinical operations require dependable availability and trustworthy results. Procurement needs sustainable costs and support. A platform can be attractive in one dimension and still fail an essential requirement in another.

The repository therefore asks a broader question than "Which vendor supports HIPAA?" Its practical question is:

> Can this specific combination of contracts, features, identities, networks, datasets, applications, people, and operating procedures satisfy the organization's applicable requirements, and can the organization demonstrate that it does?

The three vendors are evaluated as components of that combination, not as substitutes for it. The assessments are desk research and proposed designs. Their recommendations are hypotheses to investigate, not permission to ingest production protected health information. [^repo-matrix][^repo-databricks][^repo-snowflake][^repo-fabric]

### 1.1 How the repository's documents fit together

The root HIPAA controls file is an introductory Fabric-oriented checklist. It names familiar areas such as encryption, identity, auditing, workforce security, incidents, recovery, and physical safeguards. Some references are placeholders, and the recommendations are not deployed evidence. It is a starting vocabulary, not a complete compliance program. [^repo-starter]

The requirements text records 57 generic analysis notes covering product support, architecture assumptions, required permissions, and reusable design checks. The refined requirements document turns those into ten general assurance gaps and corrects or qualifies overbroad product assertions. [^repo-original][^repo-refined]

The market-research documents expand the problem into a common 60-requirement selection framework and three vendor assessments. The detailed Databricks and Fabric reports go further into the hospital's legal and operational duties, intentionally sharing identifiers for cross-platform comparison. Their legal duties overlap; their admission settings, identities, networking, evidence, and recovery mechanisms differ. The Fabric report also supplies a fourteen-control update and a numbered review of all 57 original statements without overwriting those references. The two README files explain the reports' scope and principal findings. [^repo-matrix][^repo-hospital][^repo-fabric-hospital][^repo-databricks-readme][^repo-fabric-readme]

### 1.2 Method and limits

All eleven files were read in full, including the two Fabric documents added during the final review. Repeated material was consolidated, requirement identifiers were preserved, and differences in scope were reconciled. Selected high-impact legal dates, product prerequisites, contract scope, networking limits, audit caveats, and AI authorization/geography behavior were checked against primary sources on September 30, 2026. The regulatory text inspected displayed title 45 currency through September 28, 2026.

The result synthesizes the entire local corpus; it is not a fresh independent legal audit of every source linked by that corpus. No measured performance, actual vendor pricing quote, hospital approval, or tenant configuration is implied. Architecture and approval questions describe evidence to obtain, not private engagement findings. Appendix D shows the exact source coverage.

## 2. A ground-up explanation of healthcare information

### 2.1 PHI, ePHI, and the parties responsible for them

**Protected health information (PHI)** is individually identifiable health information within the HIPAA framework. It can include clinical information, payment information, and identifying details. **Electronic PHI (ePHI)** is PHI maintained or transmitted electronically. Privacy obligations also apply to covered PHI in paper and oral forms; the Security Rule focuses on ePHI.

A **covered entity** includes health plans, healthcare clearinghouses, and qualifying healthcare providers conducting covered electronic transactions. A hospital in the repository's assumed scenario is a covered entity. A **business associate** performs covered functions or services involving PHI on behalf of a covered entity, subject to the relevant definitions and arrangements. Cloud processing can create business-associate responsibilities, including where the provider cannot ordinarily read encrypted information.

A **business associate agreement (BAA)** allocates required contractual duties. It is necessary where the relationship requires one, but it is not patient authorization, technical configuration, or proof that the hospital's own controls operate. The actual contracting party, service inventory, subprocessor chain, and incorporated terms matter. Azure hosting alone does not give a Snowflake customer the applicable Snowflake BAA. [^law-scope][^law-cloud][^snowflake-editions][^repo-hospital]

A **data protection addendum (DPA)** supplies contractual data-processing and security commitments. Microsoft can incorporate its BAA through qualifying agreements rather than requiring a separately signed standalone document in every case. Current Product Terms explicitly list Microsoft Fabric and Azure Databricks among Azure Core Services and Power BI among Power Platform Core Services. That corrects an inference based only on a product name missing from a generic overview; it does not admit every connected feature. Core Online Services exclude Previews, and governing DPA/BAA provisions and express exceptions still need feature-specific review. Neither "all previews are covered" nor "no preview can ever have relevant protections" is a safe universal conclusion. [^microsoft-terms][^law-cloud][^repo-fabric-hospital][^repo-refined]

### 2.2 The protected information extends beyond a patient table

The repository repeatedly warns against defining the data boundary too narrowly. Depending on their content, the following may require protection: raw files, curated tables, free-text notes, image headers or pixels, notebook results, query literals, logs, exported spreadsheets, BI caches, model artifacts, embeddings, checkpoints, backups, and recipient copies.

There are also **metadata** risks. A resource name, tag, URL, schema name, or error message can reveal sensitive information even when ordinary table queries are restricted. Databricks explicitly warns that customer-defined resource and metadata fields can be processed outside the relevant compliance boundary. The correct response is to inventory information locations and avoid putting patient details into names, source control, or diagnostic literals. [^databricks-hipaa][^repo-databricks][^repo-fabric][^repo-snowflake]

### 2.3 Legal authority is different from technical access

A person may have a working login and a valid table permission without having a lawful purpose for using a particular patient's information. Conversely, a patient may have a legal access right without having a platform account.

The organization must connect technical entitlements to approved purposes, investigator rosters, study status, authorizations, restrictions, and release workflows. A catalog, dashboard, or sharing feature cannot make those legal decisions by itself. This is why the repository treats consent, research authority, patient rights, and receiving-system controls as separate requirements rather than assuming they are solved by role-based access. [^repo-hospital][^repo-matrix]

## 3. The technical concepts needed to understand the assessments

### 3.1 Storage, compute, governance, and presentation are different layers

A **data lake** stores files and tables, often supporting several processing tools. A **warehouse** presents managed, structured query capabilities. A **lakehouse** combines lake storage with table management and analytical processing. A **compute engine** runs queries or transformations; examples in the repository include SQL and Spark. A **notebook** is an interactive document combining code, explanation, and results.

**Governance** describes how the organization classifies information, assigns ownership, defines policies, and tracks its use. Microsoft Fabric uses OneLake and related security/governance features; Azure Databricks uses Unity Catalog; Snowflake uses its account, object, role, policy, and governance mechanisms. These systems have different authority boundaries, not interchangeable implementations.

A **semantic model** adds business meaning, relationships, calculations, and reporting security over data. A **report** displays the results. Restricting a report does not prove that the underlying files, SQL endpoint, notebook, or external engine applies the same restriction. This distinction is central to the Fabric concerns and also applies to exported data and downstream applications on the other platforms. [^fabric-overview][^repo-fabric][^repo-databricks][^repo-snowflake]

### 3.2 Authentication, authorization, and execution identity

**Authentication** establishes who or what is making a request. **Authorization** determines what that identity may do. Microsoft Entra ID supplies identity services in the evaluated Azure-centered designs; Azure Active Directory or Azure AD in older notes refers to the earlier name.

**Multi-factor authentication (MFA)** strengthens sign-in. **Conditional Access** applies rules based on signals such as user, targeted cloud resource, device, or risk. These controls do not themselves determine which study rows a query returns.

An automated job or application often uses a **service principal** or **managed identity** rather than a human account. A request may run under the initiating user's identity, a fixed application identity, or an owner's delegated identity. These can be legitimate designs, but authorization and audit attribution change with the choice. A complete explanation must identify both the requesting person and the effective execution/storage principal where they differ. [^repo-hospital][^fabric-identity][^repo-matrix]

### 3.3 What row, column, and object restrictions mean

**Row-level security (RLS)** limits records, such as restricting an investigator to an approved cohort. **Column-level security (CLS)** limits fields. **Object-level security (OLS)** can hide model objects, including tables or columns. **Masking** substitutes or obscures a value presented to a reader. **Role-based access control (RBAC)** assigns privileges through roles; **attribute-based access control (ABAC)** evaluates attributes or tags in policy.

None of these labels guarantees enforcement through every interface. A supported SQL query may apply a policy while an independently authorized raw-file read, export, search index, or administrator policy change follows another authority path. The repository's insistence on testing every allowed path is therefore not redundant: it tests whether the intended boundary actually holds. [^repo-matrix][^repo-databricks][^repo-fabric][^databricks-abac]

### 3.4 Private connectivity is a route, not a complete security program

A **private endpoint** provides a private network path to a service. **Private Link** is the relevant Azure private-connectivity mechanism used in the assessments. A **fully qualified domain name (FQDN)** is the complete hostname; correct DNS resolution is part of making the private route work.

**Ingress** means traffic entering a boundary. **Egress** means traffic leaving it. Private ingress does not automatically stop a notebook, integration, model, or authorized user from sending information outward. It also does not replace authentication, authorization, or encrypted transport.

Scope matters: a tenant-wide control, a workspace-level control, an account control, a storage firewall, and a compute-network policy can govern different requests. "Private Link is enabled" is incomplete unless the design specifies which paths and which workloads it covers. A publicly reachable authenticated endpoint is also not equivalent to publicly disclosed PHI, although an institution may prohibit that endpoint under its own policy. [^repo-matrix][^fabric-network][^repo-databricks][^repo-snowflake]

### 3.5 Encryption and customer-managed keys

**Encryption at rest** protects stored information; **encryption in transit** protects information moving between systems. A **customer-managed key (CMK)** gives the customer specified key-management authority. Power BI's **bring your own key (BYOK)** is a distinct mechanism with its own scope.

Key control does not decide whether a permitted query is lawful. Nor does one key setting necessarily cover notebook results, model data, logs, external storage, exported files, or backups. The design needs an artifact-by-artifact map. Revoking a key may block necessary access, so rotation, outage, and recovery procedures are availability controls as well as confidentiality controls. [^fabric-keys][^databricks-keys][^repo-snowflake]

### 3.6 Four different questions often mistaken for "audit"

| Evidence product | Question it answers | Why the others do not replace it |
|---|---|---|
| Activity/audit trail | What action occurred, by which principal, on which object, at what time, with what outcome? | An event may not identify every patient or the legal purpose. |
| Historical entitlement record | Who could have accessed the data on a past date? | It needs grants, group membership, policies, ownership, and changes, not just completed reads. |
| Lineage and research provenance | Where did this result come from, and can the analysis be reproduced? | It needs input, transformation, software, policy, and consent versions; it is not a disclosure ledger. |
| Patient disclosure accounting | Which disclosures must be reported to this individual under the applicable rules? | It needs patient-specific recipient, information, purpose, and exception handling, not merely query text. |

These distinctions connect the Fabric history concerns, the comparative audit requirements, and the hospital report's patient-rights implementation. A longer retention setting alone does not supply missing event fields or recreate absent historical permissions. [^repo-refined][^repo-matrix][^repo-hospital][^law-accounting]

## 4. The legal baseline: what HIPAA/HITECH does and does not require

### 4.1 The Security Rule is an outcomes-based framework

The Security Rule requires protection of ePHI confidentiality, integrity, and availability, protection against reasonably anticipated threats and impermissible uses/disclosures, and workforce compliance. The approach considers organizational capabilities, infrastructure, costs, and risk. It is not a statute mandating a named cloud vendor or a particular private-network product. [^law-security-general]

Both hospital reports divide safeguards into administrative, physical, technical, and organizational/documentation duties. Administrative examples include risk analysis, access management, training, sanctions, incidents, backups, and evaluation. Physical examples include facilities, endpoints, and media. Technical examples include access, identity, audit, integrity, authentication, and transmission protection. Appendix C preserves every shared identifier without pretending that the implementation mechanisms are interchangeable. [^repo-hospital][^repo-fabric-hospital][^law-security-controls]

### 4.2 Required, addressable, standard, and conditional are not synonyms

| Label used in the hospital framework | Meaning |
|---|---|
| R - Required | The named implementation specification must be implemented. |
| A - Addressable | Assess whether it is reasonable and appropriate; implement it when it is, or document the decision and an appropriate equivalent alternative when required. It is not a synonym for optional. |
| Std - Standard | An applicable mandatory standard, without necessarily having a separately labeled R/A specification. |
| Conditional | Applies when the organization performs the relevant function or activity; otherwise record a justified applicability decision. |

Encryption and automatic logoff are addressable specifications under the current rule; that does not make casual omission acceptable. The repository recommends strong controls for its hospital design while distinguishing those recommendations and vendor prerequisites from universal statutory instructions. A hospital may adopt stricter policies, including private-only paths, customer keys for designated artifacts, or unusually strong audit-completeness requirements. Those policies must be evaluated honestly but should not be mislabeled as requirements imposed on every HIPAA deployment. [^law-security-general][^law-security-controls][^repo-matrix]

### 4.3 Shared responsibility includes work no platform can perform for the hospital

The cloud provider can supply certain infrastructure safeguards and contractual assurances. Hospital leaders must appoint responsible officials. Privacy and legal teams determine permitted uses and releases. HR manages workforce training and sanctions. Facilities and endpoint teams protect local access. Data owners approve purpose and quality. Platform teams configure the services. Security operations examines evidence and responds to incidents.

The root controls checklist is helpful precisely because it names several of these areas, but some of its shortcuts need context. Redundancy and encryption do not authenticate the clinical source or prove a transformation is correct. A catalog's lineage view is not an independently recoverable backup. A cloud datacenter assurance does not secure a researcher's unattended laptop. [^repo-starter][^repo-hospital]

### 4.4 HITECH extends responsibilities; it is not a platform setting

HITECH strengthened business-associate duties, breach notification, several privacy protections, and enforcement. The detailed report maps these effects to contracts, patient rights, self-paid restrictions, sale/marketing controls, notification procedures, and operating evidence.

Two subtleties are retained. First, HITECH's EHR disclosure-accounting provision must be considered alongside the current regulation, which still contains a treatment/payment/operations exclusion; counsel must resolve applicability and implementation rather than treating either source as irrelevant. Second, evidence of recognized security practices over the preceding 12 months can be relevant to enforcement consideration, but it is not immunity or a replacement for actual compliance. There is no general HHS-approved cloud HIPAA certification that authorizes the hospital's complete deployment. [^repo-hospital][^law-accounting][^law-hitech][^law-cloud]

### 4.5 Related regimes are applicability decisions, not automatic product requirements

**42 CFR Part 2** adds protections for applicable substance-use-disorder records. A SUD diagnosis alone does not establish that every record is a Part 2 record. The organization needs an actual applicability determination and appropriate use, disclosure, consent, proceedings, and notice controls. The 2024 rule's compliance date was February 16, 2026. [^law-part2]

**Research rules and agreements** may require patient authorization, an appropriately documented waiver, IRB/privacy-board processes, grant or sponsor restrictions, and recipient agreements. IRB approval is not automatically a HIPAA authorization. Preparatory-to-research permission is not permission for the researcher to remove PHI. [^law-research][^repo-hospital]

**FDA Part 11 and regulated applications** require scope analysis for relevant electronic records and signatures. They do not apply automatically to every hospital notebook. Similarly, a research model does not become an approved clinical or medical-device application merely because its infrastructure is secure. [^law-fda][^repo-matrix]

**HIPAA administrative transactions** concern applicable exchanges such as claims, eligibility, remittance, enrollment, and related identifiers and code sets. Analytics may support them, but a data platform is not automatically a conforming EDI endpoint. The 2026 claims-attachment/signature rule has a May 26, 2028 compliance date for its finalized scope; it is distinct from a blanket signature requirement for analytics or from the unfinalized prior-authorization attachment proposal in that rule. [^law-transactions][^law-attachments]

State law, international rules, genomics agreements, human-subject requirements, and other specific obligations can add restrictions. Neither a NIST mapping nor a commercial Azure deployment automatically establishes FedRAMP authorization, a US-only legal mandate, or permission for international processing. [^repo-matrix][^repo-hospital]

### 4.6 Date-sensitive legal and product changes

| Date or status | Meaning as of September 30, 2026 |
|---|---|
| January 6, 2025 | Publication of the HIPAA Security Rule cybersecurity proposal. A proposed rule is not the current legal baseline. |
| July 2027 forecast | The reviewed OIRA agenda forecasts final action; that is not an enacted rule or compliance deadline. |
| June 18, 2025 | Court order vacating most of the 2024 reproductive-health rule; HHS identifies the affected NPP provisions and remaining requirements. |
| February 16, 2026 | Part 2 and applicable remaining NPP update deadlines have already passed. |
| October 1, 2026 | Fabric managed healthcare solution deployment is restricted to existing customers; this does not retire Fabric itself. |
| February 1, 2027 | Databricks' announced enforcement date for the VNet-encryption enablement prerequisite, including existing profile-enabled workspaces. This is a product requirement, not a HIPAA amendment. |
| December 31, 2027 | End of support for the current Fabric managed healthcare solution. |
| May 26, 2028 | Compliance date for the finalized claims-attachment/electronic-signature requirements. |

The hospital report also preserves the January 23, 2020 Ciox access decision: the compulsory third-party directive is limited as described by HHS to electronic EHR copies, and the individual-access fee limitation does not apply to a request to transmit records to a third party. The individual's own access rights remain protected. Codified text, court-order guidance, and current applicable law must be read together. [^law-proposal][^law-reproductive][^law-part2][^fabric-healthcare][^databricks-profile][^law-attachments][^law-ciox]

## 5. Privacy, patient rights, and research releases

### 5.1 Purpose, minimum necessary, and special categories

Treatment, payment, and healthcare operations are defined permitted-use categories, not catch-all labels for unrelated research, commercial training, or marketing. Minimum-necessary controls require an appropriate scope of information and recipients, while retaining the rule's exceptions, including applicable treatment-provider disclosures/requests and individual access.

The hospital framework separately addresses authorizations and revocations, psychotherapy notes, marketing, sale/remuneration, fundraising opt-outs, representatives and minors, directory preferences, caregivers, confidential communications, and organizational boundaries. A requested restriction for a qualifying fully self-paid service must be handled at the relevant service level in applicable health-plan payment/operations disclosures; it is not a blanket deletion of the hospital record.

These duties need patient and purpose context. A group called "Researchers" is insufficient if different studies, data categories, or releases have different conditions. The organization also needs complaint, sanction, mitigation, nonretaliation, notice, and training processes. Appendix C.3 and C.4 preserve the detailed categories rather than collapsing them into an undifferentiated "consent" feature. [^repo-hospital][^law-privacy]

### 5.2 Patient rights require workflows, not just queries

Patients can have rights relating to designated record sets, amendments, accounting, restrictions, and communications. Analytics used to make decisions about individuals may matter to designated-record-set scope. HIM and Privacy must determine the applicable records and exceptions.

An access workflow verifies authority, assembles the relevant information, produces an appropriate format, applies lawful fee and denial/review rules, and meets the deadline. An amendment workflow records the decision, appends or links the correction and required disagreement material, and propagates appropriate changes to recipients and downstream datasets. HIPAA does not create a general right to erase every record.

Disclosure accounting requires the applicable patient-specific facts and exclusions. An internal query log is not automatically a reportable disclosure, and a query containing no patient identifier cannot by itself produce a correct accounting. The hospital needs an approved disclosure ledger and corresponding release process. [^law-access][^law-amendment][^law-accounting][^repo-hospital]

### 5.3 De-identification is not the same as encryption or masking

There are three distinct release categories:

| Category | What it means | Essential caution |
|---|---|---|
| PHI | Identifiable covered information released or processed under an applicable legal basis | Permissions, purpose, agreements, safeguards, and patient rights still matter. |
| Limited data set | PHI with specified direct identifiers removed, used for permitted purposes under a data use agreement | It remains PHI; it is not synonymous with de-identified data. |
| Validated de-identified data | Data satisfying Safe Harbor or Expert Determination as applicable | The actual release and context must satisfy the method, not merely the UI presentation. |

**Safe Harbor** requires the specified identifier removals and the no-actual-knowledge condition. The hospital report's complete 18-category inventory is retained here:

| # | Identifier category to address |
|---|---|
| 1 | Names |
| 2 | Geographic subdivisions below state, subject to the specific three-digit ZIP population rule |
| 3 | Individual-related date elements other than year, and ages over 89 with the required age/date treatment |
| 4 | Telephone numbers |
| 5 | Fax numbers |
| 6 | Email addresses |
| 7 | Social Security numbers |
| 8 | Medical-record numbers |
| 9 | Health-plan beneficiary numbers |
| 10 | Account numbers |
| 11 | Certificate/license numbers |
| 12 | Vehicle identifiers and serial/license-plate numbers |
| 13 | Device identifiers and serial numbers |
| 14 | URLs |
| 15 | IP addresses |
| 16 | Biometric identifiers |
| 17 | Full-face photographs and comparable images |
| 18 | Other unique identifying characteristics, numbers, or codes, subject to the specific re-identification-code provision |

The qualifying ZIP exception uses more than 20,000 people for the combined three-digit area; otherwise the specified initial digits become 000. Ages over 89 and date elements revealing those ages need the permitted 90-or-older treatment. The analysis must address relevant relatives, employers, and household members as well as the individual. [^law-deidentification][^repo-hospital]

**Expert Determination** uses a qualified expert's documented analysis of very small re-identification risk for the anticipated recipient and context. It can be appropriate where useful dates, geography, images, or linked research information cannot satisfy Safe Harbor. Text, images, rare combinations, and genomic linkage deserve explicit review.

Hashing a medical-record number, masking a display, or holding a re-identification key separately does not automatically satisfy either route. The specific regulatory re-identification-code mechanism has its own conditions. Azure Health Data Services offers a separate de-identification service, but selecting a service does not replace validation and release approval. [^law-deidentification][^fabric-deidentification][^repo-hospital]

## 6. Incidents, breach notification, and retention

### 6.1 An incident is not automatically a reportable breach

A security incident needs investigation and response. An impermissible PHI acquisition, access, use, or disclosure is presumed a breach unless an applicable exception or documented low-probability assessment supports another conclusion. The assessment addresses the information/identifiers, unauthorized recipient, actual acquisition or viewing, and mitigation.

Encryption needs contextual analysis. A stolen encrypted file and an attacker using a valid account to retrieve plaintext are different cases. The existence of at-rest encryption does not settle whether notification duties apply.

The discovery clock is not postponed until investigators finish. Security, Privacy, Legal, HIM, communications, and suppliers need an agreed chronology, evidence preservation, containment, and notice process. Shorter contractual supplier incident-notice deadlines can be important even though the statutory breach-notification ceiling is longer. [^law-breach][^repo-hospital]

### 6.2 The deadlines and thresholds must not be mixed up

| Obligation | Baseline described in the hospital framework |
|---|---|
| Individual access | Action within 30 days; one additional 30-day extension with the required timely written reason/date |
| Amendment | Action within 60 days; one additional 30-day extension with required notice |
| Accounting request | Action within 60 days; one additional 30-day extension; first accounting in 12 months free |
| Accounting lookback | Six years, subject to applicable exclusions and other rule conditions |
| Required Security/Privacy documentation | Six years from creation or last date in effect, whichever is later |
| Individual breach notice | Without unreasonable delay, no later than 60 calendar days after discovery, subject to lawful delay |
| BA-to-covered-entity breach notice | Without unreasonable delay, no later than 60 calendar days; applicable contracts may require faster reporting |
| Media notice | More than 500 residents of a state/jurisdiction; required timing and content apply |
| HHS large-breach reporting | 500 or more individuals; contemporaneous with individual notice |
| HHS smaller-breach reporting | Within 60 days after the end of the year of discovery; individual notices are not postponed to year-end |
| Substitute notice for 10 or more unreachable individuals | Applicable 90-day web/media notice and toll-free-contact provisions |
| Decedent PHI protection | 50 years after death; this is not a universal duty to retain records for 50 years |

Notice content, approved delivery methods, lawful law-enforcement delays, exceptions, and evidence of the decision also matter. The more-than-500 media threshold is not the same as the 500-or-more HHS threshold. Appendix C.5 preserves all ten breach rows. [^repo-hospital][^law-access][^law-amendment][^law-accounting][^law-documentation][^law-privacy-administration][^law-breach]

### 6.3 There is no single universal retention number for everything

Required HIPAA documentation, patient disclosure ledgers, raw telemetry, clinical records, research data, backups, model artifacts, and legal holds are different record classes. The six-year documentation requirement is not a blanket six-year retention rule for every raw access event or medical record.

A platform default is also not the hospital's records policy. The original Fabric notes mistook a 30-day monitoring default for an unchangeable limit. Databricks system-table retention and Snowflake history windows have separate scope and recovery caveats. Increasing a setting does not restore already deleted information, prove complete capture, or create independent ransomware recovery.

The organization should define record-specific schedules, access controls, legal holds, approved disposal, and evidence retrieval. The hospital report proposes retaining selected control/investigation evidence conservatively where appropriate; that is a design decision to justify, not an invented universal legal raw-log mandate. [^law-documentation][^repo-hospital][^repo-refined]

## 7. A common architecture and a worked example

### 7.1 The architecture shared by the analyses

The following is a synthesis of proposed designs, not a deployed topology or a claim that HIPAA mandates it:

```text
EHR / laboratory / imaging / revenue / approved research sources
  -> authenticated and encrypted ingestion
  -> restricted landing and quarantine
  -> governed preparation and quality checks
  -> purpose-scoped PHI datasets
       -> approved clinical/operations applications
       -> approved study analysis
       -> validated limited-data-set or de-identified release
  -> authorized BI, application, or recipient

Controls surrounding every stage:
  legal purpose + contract/service scope + data ownership
  individual/workload identities + least privilege + managed endpoints
  tested ingress/egress + artifact-specific encryption and keys
  activity evidence + historical policy records + disclosure accounting
  versioned code/configuration + recovery copies + clinical fallback
  hospital training, patient-rights, incident, and review procedures
```

Data must remain controlled after it leaves the central platform. A report subscription, recipient spreadsheet, search index, model endpoint, or external application creates another information and authority boundary. [^repo-hospital][^repo-matrix]

### 7.2 Two studies and three environments

Consider synthetic Study A and Study B, each with development, test, and production environments. An investigator is authorized for Study A's approved cohort. An ingestion identity can write controlled source data. A policy administrator can change grants but should not be an ordinary investigator. A SOC collector receives approved evidence. These are illustrative roles, not existing accounts.

The investigator signs in successfully. That proves identity, not permission to Study B. The test must attempt the intended Study A query and prohibited Study B access through every permitted interface: SQL, notebook, API, storage, BI, sharing, and any search application.

Suppose the dashboard applies Study A RLS but the same user has an independent raw-storage grant. The report appears secure, yet the intended boundary is not established. Alternatively, a delegated engine may legitimately read as its owner: then the design needs correct source privileges, reporting restrictions, and a correlation record connecting the human request to the effective principal.

Next, the investigator's study entitlement expires. The institution tests new requests, active tokens and sessions, caches, shared outputs, and any permitted retained results. Turning off a group membership is not proof that every copy or session immediately stops working.

Finally, a synthetic source correction is applied, an approved research output is released, a collector fails, and a key becomes temporarily unavailable. The exercise checks amendment propagation, reproducibility, output approval, collection-health alerts, and recovery. This demonstrates why the repository proposes a comparative proof-of-concept rather than a slide-based selection. [^repo-matrix][^repo-hospital]

### 7.3 Accountability must be explicit

| Function | Main responsibility in the proposed operating model |
|---|---|
| Hospital leadership | Designate responsible officials, fund the program, and approve accountable decisions |
| Privacy/HIM | Patient rights, notices, permitted releases, disclosure accounting, and privacy investigations |
| Legal/procurement | Applicability, BAAs/DUAs, supplier duties, contracts, and legally permissible exceptions |
| Security/SOC | Risk analysis, monitoring, evidence protection, incidents, and control evaluation |
| Platform/network/key teams | Supported configurations, identities, connectivity, encryption, deployment, and recovery |
| Data/research/clinical owners | Approved purposes/cohorts, consent rules, data quality, provenance, and intended-use validation |
| HR/facilities/endpoint teams | Workforce clearance, training/sanctions, termination, and physical/local-device safeguards |
| FinOps/operations | Sustainable capacity, licenses, staffing, support, and safe budget controls |

Actual named owners and approvals are required at implementation time. An assumed central-team permission is not an operating safeguard. [^repo-hospital][^repo-matrix][^repo-refined]

## 8. Microsoft Fabric: integration with important workload boundaries

### 8.1 What Fabric contributes

Fabric brings data engineering, warehousing, real-time analytics, and Power BI into an integrated SaaS experience around OneLake. The repository treats it as a plausible first candidate for Microsoft-centered analytics and BI, subject to an approved workload-specific architecture.

The key lesson is not that Fabric lacks security. It is that network, key, identity, governance, and evidence behavior is not uniform across all item types and access paths. A protected lakehouse cannot stand in for a protection analysis of its SQL endpoint, semantic model, shortcut, export, or external consumer. [^fabric-overview][^repo-fabric]

Fabric is SaaS. The hospital report did not establish a Fabric counterpart to Databricks' HIPAA-profile/Premium/compliance-add-on admission sequence. Instead, it requires the actual Fabric tenant, capacity region, item inventory, contracts, licenses, and supported control combinations. An F-SKU capacity prerequisite belongs to the particular feature that requires it; it is not a new universal HIPAA specification. [^repo-fabric-hospital][^fabric-availability]

### 8.2 Private networking and serving architecture

The workspace-private support matrix documents exclusions and limitations for semantic models, item sharing, deployment pipelines, SQL database scenarios, and selected AI/integration paths. Tenant-private designs are different. A private warehouse or SQL analytics endpoint connection string exists, contradicting the original blanket "no private SQL FQDN" assertion; the particular Databricks-to-Fabric integration remains untested.

Direct Lake, DirectQuery, and Import are different reporting modes. Direct Lake reads appropriate lake-backed data without submitting every operation as a SQL query; DirectQuery queries the source; Import holds a copied model dataset. An inbound-restricted source can require a supported gateway/Import/DirectQuery architecture rather than the assumed Direct Lake path. A warehouse/analytics endpoint is also not the same item type as a Fabric SQL database. [^fabric-network][^fabric-reporting][^repo-fabric][^repo-refined]

The strict comparative baseline requires every PHI workspace, including BI serving, to use workspace-level inbound restrictions. Its **N08 gap** follows from that architecture. A separately assessed tenant-private design, approved serving boundary, or suitable de-identified dataset can change the design, but cannot be credited without new evidence.

A client connected privately to two workspaces does not establish a server-to-server route between them. Supported managed private endpoints or gateways must fit the actual connection. Workspace restrictions also do not govern every administration or network-policy API identically; the tenant boundary and each endpoint's roles, application/delegated identity, and OAuth scopes require separate review. Git support is distinct from deployment-pipeline support, and `fabric-cicd` is one possible approved delivery mechanism, not the only possible one. [^repo-fabric-hospital][^fabric-network][^fabric-admin-evidence]

### 8.3 OneLake policy and identity are separate decisions

OneLake security and data-access roles have a May 2026 GA announcement. That does not make every engine or policy feature GA: the detailed report identifies Eventhouse RLS and authorized third-party integration as preview paths. Exact engine eligibility remains an admission decision. [^fabric-releases][^fabric-onelake][^repo-fabric-hospital]

Elevated workspace roles have broad access within their workspace. OneLake roles grant access rather than providing a general Deny-role mechanism; broad default reader grants and unioned memberships need review. ReadWrite roles cannot be treated as restricted-reader roles carrying row/column constraints. External-engine enforcement depends on the supported path; some unsupported filtering cases are blocked rather than transparently filtered.

For SQL analytics endpoints, user identity and delegated identity are different authorization models. Direct Lake variants and fixed identities also matter. Storage-level owner attribution does not prove that all end-user evidence is lost: engine or semantic-model logs may preserve human and correlation information. Conversely, a semantic-model RLS/OLS rule does not prove that other source paths are restricted. [^fabric-onelake][^fabric-identity][^repo-refined][^repo-fabric]

| Mode or permission | Consequence for the proposed hospital design |
|---|---|
| SQL delegated identity, the new-endpoint default | The owner accesses storage; the user's SQL permissions govern the query. The user's OneLake table policy is not automatically inherited. |
| SQL user identity | OneLake table policy governs reads; SQL permissions still matter for nondata objects. Documented RLS also applies to Admin/Member/Contributor users in this mode, without removing their broader privileged powers. |
| Changing SQL identity mode | Queries across the workspace can be interrupted, SQL roles can be removed, and inline functions can be lost. Preserve definitions and validate the change; this is not a harmless toggle. |
| Policy propagation and cached owner tokens | User-mode synchronization can take five minutes; delegated owner-token changes have separate caching behavior. Measure actual revocation rather than equating a saved grant change with immediate loss of access. |
| Direct Lake SSO versus fixed identity | SSO checks the effective user; a fixed identity can isolate consumers from the source. Refresh and query identities may differ, and SQL-backed versus OneLake-backed modes have different fallback behavior. |
| Semantic-model Write permission | Ordinary model RLS/OLS is not enforced for model writers. Source policy and privileged-change controls remain separate. |
| Warehouse data reached through a OneLake shortcut | Warehouse SQL policies are not automatically translated into restrictions on raw shortcut reads. Restrict and test that path separately. |

These are scoped behaviors, not claims that administrators universally bypass every rule or that one policy universally protects every engine. The relevant mode, supported identities, source permissions, owner, and change history belong in the evidence record. [^fabric-identity][^fabric-reporting][^repo-fabric-hospital]

Fabric **workspace identity** is a GA automatically managed service principal, not an Azure managed identity with identical lifecycle/governance. Authorized users can use it through connections, so control who can assume its access. Recovery must also account for identity recreation and rebinding; restoring a dataset alone does not restore every credential relationship. [^fabric-workspace-identity][^repo-fabric-hospital]

### 8.4 Keys are artifact-specific, not a workspace label

Workspace CMK has GA announcements in the October and November 2025 archive; the source market assessment cites November, not an independently established first-release day. It supports a defined item list and restricts unsupported items, with specified metadata/cache/library exclusions. Power BI BYOK covers supported imported semantic-model data under its separate capacity arrangements; it is not blanket customer-key protection for every BI artifact or mode. The comparative **N05 gap** concerns the institution's strict all-designated-artifact key policy, not absence of all customer-key options.

A reporting-workspace designation of "CMK" is therefore insufficient evidence on its own. An assessment must identify artifact types, data locations, selected key mechanisms, applicable coexistence/support status, and an approved recovery process. [^fabric-keys][^fabric-releases][^repo-fabric][^repo-refined]

### 8.5 Audit and monitoring require multiple evidence planes

SQL auditing is off by default, and its documented best-effort behavior permits configured events to be omitted under high activity or network load. This supports the comparative **A03 gap** against a strict lossless requirement. It does not, by itself, establish a HIPAA violation.

Predicate filtering removes excluded events before persistence and only operates on enabled actions/groups. SQL-only evidence misses reads that do not submit SQL. Logs may contain sensitive statement text, and audit-folder access can be broad; readable or immutable operational evidence is not necessarily independently protected against deletion of its enclosing assets. The proposed design combines relevant SQL, OneLake, engine/model, identity, and tenant records with an independently retained archive. [^fabric-audit][^fabric-diagnostics][^repo-fabric][^repo-refined]

Warehouse audit-folder roles can include Viewer with ReadAll; deleting the warehouse can delete its audit files. A narrower SQL permission, `VIEW DATABASE SECURITY AUDIT`, is a different access mechanism. SQL analytics endpoint file browsing differs from warehouse browsing; its documented audit-reading function is `sys.fn_get_audit_file_v2`. OneLake diagnostic-file immutability also does not make the enclosing workspace/lakehouse undeletable. The proposed archive must survive the destructive administrator scenario it is intended to address. [^fabric-audit][^fabric-diagnostics][^repo-fabric-hospital]

Operational collection is not retroactive: the current Monitoring Item begins with collection off and does not backfill earlier activity. OneLake diagnostics can take up to an hour to start. The new report therefore requires evidence collection before admitting PHI and separate tests for enablement, propagation, overload, correlation, and failed collectors. [^fabric-monitoring][^fabric-diagnostics][^repo-fabric-hospital]

There is a real documentation conflict: the updated monitoring overview supports Private Links and adjustable retention with a 30-day default, while the workspace-private matrix still lists monitoring as unsupported. The article does not choose a categorical verdict. Several original statements about filtering, sharing roles, and roughly 15-minute recreation belong specifically to legacy monitoring.

Current monitoring Eventhouse/ingestion/dashboard behavior under throttling differs from Power BI reports and Activator alerts, which can be throttled. Central alerting must not depend blindly on the analytics workload remaining healthy. Monitoring/Log Analytics coexistence and the selected legacy/current experience also need configuration-specific review. [^fabric-monitoring][^fabric-network][^repo-refined]

Purview Audit adds another distinct plane. Audit Reader/Manager role groups support specified search/export duties; Exchange permissions still matter for certain cmdlets and auditing enablement. Platform roles and OAuth scopes are not interchangeable universal alternatives. Audit Standard's documented default is 180 days; Premium's automatic one-year rule names selected workloads and is not a blanket one-year promise for Fabric records. Actual record types, licenses, policies, collection access, and approved archives must be demonstrated. [^fabric-admin-evidence][^repo-fabric-hospital]

### 8.6 Governance, operations, and lifecycle

Domains organize content; they are not item-access security boundaries. Tenant administration does not mean every policy target is tenant-wide. Sensitivity labels, DLP, audit access, Conditional Access, and guest/session policies require supported scopes, licenses, and central-team cooperation. Fabric does not support the CAE session control in the reviewed guidance; user/group targeting is not the only Conditional Access signal.

Classification is not authorization or de-identification. Supported Purview protection policies can add tenant access restrictions, and publishing policies can protect specified PBIX/export paths. That protection does not universally follow CSV/text exports or cross-tenant access. DLP has item, storage-mode, trigger, service-principal, capacity, and licensing limitations; its restrict-access action is preview. Neither labeling nor DLP universally prevents local copies or screenshots. A posture/template scan is an input to risk assessment, not a complete inspection or certification of the hospital. [^fabric-protection][^repo-fabric-hospital]

There is a second documentation conflict: the Capacity Metrics app page supports tenant Private Link while excluding workspace isolation on its installation workspace, but the tenant Private Link overview says the app is unsupported. The earlier refined review follows the app page; the hospital Fabric review follows the overview. Preserve both statements and obtain configuration-specific confirmation rather than selecting the convenient answer. The Govern experience has a documented Private Link limitation; managed-VNet Spark starter-pool/startup behavior is an operational consideration rather than a guaranteed startup SLA; and outbound protection/gateway/connection-rule mechanisms and previews must be checked per workload. [^fabric-governance][^fabric-conditional][^fabric-metrics][^fabric-network][^fabric-managed-vnet][^fabric-outbound][^repo-refined][^repo-fabric-hospital]

The availability matrix and detailed pages also disagree about selected default-model, Dataflow, and Power BI outbound-protection scenarios. A checkmark, preview label, or omission must be read with the exact item variant and feature. If a required control depends on the unresolved scenario, it remains unresolved until supported behavior and eligibility are established. [^fabric-availability][^repo-fabric-hospital]

The managed healthcare solution is moving to a customer-managed delivery model. The source/documentation package became available August 3, 2026; the deployment restriction begins October 1, 2026; managed-solution support ends December 31, 2027. This transition is not Fabric's retirement. Official healthcare material and a patient-data security scenario exist; neither certifies the proposed hospital deployment. Azure TRE's accelerator status is also not an assurance that a complete research enclave has been delivered. [^fabric-healthcare][^fabric-reference][^repo-fabric]

### 8.7 Recovery windows and independent restoration

The hospital Fabric report distinguishes the following current product mechanisms:

| Mechanism | Documented window or constraint | What the hospital still needs |
|---|---|---|
| Collaborative workspace retention | Seven-day default, configurable 7-90 days | Recover within the actual policy; validate items, security, identities, and consumers afterward |
| My workspace retention | Fixed 30 days | A personal-workspace recovery window is not an enterprise PHI operating model |
| Supported item recovery | Three-day default, configurable 3-90 days or disabled; explicitly configured prior settings are preserved | Check actual tenant setting and supported item type; do not assume every object is recoverable |
| OneLake file soft deletion | Seven days | Independent recovery for failures beyond accidental file deletion |
| Capacity/OneLake disaster recovery | Supported paired-region scope and asynchronous replication | Account for uncopied data, tenant dependencies, unsupported assets, keys, and full processing recovery |

Git preserves supported definitions, not a complete PHI backup. Copies of live Delta files must be transaction-consistent. Restoring an item does not universally restore shared grants, workspace identities, warehouse snapshots, connections, schedules, or downstream bindings. Protect consistent data and reconstruction assets independently and prove clinical fallback and full-workflow recovery. [^fabric-recovery][^fabric-workspace-identity][^repo-fabric-hospital]

## 9. Azure Databricks: flexible research engineering with several security planes

### 9.1 Why it is a candidate

The repository's preliminary hypothesis favors Azure Databricks for complex research engineering, distributed processing, and custom ML. Unity Catalog, supported filters/masks, notebook and job workflows, and ML capabilities provide useful building blocks. The detailed hospital report demonstrates how to connect those building blocks to broader hospital responsibilities.

The operating burden is material: identity, catalog privileges, compute modes, Azure storage, classic/serverless networking, deployment, clients, and downstream systems must work together. The platform is not a turnkey consent application, managed researcher enclave, patient-rights system, or guaranteed complete evidence repository. [^repo-databricks][^repo-hospital]

### 9.2 Admission prerequisites come before PHI

The reviewed Azure guidance requires Premium plus the Enhanced Security and Compliance add-on, the compliance security profile, and the appropriate HIPAA standard selection. PHI workspaces include test or recovery environments whenever they actually contain PHI.

The HIPAA guide also requires the managed-services CMK option or the specified customer-account notebook-result storage option. The hospital design chooses managed-services CMK while separately protecting other storage and disks. Customer-defined sensitive names remain a concern. Profile and standard selections are intended to be permanent after regulated processing, and preview/regional eligibility must be confirmed rather than assumed from general availability. [^databricks-hipaa][^databricks-profile][^repo-hospital]

VNet encryption and supported VM types are current product prerequisites; enforcement of the encryption enablement requirement is announced for February 1, 2027. That enforcement date must not be confused with a new HIPAA legal deadline. [^databricks-profile]

### 9.3 Catalog governance must not be bypassed by storage authority

Unity Catalog can govern supported reads, filters, masks, workspace bindings, and other policy mechanisms. A researcher with separately authorized raw ADLS access has another authority path. The proposed design therefore uses scoped storage identities and governed interfaces rather than ordinary researchers holding account keys, broad SAS credentials, or unrestricted storage roles.

Storage activity may identify the connector/workload identity rather than the human reader. Catalog/job/request records must provide the other side of attribution. Powerful ownership, MANAGE, policy, and administrative privileges also need separation and review. Approved compute/runtime and operation support are part of the policy boundary. [^repo-databricks][^repo-hospital][^databricks-governance]

Source-table ABAC does not automatically apply to AI Search indexes. A separately built index can expose information under a different policy boundary. Govern the indexed corpus, service permission, retrieval authorization, refresh, and output process rather than assuming a table policy follows the data into AI. [^databricks-abac]

### 9.4 Classic and serverless networking are not one network

Classic compute can use the customer's Azure VNet and relevant private connectivity. Serverless compute runs in a provider-managed plane, with its own network connectivity configuration and egress controls. A classic firewall is not proof of a serverless outbound restriction.

The detailed report highlights egress-policy caveats: FQDN rules can permit domains sharing an IP, some destinations are implicitly allowed, and changes can have propagation or restart requirements. Dry-run rules log rather than block. The institution must test the effective enforced policy and required dependencies instead of accepting an allowlist screenshot as proof. [^databricks-network][^repo-hospital]

### 9.5 Evidence scope, latency, and retention

The audit system table `system.access.audit` is Public Preview in the reviewed reference. Deleting a workspace removes its events older than 14 days from that table; this is not a claim that an independently exported archive is also deleted.

Baseline retention is table-specific, commonly 365 days for relevant tables. The configurable-retention Beta uses a 395-day default and permits 30-3,650 days for supported tables, with listed schema exclusions and compliance-standard conditions. It neither backfills deleted history nor guarantees full retained-data recovery after total loss. System tables do not provide real-time monitoring.

Azure diagnostic settings omit account-level events and do not cover every event/service in the reference. Query-history scope is not every Spark/file/patient access. Collector permissions and some sensitive fields have their own access rules. These facts explain why the hospital report requires a multi-source strategy, collection-health monitoring, protected disclosure ledgers, and independent evidence recovery. [^databricks-audit][^databricks-system-tables][^databricks-diagnostics][^repo-hospital]

### 9.6 Code, secrets, deletion, and recovery

Versioned deployment bundles support delivery, but approval, clinical/research validation, policy regression tests, and rollback remain customer processes. Updating a compute policy is not proof that existing compute has adopted it. Packages, init scripts, clients, and connectors still need maintenance ownership.

Secret redaction does not make privileged scope access harmless. The reviewed Key Vault-backed secret-scope integration uses the vault access-policy model; that is integration-specific, not a reason to weaken a shared hospital vault indiscriminately. Prefer supported managed identities where practical.

Logical `DELETE` can leave retained data files, deletion vectors, caches, clones, indexes, exports, and backups. Authorized disposal needs appropriate purge/vacuum operations and separate treatment of other copies, while respecting holds and recovery needs.

Managed DR requires gated enrollment, Premium workspaces, Mission Critical add-ons, and enabled serverless in both primary and secondary regions, with the required metastore, storage, identity, and privileges. Mission Critical bundles the compliance features but does not automatically configure them or establish PHI eligibility for the gated preview. Replicating selected managed assets does not replicate external-table/volume data automatically; omitted pipelines, models, serving/search assets, secrets, and other exclusions need separate recovery design. Delta history, redundancy, shallow clones, and live replication are not equivalent to independent recoverable backups; full recovery includes policies, keys, identities, code, integrations, and usable processing. [^repo-databricks][^repo-hospital][^databricks-operations][^databricks-secrets][^databricks-recovery]

## 10. Snowflake on Azure: governed query access with separate collaboration and AI boundaries

### 10.1 The evaluated baseline

The repository evaluates commercial Snowflake on Azure in an approved US account region, using Business Critical, a signed Snowflake BAA, and individually approved table, networking, sharing, and AI/ML configurations. This is the evaluated baseline, not evidence that an actual hospital account exists.

Its preliminary strength is governed SQL analytics and controlled collaboration, with native role/policy mechanisms, supported history, managed recovery features, and ML functionality. It should not be dismissed as SQL-only: the reviewed ML documentation includes feature engineering, CPU/GPU training, a model registry, and monitoring. PHI eligibility and clinical suitability still need service-specific review. [^snowflake-editions][^snowflake-ml][^repo-snowflake]

### 10.2 Native query policies are not every-path policies

Row-access and column-masking policies can restrict supported query results. Policy administrators must be separated from ordinary researchers; writer permissions must be controlled independently because read filtering is not a complete write restriction.

Stages, external files, integrations, containers, exported results, BI clients, and receiving accounts introduce separate boundaries. A secure share or a clean-room template does not establish research authority, eliminate inference risk, or recall already copied results. Workload identity federation can help replace long-lived credentials, but actual driver/integration support and residual credential paths need validation. [^repo-snowflake][^snowflake-policies][^snowflake-collaboration]

### 10.3 Cortex Search and inference are two distinct risks

**Search authorization:** Cortex Search runs with owner's rights. A caller's permission to query the service is not proof that every source row is restricted by that caller's original table entitlement. Approve the indexed corpus and service audience; implement any required filtering in trusted authorization rather than trusting a caller-supplied filter.

**Processing geography:** Stored tables in an approved region do not prove that inference stays there. Current documentation states that new accounts in new commercial organizations after March 9, 2026 default to `ANY_REGION`. Other eligible accounts can inherit same-cloud geography defaults, and explicit account settings override inherited defaults. `DISABLED` confines processing to the home region but can limit available models/features. The correct approval evidence is the effective setting and eligible processing scope, not an assumed default.

These are architecture hazards, not evidence of a hospital disclosure. General Business Critical availability does not approve every Cortex, search, clean-room, container, or ML feature for PHI. [^snowflake-search][^snowflake-inference][^repo-snowflake]

### 10.4 History, keys, recovery, and cost

Access History has a documented 365-day window for supported activity, with scope and latency limits. It is not a universal lossless trail or an independently retained WORM archive. The institution needs other evidence sources, entitlement history, and approved collection privileges.

Private ingress, private outbound connectivity, stages, recipient accounts, and Tri-Secret Secure key control have separate scopes. Customer key loss can become an availability event; keys and external locations need their own recovery map.

Time Travel commonly starts with one day; eligible Enterprise-or-higher permanent objects can have up to 90 days, while other object types differ. Applicable Fail-safe adds a separate seven-day vendor-mediated, best-effort recovery period. It is neither the normal customer archive nor a guaranteed rapid-restore mechanism. Full failover includes roles, clients, integrations, keys, and external resources.

Resource monitors cover warehouses, not every serverless or AI charge. Cost and budget controls therefore need multiple charge classes and safe suspension rules for important hospital workloads. [^repo-snowflake][^snowflake-history][^snowflake-recovery][^snowflake-network-keys][^snowflake-budgets]

## 11. The ten general gaps connect all the documents

The refined requirements are **assurance gaps in the supplied narrative**, not findings from a tested tenant. They translate many specific complaints into control outcomes. The following explanation preserves their identifiers while making the desired assurance plain.

| Gap | General assurance needed | What a beginner should understand |
|---|---|---|
| G01 | Secure connectivity with compatible workloads | Demonstrate every necessary ingress/egress route, client, gateway, and workload under the selected network boundary. A private endpoint alone is not the integration. |
| G02 | Least privilege across layers | Prove that storage, query, reporting, writer, administrator, and external-engine permissions form one intended boundary. |
| G03 | Identity and session enforcement | Know the requesting and effective identities, and prove appropriate revocation across tokens, sessions, and applications. |
| G04 | Adequate protected audit evidence | Enable required sources, understand omissions/filtering, protect sensitive logs, and examine usable evidence. |
| G05 | Historical access, provenance, and retention | Reconstruct permissions and released results over time; apply record-specific retention rather than a universal raw-log number. |
| G06 | Accountable governance and real approvals | Identify owners and obtain achievable tenant/policy/collector permissions before counting controls as implemented. |
| G07 | Reliable monitoring and availability | Validate capacity, throttling, collection, alerting, legacy/current monitoring, and startup behavior together. |
| G08 | Controlled releases and study-scale topology | Demonstrate compatible deployment, environment isolation, estate inventory, and maintainable automation. |
| G09 | Encryption coverage and safe publication | Map keys to actual artifacts and validate PHI minimization/de-identification for actual releases. |
| G10 | Supported services and a defensible compliance case | Establish exact eligibility, lifecycle, dated architecture/control evidence, and approved responsibilities. |

Appendix D maps each of the 57 original entries to these themes exactly once. Corrected claims remain accounted for rather than silently disappearing. [^repo-refined]

### 11.1 How to read disagreements in the source material

There are several different kinds of disagreement. A blanket claim can be contradicted, such as absence of a private SQL hostname. A default can be misread as a maximum, such as monitoring retention. A statement can describe legacy behavior. Two current official pages can conflict, as monitoring/Private Link documentation does. A stricter architecture can have a real product mismatch without making every architecture illegal.

Generic design checks are a different category from verified product behavior. Assess serving-boundary restrictions, source security roles, artifact-level key coverage, applicable NIST controls, complete workspace footprints, document versioning, and required tenant approvals. These checks do not establish that any particular environment exposes PHI, lacks a control, or violates a NIST requirement.

The market documents contain illustrative NIST anchors and a control-mapping resource; the earlier ten-gap review deliberately did not perform a separate NIST assessment. A control identifier organizes evidence. It does not, by itself, demonstrate implementation or certify the entire baseline. [^repo-original][^repo-refined][^repo-matrix][^nist-hipaa]

The newer hospital Fabric report adds stronger contract-name evidence and a full original-claim review. It does not supersede the comparison by silently declaring the three G rows closed. It also interprets some documentation differently, especially Capacity Metrics. This article preserves those differences; Appendix D.3 records every original entry's review outcome. [^repo-fabric-hospital][^repo-fabric][^repo-refined]

### 11.2 What the control identifiers in the repository mean

**NIST** is the US National Institute of Standards and Technology. SP 800-53 supplies a control catalog; SP 800-66 Rev. 2 supplies HIPAA Security Rule implementation and mapping guidance. Prefixes identify control families, while numbers identify controls within those families. For example, AC concerns access control and AU concerns audit/accountability. These identifiers are not vendor features.

The following reproduces the comparison's illustrative anchors and explains their purpose:

| Requirement family | HIPAA anchors in the source | Illustrative NIST anchors and meaning |
|---|---|---|
| Legal and risk | 164.308(a)(1), 164.308(b), 164.314 | RA-3: risk assessment; SA-9: external-service responsibilities |
| Identity and study boundaries | 164.308(a)(3)-(4), 164.312(a), 164.312(d) | AC-2/3/5/6: accounts, enforcement, separated duties, least privilege; IA-2: identification/authentication |
| Networks, keys, secrets | 164.312(a), 164.312(e) | SC-7/12/13/28: boundaries, keys, cryptography, stored information; IA-5: authenticators |
| Governance and research | Applicable Privacy Rule and research duties | AC-3/4: access enforcement/information flow, plus actual purpose-specific rules |
| Audit and incidents | 164.308(a)(6), 164.312(b) | AU-2/3/5/6/9/12: event selection, content, failures, review, protection, generation; IR-4: incident handling |
| Resilience and integrity | 164.308(a)(7), 164.312(c) | CP-9/10: backup/reconstitution; SI-7: integrity; CM-3: controlled changes |
| Operations and suppliers | Hospital supplier and records policies | SA-9: external services; CM-2/3: baselines/change control; SI-2: flaw remediation |

For an actual claim, identify the control revision, applicable scope, implementation, test, evidence, owner, and lawful alternatives. HIPAA does not automatically impose every SP 800-53 control on every hospital. The source's crosswalk is an organizing aid, not a completed NIST assessment. [^repo-matrix][^repo-fabric-hospital][^nist-hipaa][^nist-controls]

## 12. How to select a platform without manufacturing a winner

### 12.1 Interpret the ratings correctly

| Rating | Meaning in the repository | What it does not mean |
|---|---|---|
| M | Documented supported capability meets the platform-capability requirement | Not proof of configuration, operation, or production approval |
| C | Material conditions must be closed | Not an acceptable unresolved production gate |
| G | Documented mismatch against the evaluated requirement/baseline | Not necessarily a universal vendor incapability or a legal violation |
| U | Evidence is insufficient | Not proof that the vendor lacks the capability |

The **P0** requirements are production gates. **P0*** requirements are gates when their stated use case or policy applies. **P1** requirements are important selection criteria; **P2** requirements concern optimization.

The source matrix has the following counts, reproduced for traceability rather than ranking:

| Platform | M | C | G | U |
|---|---|---|---|---|
| Azure Databricks | 2 | 54 | 0 | 4 |
| Snowflake | 3 | 53 | 0 | 4 |
| Microsoft Fabric | 2 | 52 | 3 | 3 |

Snowflake's extra M is not a numerical win. Fabric's three G rows are scoped to strict key/network/lossless-audit requirements. Databricks and Snowflake have U, not M, for the reviewed lossless all-path audit guarantee. All three have major unverified contract/service/assurance matters. Appendix B preserves all 60 rows. [^repo-matrix]

### 12.2 Approval gates precede weighted preferences

The sequence is applicability, closure, then scoring. An applicable U needs evidence; an applicable C needs its conditions demonstrated; an applicable G needs redesign or a legally permissible policy resolution. A contract or legal obligation cannot be waived merely because a technical committee accepts risk.

A closure record should contain the requirement, evaluated architecture/version, evidence, owner, unresolved condition, residual risk, approver, and decision. The particularly important source gates include exact feature/region eligibility and supplier terms for every candidate, the lossless requirement if adopted, and the strict Fabric key/network assumptions.

Only after gate closure does the source propose 0-4 suitability scoring with separate confidence/evidence fields and nonapplicable requirements removed from the denominator. There are no measured product scores in this repository. [^repo-matrix]

### 12.3 The source's three weighting profiles

These are proposed preferences, not benchmark results:

| Category | Hospital BI/warehouse | Research/ML | Strict research enclave |
|---|---|---|---|
| Legal and research | 20% | 18% | 15% |
| Identity and authorization | 18% | 18% | 20% |
| Network and encryption | 15% | 15% | 20% |
| Governance and privacy | 15% | 22% | 20% |
| Audit and incidents | 15% | 12% | 15% |
| Resilience and integrity | 12% | 10% | 7% |
| Commercial and operations | 5% | 5% | 3% |

The profiles make the tradeoff visible: custom research emphasizes governance/ML; a strict enclave emphasizes boundaries and output control; operational BI values legal, identity, reliability, and evidence together. No profile can average away an unresolved gate. [^repo-matrix]

### 12.4 Comparative proof-of-concept and acceptance targets

Use synthetic or appropriately de-identified data initially. The required comparisons exercise two studies/three environments, private ingress and egress, all-path evidence reconciliation, consent/expiry and collaborator termination, de-identification/output review, AI retrieval/geography, and failure/recovery/key/budget stress.

The repository proposes the following measurable targets. They are **example procurement criteria requiring hospital approval**, not vendor promises or HIPAA-prescribed numbers:

| Area | Proposed target and qualification |
|---|---|
| Prohibited access/export/network cases | 100% of the defined prohibited cases denied, with usable denial evidence |
| New access after approved entitlement change | Prevented within 15 minutes; active sessions, caches, and authorized retained outputs measured separately |
| Audit reconciliation and latency | 100% of required synthetic operations reconciled; 99% of security events to SOC within 15 minutes; a lossless policy additionally needs a documented guarantee |
| Operational analytics recovery | Full-workflow RTO of four hours and data RPO of one hour; not a life-safety-system target |
| Reproducibility and release | Every released regulated analysis has input/code/policy/consent versions and approval; defined policy regression tests pass |
| Integrity | 100% reconciliation of agreed source/control totals and identifier mappings; meaningful discrepancies block release |

Passing a finite test suite demonstrates observed behavior under those conditions. It does not prove a perpetual lossless guarantee, establish contract scope, or automatically validate a clinical application. [^repo-matrix]

## 13. Operating the approved design is part of the design

### 13.1 Cost means the complete control program

The source assessments do not contain current price quotes or measured TCO. They identify cost drivers: editions and compliance add-ons, compute/capacity, history and backups, monitoring, independent evidence storage, private networking/gateways, replication/DR, AI/serverless, governance licenses, endpoints, staff, assurance, migration, and contract exit.

Budget controls must cover all relevant charge classes without silently stopping essential hospital services. Capacity or warehouse throttling can also disrupt analytics-dependent alerts. Independent detection and funded recovery are therefore operational requirements, not optional decorations. [^repo-matrix][^repo-databricks][^repo-snowflake][^repo-fabric]

### 13.2 Workspace counts require an actual topology

Architecture options must be compared using a complete, consistently scoped inventory rather than isolated workspace headline counts. Distinguish shared resources, study/environment units, per-unit multipliers, and separately counted recovery overhead. No actual study portfolio or customer estate counts are represented here.

For a uniform illustrative topology, a useful accounting expression is:

```text
Total workspaces =
  shared workspaces
  + studies x environments x workspaces per study/environment
  + separately counted recovery workspaces
```

Real topologies may share components or have exceptions. The inventory must explicitly identify those choices, avoid double counting recovery resources already included in another category, and record inherited policies, capacity assignments, privileged ownership, lifecycle automation, and retirement responsibilities. Use the same counting assumptions for each compared option. [^repo-original][^repo-refined][^repo-matrix]

### 13.3 Recovery is more than copies or read availability

Replication can preserve availability while copying corruption or deletion. A shallow clone can depend on the source. Read-only failover may not restore transformations or release workflows. A saved dataset without keys, permissions, configuration, clients, or external dependencies may be unusable.

The organization needs independent recovery assets, tested key recovery, protected credentials, and a runbook demonstrating restored end-to-end processing. Clinical fallback and emergency access must be tested as actual workflows, not assumed from the existence of a tenant administrator account. [^repo-hospital][^repo-matrix][^repo-databricks][^repo-snowflake][^repo-fabric]

### 13.4 Implement in dependency order

The Databricks hospital report proposes five stages: establish scope/governance and risk; admit an eligible platform boundary; implement least privilege and purpose/restriction enforcement; implement evidence and patient rights; then operationalize and prove recovery, workforce controls, incident response, and acceptance gates. The Fabric report follows the same dependencies while making tenant approvals and engineering/reporting item compatibility explicit early decisions, and treating independent recovery/clinical fallback as their own proof obligations.

Staff/guest Conditional Access, any required session policy, label/protection assignments, and audit permissions are dependencies to verify early in that sequence. A technical design cannot count a proposed control as implemented before its approval and behavior are evidenced. Alternative approved central collection or policy-delivery processes may be possible, but must also be demonstrated.

Production readiness includes contracts, product prerequisites, purpose controls, least privilege, identity lifecycle, networking, encryption/recovery keys, audit, rights, de-identification, resilience, incidents, hospital operations, and external/AI recipients. None should be inferred from a successful demo alone. [^repo-hospital][^repo-fabric-hospital][^repo-refined]

## 14. Conclusion

The repository does not establish that one vendor is universally compliant and the others are not. It establishes a disciplined way to investigate a hospital and research platform. Fabric offers integrated Microsoft analytics with important workload-specific boundaries; Azure Databricks offers flexible governed engineering with substantial cross-plane responsibilities; Snowflake offers strong managed query/collaboration capabilities with separate external and AI boundaries.

The decisive question is whether the institution can approve and sustain the exact architecture, close every applicable gate, and retain evidence that both technical and organizational controls operate. The catalogs below preserve the detail needed to ask that question without losing the explanatory thread.

## Appendix A. Consolidated glossary

These definitions explain terms used across the source corpus; product-specific scopes remain as discussed in the relevant platform sections. [^repo-matrix][^repo-hospital]

| Term | Plain-language meaning |
|---|---|
| ABAC | Attribute-based access control: policies evaluate approved attributes/tags as well as object grants. |
| ACL | Access-control list: permissions attached to an object or resource. |
| Activator | Fabric's event-driven alert/action capability; availability depends on its supported paths and capacity behavior. |
| ADLS | Azure Data Lake Storage, a customer storage boundary in many Databricks designs. |
| BAA | Business associate agreement; required contractual assurances for an applicable PHI relationship. |
| BI | Business intelligence: models, reports, dashboards, and related consumption. |
| BYOK / CMK | Bring your own key / customer-managed key; related concepts with product-specific implementations and coverage. |
| CA / CAE | Conditional Access / continuous access evaluation; policy and session mechanisms, not table permissions. |
| Catalog / metastore | Governance structures organizing data objects and associated metadata/permissions. |
| CI/CD | Controlled automated integration and deployment of code/configuration, with required approvals and tests. |
| CLS / OLS / RLS | Column-, object-, and row-level restrictions; enforcement depends on the interface and identity. |
| Clean room | A constrained collaboration environment for approved analyses; not automatically a complete researcher enclave. |
| Control plane / compute plane | Management services / resources executing data work; their networks and responsibilities can differ. |
| Cortex | Snowflake's AI capabilities; search authorization and inference routing need separate review. |
| CSP | Databricks compliance security profile, not a general hospital certification. |
| Delta / ACID | Managed table/transaction mechanisms supporting consistency; not proof of clinical correctness or independent backup. |
| DICOM | Medical imaging format/standards; headers and image content can contain identifiers. |
| Direct Lake / DirectQuery / Import | Different Power BI data-access/storage modes, with different source, key, identity, and evidence consequences. |
| DLP | Data loss prevention controls applying to supported information and enforcement paths. |
| DNS / FQDN | Name-resolution service / complete hostname; critical to correct private routing. |
| DPA | Data protection addendum: incorporated contractual processing/security commitments, with service-specific terms and exceptions. |
| DR | Disaster recovery: restoring usable services after serious failure, including dependencies. |
| DUA | Data use agreement, including applicable limited-data-set release terms. |
| EDI | Electronic data interchange, including applicable standardized administrative transactions. |
| EHR / ePHI | Electronic health record / electronic protected health information. |
| Entra ID | Microsoft's identity service, formerly Azure Active Directory. |
| Eventhouse / KQL | Fabric real-time analytical storage/query experience / its query language. |
| FHIR / HL7 / OMOP | Healthcare exchange standards or analytical models whose actual semantics need validation. |
| F-SKU | Fabric capacity purchasing tier; required by particular features, not a universal legal HIPAA control. |
| GA / preview / Beta | Release/status labels; none independently proves PHI contractual eligibility. |
| Gateway | A supported bridge between consumers and data/network boundaries, not a universal bypass of restrictions. |
| HIM | Health information management, including record and patient-rights workflows. |
| HIPAA / HITECH | US healthcare privacy/security framework / legislation that strengthened relevant duties and protections. |
| HITRUST / SOC 2 | Assurance frameworks/reports for scoped controls; not blanket hospital HIPAA authorization. |
| IaC | Infrastructure as code: versioned definitions for repeatable configuration. |
| Ingress / egress | Traffic entering / leaving a boundary. |
| IRB | Institutional review board; its approval is not automatically a HIPAA authorization. |
| Managed identity / service principal | Nonhuman identity used by a workload or service under a defined permission model. |
| MDCA | Microsoft Defender for Cloud Apps, relevant to approved session/application controls. |
| MFA | Multi-factor authentication, strengthening sign-in without deciding data-purpose authorization. |
| Mission Critical | Databricks add-on bundling specified compliance features and gated managed DR; settings and eligibility still require approval. |
| ML / RAG | Machine learning / retrieval-augmented generation using retrieved material as AI context. |
| MPE | Managed private endpoint, used for supported service-to-service private paths. |
| NCC | Databricks network connectivity configuration for applicable serverless connectivity. |
| NIST | US standards body providing control and HIPAA implementation resources; mappings are not certifications. |
| NPI / EIN | Provider / employer identifiers relevant to applicable administrative exchanges. |
| NPP / NPRM | Notice of privacy practices / notice of proposed rulemaking; these serve very different purposes. |
| OAP | Fabric outbound access protection, with workload-specific mechanisms and support. |
| OAuth / PAT / SAS | Authorization/token mechanisms; personal access tokens and storage access signatures require controlled scope/lifecycle. |
| PHI | Protected health information under the applicable HIPAA definitions. |
| Private Link / VNet | Azure private service-connectivity mechanism / virtual network. |
| Purview | Microsoft governance, information-protection, DLP, and audit capabilities with separate scopes/permissions. |
| RACI | Responsibility allocation identifying who performs, owns, is consulted on, or is informed about work. |
| RBAC | Role-based access control. |
| RPO / RTO | Acceptable data-loss interval / target time to restore the required service. |
| SIEM / SOC | Security information and event management system / security operations center. |
| Spark | Distributed data-processing engine used for engineering and research workloads. |
| SSO | Single sign-on; in the Direct Lake discussion, query authorization uses the effective signed-in user rather than a fixed backend identity. |
| TCO / FinOps | Full lifecycle cost / financial operating discipline for cloud services. |
| TLS | Transport encryption protocol; private routing does not replace it. |
| TPO | Treatment, payment, and healthcare operations, subject to the relevant legal definitions. |
| TRE | Trusted research environment: the complete controlled researcher-to-output workflow, not just notebooks. |
| Unity Catalog / OneLake | Databricks governance catalog / Fabric's shared data foundation; their actual controls have different scopes. |
| VDI | Managed virtual desktop environment, potentially part of researcher endpoint controls. |
| WORM | Write once, read many protection; independent retention and deletion boundaries still need validation. |
| Workspace identity | Fabric-managed service principal with its own connection, assumption, lifecycle, and recovery rules. |
| XEL / XMLA | SQL audit/event-file format / analytical-model access protocol; both need their own permission and evidence scope. |

## Appendix B. Complete 60-requirement comparative catalog

The IDs, priorities, and ratings below reproduce the repository matrix. Requirement wording is condensed for readability; the explanatory parts above describe the needed evidence. **DB** = Azure Databricks; **SF** = Snowflake; **F** = Fabric. M/C/G/U and P0/P0*/P1/P2 have the meanings in part 12. These are documented-capability/evidence ratings, not tested controls. [^repo-matrix]

### B.1 Legal, contractual, and research

| ID | Priority | Requirement and closure focus | DB | SF | F |
|---|---|---|---|---|---|
| L01 | P0 | Effective BAA and relevant processor/subcontractor terms | C | C | C |
| L02 | P0 | Exact PHI service, feature, preview, region, and subprocessor eligibility | U | U | U |
| L03 | P0* | Required current scoped assurance or government authorization | U | U | U |
| L04 | P0 | Approved risk assessment and shared-responsibility allocation | C | C | C |
| L05 | P0* | Applicable sensitive-category and Part 2 restrictions | C | C | C |
| L06 | P0* | Research authority, investigator/purpose list, IRB and agreement restrictions | C | C | C |
| L07 | P0* | Applicability and validation of regulated records/signatures | C | C | C |
| L08 | P0* | Approved storage, metadata, support, backup, and inference geography | C | C | C |
| L09 | P0 | Incident/notification/evidence rights and supplier-accountability terms | U | U | U |
| L10 | P1 | Record-specific retention, holds, permitted disposal, and access rights | C | C | C |

### B.2 Identity and boundaries

| ID | Priority | Requirement and closure focus | DB | SF | F |
|---|---|---|---|---|---|
| I01 | P0 | Federated human identity and enforceable MFA across clients | M | M | M |
| I02 | P0 | Distinct workload identities and supported short-lived authentication | C | M | C |
| I03 | P0 | Least privilege and separation of policy, researcher, and evidence duties | C | C | C |
| I04 | P0 | Denied cross-study/environment/collaborator access | C | C | C |
| I05 | P0 | Row/column restrictions through every permitted read path | C | C | C |
| I06 | P1 | Separately controlled writers and transformation identities | C | C | C |
| I07 | P1 | Approved elevation, emergency use, and vendor-support oversight | C | C | C |
| I08 | P0* | Approved collaborator onboarding, study expiry, and measured revocation | C | C | C |

### B.3 Networks, encryption, and secrets

| ID | Priority | Requirement and closure focus | DB | SF | F |
|---|---|---|---|---|---|
| N01 | P0* | Private-only required inbound paths, with public-path denial evidence | C | C | C |
| N02 | P0* | Enforced controls on unapproved outbound destinations and exports | C | C | C |
| N03 | P1 | Working private source/partner connectors, DNS, identity, and failover | C | C | C |
| N04 | P0 | Supported at-rest/transit encryption across the actual boundaries | M | M | M |
| N05 | P0* | Customer key coverage for every policy-designated PHI artifact | C | C | G |
| N06 | P1 | Tested key rotation, revocation, outage, and recovery | C | C | C |
| N07 | P0 | Controlled secrets and PHI in names, code, prompts, traces, and literals | C | C | C |
| N08 | P0* | Required BI, compute, CI/CD, monitoring, and AI coexist with isolation | C | C | G |

### B.4 Governance, privacy, and research workflow

| ID | Priority | Requirement and closure focus | DB | SF | F |
|---|---|---|---|---|---|
| G01 | P1 | Sensitive inventory, classification coverage, and ownership | C | C | C |
| G02 | P1 | Lineage and historical input/code/policy/consent provenance | C | C | C |
| G03 | P0* | Enforced purpose, consent, study expiry, and permitted withdrawal handling | C | C | C |
| G04 | P0* | Validated Safe Harbor or Expert Determination release | C | C | C |
| G05 | P0* | Approved recipients, legal terms, sharing scope, and revocation | C | C | C |
| G06 | P0* | Controlled research endpoints/raw export and approved outputs | C | C | C |
| G07 | P0* | Eligible, authorized, geographically approved AI/ML/RAG/tool paths | C | C | C |
| G08 | P1 | Equivalent policy, provenance, and disposition on external engines | C | C | C |
| G09 | P1 | Protected BI exports, subscriptions, caches, and endpoint copies | C | C | C |
| G10 | P1 | Validated healthcare semantics, terminology, quality, and integration lifecycle | C | C | C |
| G11 | P0* | Research versus clinical intended use, safety, and model validation | C | C | C |

### B.5 Audit and incidents

| ID | Priority | Requirement and closure focus | DB | SF | F |
|---|---|---|---|---|---|
| A01 | P0 | Attributable evidence for every allowed PHI path | C | C | C |
| A02 | P1 | Human-to-workload-to-storage attribution and stable correlation | C | C | C |
| A03 | P0* | Required event completeness/latency; guarantees where lossless is demanded | U | U | G |
| A04 | P1 | Reconstruction of past grants, groups, ownership, and effective policy | C | C | C |
| A05 | P0 | Independently protected, retained, recoverable evidence | C | C | C |
| A06 | P1 | PHI minimization and restricted access in query/audit records | C | C | C |
| A07 | P1 | SOC and collector-health detection independent of analytics disruption | C | C | C |
| A08 | P0 | Rehearsed investigation, containment, preservation, and notification | C | C | C |
| A09 | P1 | Actually approved least-privileged collection/evidence-sharing access | C | C | C |

### B.6 Resilience, integrity, and delivery

| ID | Priority | Requirement and closure focus | DB | SF | F |
|---|---|---|---|---|---|
| R01 | P0 | Restore data, policies, code, identities, and configuration after destruction | C | C | C |
| R02 | P0 | Full-workflow cross-region RPO/RTO and recovered write processing | C | C | C |
| R03 | P1 | Isolation and safe contention/degradation under realistic load | C | C | C |
| R04 | P0* | Approved versioned releases, regression tests, reproducibility, and rollback | C | C | C |
| R05 | P1 | Maintenance of SaaS dependencies, runtimes, packages, gateways, and clients | C | C | C |
| R06 | P0* | Source/identifier/unit/terminology/cohort/output reconciliation | C | C | C |
| R07 | P1 | Usable exit/export, migrated policies, and evidenced permitted deletion | C | C | C |

### B.7 Commercial and operating requirements

| ID | Priority | Requirement and closure focus | DB | SF | F |
|---|---|---|---|---|---|
| O01 | P1 | Fully loaded lifecycle costs including control operation and labor | C | C | C |
| O02 | P1 | Charge-class budgets without unsafe service shutdown | C | C | C |
| O03 | P1 | Scalable approved provisioning and retirement of studies/environments | C | C | C |
| O04 | P1 | Released, eligible, supported dependencies and funded lifecycle transitions | C | C | C |
| O05 | P1 | Named sustainable hospital operating skills and support coverage | C | C | C |
| O06 | P1 | Actual licenses, integrations, central privileges, and approvals | C | C | C |
| O07 | P2 | Controlled representative workload, migration, recovery, and cost benchmarks | C | C | C |

## Appendix C. Complete hospital implementation catalog

This appendix translates the 138 shared catalog positions in `Databricks\HIPAA-HITECH-Azure-Databricks.md` and `Fabric\HIPAA-HITECH-Microsoft-Fabric.md` into a compact reading inventory. **HC-** is added here solely to distinguish the hospital IDs from the market matrix and the ten general G01-G10 gaps. Remove HC- to find the original ID in either report. For example, HC-A01 is a hospital transaction obligation, not comparative audit requirement A01.

There are 138 rows: 6 admission/design, 54 Security Rule, 42 Privacy Rule, 12 conditional disclosure-permission, 10 breach, 8 HITECH, and 6 transaction/cooperation rows. This covers both reports' identifier sets once, not every law governing every hospital and not 276 distinct obligations. Admission rows combine legal and architectural requirements; the two reports use product-specific wording for G04/G05. Their differences are explicit below. Other rows summarize the shared duty and evidence outcome; parts 8 and 9 explain each implementation plane. Proposed owners are functions in the source role models, not actual staff assignments. [^repo-hospital][^repo-fabric-hospital]

### C.1 Admission and design conditions

| ID | Shared assurance | Databricks mechanism/evidence | Fabric mechanism/evidence |
|---|---|---|---|
| HC-G01 | Define covered functions, PHI flows, derived information, and designated records. | Approved inventory including notebooks, storage, models, and recipients | Approved inventory including OneLake, models, gateways, caches, and recipients |
| HC-G02 | Determine preemption and specially protected data rules. | Legal restriction matrix enforced in approved data/release paths | The same applicability work, enforced through supported Fabric/release paths |
| HC-G03 | Establish effective BA assurances and contracting/subprocessor duties. | Governing Microsoft terms and actual procurement/service chain; Legal | Governing Microsoft terms, Fabric/Power BI/ancillary scope and PHI vendors; Legal |
| HC-G04 | Admit a supported product deployment boundary. | Premium, compliance profile/HIPAA standard and required add-on; eligible compute/network exports | Tenant/capacity region, item types, licenses, network/key/monitoring compatibility; no transplanted Databricks HIPAA switch |
| HC-G05 | Protect actual PHI locations, outputs, and identities. | Required notebook-result option plus full storage/disk/key map | Default encryption plus approved optional workspace CMK/BYOK, source/model/connection/recipient map |
| HC-G06 | Approve features, regions, previews, AI/support/DR, and metadata hygiene. | Versioned eligible-feature/destination allowlist and scope confirmation | Versioned feature/item/destination allowlist, contract scope, and resolved product conflicts |

Privacy/Legal/Data approve scope, purpose, and eligibility; Platform/Security prove deployed boundaries. A shared identifier does not allow substituting one vendor's admission evidence for the other's.

### C.2 Security Rule safeguards

R/A/Std have the meanings in part 4. Parent standards remain applicable alongside their named specifications. The regulatory families are 164.306, 164.308, 164.310, 164.312, 164.314, and 164.316. [^law-security-general][^law-security-controls][^law-documentation]

| ID | Class | Requirement and practical evidence |
|---|---|---|
| HC-S01 | Std | Protect ePHI confidentiality, integrity, availability, and workforce compliance; maintain an end-to-end threat/control baseline. |
| HC-S02 | R | Analyze risks across every information flow, identity, endpoint, vendor, and data location; retain the scoped assessment. |
| HC-S03 | R | Manage identified risks with accountable actions and authorized residual-risk decisions. |
| HC-S04 | R | Apply an appropriate workforce sanction policy with protected HR/security records. |
| HC-S05 | R | Review system activity and act on relevant access, privilege, export, and incident evidence. |
| HC-S06 | Std | Designate the security official and clear authority/delegation. |
| HC-S07 | A | Authorize and supervise workforce access based on approved responsibilities. |
| HC-S08 | A | Establish job-appropriate clearance and role approvals before provisioning. |
| HC-S09 | A | Revoke leaver/mover identities, grants, credentials, sessions, and job ownership appropriately. |
| HC-S10 | R, conditional | Isolate clearinghouse functions when present; otherwise document non-applicability. |
| HC-S11 | A | Define purpose/role-based PHI access and retain approved grants. |
| HC-S12 | A | Establish, review, and modify access; preserve recertification and historical changes. |
| HC-S13 | Std | Train the workforce and management on relevant security responsibilities. |
| HC-S14 | A | Issue useful security reminders and document relevant communications. |
| HC-S15 | A | Protect against malicious software across supported compute, packages, scripts, and endpoints. |
| HC-S16 | A | Monitor authentication activity, failures, unusual sources, and workload identities. |
| HC-S17 | A | Manage passwords and credentials with supported strong, scoped authentication. |
| HC-S18 | Std + R | Respond to and report suspected incidents, mitigate harm, and document outcomes. |
| HC-S19 | R | Maintain retrievable exact ePHI copies and demonstrate consistent backup restoration. |
| HC-S20 | R | Recover required services and dependencies under an approved disaster-recovery plan. |
| HC-S21 | R | Sustain safe emergency-mode operations, priorities, access, and reconciliation. |
| HC-S22 | A | Test and revise contingency procedures after representative failures. |
| HC-S23 | A | Classify application/data criticality and restoration priorities. |
| HC-S24 | Std | Perform periodic technical/nontechnical evaluations and change-triggered reassessment. |
| HC-S25 | R | Obtain applicable written BA assurances and downstream responsibility evidence. |
| HC-S26 | A | Enable controlled emergency facility access for restoration. |
| HC-S27 | Std + A | Secure relevant hospital facilities and review provider physical-control assurances. |
| HC-S28 | A | Validate visitors and physical access according to role and authority. |
| HC-S29 | A | Retain records of security-relevant physical maintenance and changes. |
| HC-S30 | Std | Define appropriate workstation use, surroundings, remote work, and local PHI handling. |
| HC-S31 | Std | Restrict physical endpoint access, including unattended screens and managed remote access. |
| HC-S32 | Std + R | Dispose of PHI/media appropriately across hardware, exports, histories, and backups. |
| HC-S33 | R | Sanitize media before reuse and retain the relevant handling evidence. |
| HC-S34 | A | Track media/export custodians and movement. |
| HC-S35 | A | Create appropriate retrievable backups before moving equipment containing PHI. |
| HC-S36 | Std | Enforce authorized access across catalog, compute, workspace, storage, and network paths. |
| HC-S37 | R | Use unique human/workload identities and retain initiating/run-as attribution. |
| HC-S38 | R | Test attributable, scoped emergency PHI access and review its use. |
| HC-S39 | A | Test actual idle-session termination or a justified alternative; compute shutdown is not user logoff. |
| HC-S40 | A | Protect every relevant PHI location with an approved encryption/decryption design and recoverable keys. |
| HC-S41 | Std | Record and examine risk-relevant activity through a demonstrated evidence-coverage strategy. |
| HC-S42 | Std | Prevent improper alteration/destruction using controlled writes, changes, reconciliation, and recovery. |
| HC-S43 | A | Validate ePHI source/integrity with appropriate authentication, manifests, and reconciliation. |
| HC-S44 | Std | Authenticate people and workloads; test negative and credential-replay/fallback cases as appropriate. |
| HC-S45 | Std | Protect every PHI transmission, including APIs, exports, streaming, and external recipients. |
| HC-S46 | A | Validate transmission integrity, certificates, and received clinical content. |
| HC-S47 | A | Apply appropriate encrypted transport; private routing alone is insufficient. |
| HC-S48 | R | Ensure BA/subcontractor contracts cover required safeguards and incident/breach reporting. |
| HC-S49 | R, conditional | Protect group-health-plan sponsor separation and required plan-document/agent arrangements. |
| HC-S50 | Std | Maintain approved reasonable/appropriate policies and operating procedures. |
| HC-S51 | Std | Retain required written policies, documented actions, and assessments in controlled evidence storage. |
| HC-S52 | R | Retain required Security documentation for six years from creation/last effectiveness, whichever is later. |
| HC-S53 | R | Make needed procedures/documentation available to responsible implementers and authorized reviewers. |
| HC-S54 | R | Periodically review/update documentation and preserve effective-date/version history. |

Security/Platform/Data own most technical evidence; HR and Facilities own relevant workforce/physical records; Legal owns agreement and applicability decisions; Clinical participates in emergency and recovery validation. These responsibilities must be named and funded rather than assumed.

### C.3 Privacy and patient-rights duties

| ID | Requirement in plain language | Main owner/evidence |
|---|---|---|
| HC-P01 | Use/disclose PHI only for an applicable permitted or required purpose. | Purpose/release register; Privacy/Data |
| HC-P02 | Implement minimum-necessary scope while preserving statutory exceptions. | Role/release protocols and exception tests; Privacy/Data |
| HC-P03 | Validate actual treatment/payment/operations uses and qualifying inter-entity conditions. | Purpose determinations; Privacy/Clinical |
| HC-P04 | Limit incidental exposure in screens, logs, alerts, tickets, and shared outputs. | Safeguards/output review; Privacy/Platform |
| HC-P05 | Provide required individual/HHS disclosures through controlled authorized workflows. | Production/request records; HIM/Legal |
| HC-P06 | Verify recipients' identity and legal authority, with applicable exceptions. | Verification records; HIM/Privacy |
| HC-P07 | Handle representatives, minors, and abuse/endangerment exceptions correctly. | Authority/exception decisions; Privacy/Clinical |
| HC-P08 | Protect retained decedent PHI for 50 years after death without inventing a 50-year retention mandate. | Classification rationale; Privacy/Data |
| HC-P09 | Obtain valid authorizations with required scope, statements, signatures, expiration, and copies. | Approved forms and tests; HIM/Privacy |
| HC-P10 | Handle invalid/revoked authorizations and lawful compound/conditioning/reliance exceptions. | Form review and revocation tests; Privacy/Legal |
| HC-P11 | Distinguish actual psychotherapy notes and enforce their authorization/exception rules. | Classification/release tests; Privacy/Clinical |
| HC-P12 | Apply marketing authorization and remuneration conditions rather than mislabeling marketing as care. | Marketing decisions; Privacy/Legal |
| HC-P13 | Apply sale/remuneration authorization requirements or actual exceptions. | Commercial-sharing review; Privacy/Legal |
| HC-P14 | Respect facility-directory fields, recipients, preferences, and applicable emergency rules. | Preference/feed evidence; Clinical/Privacy |
| HC-P15 | Limit caregiver/family/disaster-relief releases using valid agreement or professional judgment. | Scoped decisions; Clinical/HIM |
| HC-P16 | Validate all Safe Harbor identifier rules and no-actual-knowledge conditions. | Transformation/release approval; Privacy/Data |
| HC-P17 | Obtain and implement a qualified Expert Determination where used. | Expert report and validation; Privacy/Data/expert |
| HC-P18 | Satisfy the specific re-identification-code conditions and protect its mechanism. | Code/separation evidence; Privacy/Data |
| HC-P19 | Validate limited-data-set releases and DUA safeguards, recipients, purposes, and violation handling. | DUA/release evidence; Privacy/Legal |
| HC-P20 | Limit fundraising extracts, provide required notice/opt-out, and honor suppression. | Notice/suppression tests; Privacy/foundation |
| HC-P21 | Apply genetic-underwriting and related purpose restrictions where health-plan functions exist, with actual exceptions. | Prohibition tests; Legal/health plan/Data |
| HC-P22 | Publish an accurate NPP with applicable rights, duties, contacts, dates, and surviving updates. | Current notice/practice review; Privacy/Legal |
| HC-P23 | Deliver/post notices, seek required acknowledgment, supply paper/electronic access, and retain versions. | Delivery/acknowledgment records; HIM/Privacy |
| HC-P24 | Process requested/agreed restrictions and lawful emergency/termination exceptions. | Restriction register and tests; Privacy/Data |
| HC-P25 | Enforce requested qualifying fully self-paid restrictions on health-plan payment/operations disclosures. | Service-level exclusion tests; Revenue/Privacy/Data |
| HC-P26 | Honor reasonable confidential-communication preferences under the applicable provider/plan rules. | Preference/delivery tests; HIM/Privacy |
| HC-P27 | Identify designated records and act on individual access within the required timing. | Scope and request tracker; HIM/Data |
| HC-P28 | Supply proper access format, cost-based fees, lawful denials/reviews, and Ciox-qualified third-party treatment. | Export/fee/denial evidence; HIM/Legal |
| HC-P29 | Decide amendment requests within the required timeline and permitted denial grounds. | Request/decision records; HIM/Clinical |
| HC-P30 | Link amendments/disagreements and appropriately propagate accepted changes and future-disclosure material. | Downstream reconciliation; HIM/Data |
| HC-P31 | Process accounting requests, timing, fees, exclusions, and lawful suspensions. | Accounting workflow; HIM/Privacy |
| HC-P32 | Maintain required patient-specific accounting content and the six-year lookback. | Disclosure ledger and sample accounting; Privacy/Data |
| HC-P33 | Designate the privacy official and complaint/information contact. | Leadership designations |
| HC-P34 | Provide role-appropriate privacy/breach training and keep records. | Training records; HR/Privacy |
| HC-P35 | Protect oral/paper PHI, displays, reports, local copies, and electronic outputs. | Operational safeguards; Privacy/Security |
| HC-P36 | Maintain a complaint process and protected disposition records. | Complaint register; Privacy |
| HC-P37 | Apply lawful sanctions and mitigate known harmful effects of violations. | Protected case/actions; HR/Privacy/Security |
| HC-P38 | Prohibit retaliation and improper rights waivers while preserving lawful protected disclosures. | Policies/case review; Legal/HR/Privacy |
| HC-P39 | Version privacy/breach policies, coordinate practice/NPP changes, and retain required documentation. | Policy/retention history; Privacy/Legal |
| HC-P40 | Include required BA contract duties and respond appropriately to known material violation patterns. | Contract/vendor review; Legal/Privacy |
| HC-P41 | Document hybrid/affiliated entities, plans, multiple functions, and qualifying shared arrangements. | Boundary/plan documents and tests; Legal/Privacy/Platform |
| HC-P42 | Assess limited legacy/transitional permissions rather than treating historic dates as current exemptions. | Legacy applicability record; Legal/HIM |

### C.4 Conditional disclosure permissions

These are permissions subject to legal/clinical conditions, not automatic requirements to disclose or rights to unrestricted platform access. A separate law may impose an actual disclosure duty. [^repo-hospital][^law-disclosures]

| ID | Category | Approval and scope that must be established |
|---|---|---|
| HC-DCL01 | Required by law | Actual legal requirement and any associated process/scope conditions |
| HC-DCL02 | Public health | Authority for disease/vital events/child abuse/FDA/exposure/qualifying occupational purposes; school-immunization agreement when required |
| HC-DCL03 | Abuse, neglect, domestic violence | Applicable law, consent or judgment, required notice, and safety exceptions |
| HC-DCL04 | Health oversight | Qualifying oversight purpose and exclusions; approved records rather than broad accounts |
| HC-DCL05 | Judicial/administrative process | Order scope or required notice/protective-order assurances and return/destruction terms |
| HC-DCL06 | Law enforcement | Applicable process/exception and limited permitted fields, including biological/DNA limits |
| HC-DCL07 | Coroners, medical examiners, funeral directors | Relevant duties, authority, and necessary release scope |
| HC-DCL08 | Cadaveric donation | Qualifying organ/eye/tissue procurement or transplant purpose/recipient |
| HC-DCL09 | Research without individual authorization | Valid waiver/alteration or preparatory/decedent representations; approved scope, risks, signatures, and reuse/destruction conditions |
| HC-DCL10 | Serious health/safety threats | Applicable good-faith professional judgment, legal/ethical conditions, and appropriate recipient |
| HC-DCL11 | Specialized government functions | Actual military/veterans, national-security, protective, State Department, custody, public-benefits, or NICS authority and role |
| HC-DCL12 | Workers' compensation | Applicable law, permitted necessity, and approved limited benefit/injury extract |

### C.5 Breach duties

| ID | Requirement | Practical evidence |
|---|---|---|
| HC-B01 | Evaluate definition, exceptions, presumption, and four-factor compromise risk. | Documented Privacy/Legal/Security decision |
| HC-B02 | Determine whether the actual PHI was unsecured, considering plaintext access and key exposure. | Encryption/access analysis |
| HC-B03 | Start the discovery clock correctly and notify without unreasonable delay within the ceiling. | Chronology and timing evidence |
| HC-B04 | Give required plain-language individual content and approved delivery. | Notices and delivery records |
| HC-B05 | Apply fewer-than-10 versus 10-or-more substitute-notice rules and relevant decedent exceptions. | Posting/contact evidence |
| HC-B06 | Apply media notice for more than 500 residents in the relevant jurisdiction. | Geographic count and notice |
| HC-B07 | Apply 500-or-more HHS reporting and smaller-breach year-end reporting without deferring individual notice. | Counts, log, submission evidence |
| HC-B08 | Obtain timely BA notice and affected-person/information detail. | Vendor terms and received notices |
| HC-B09 | Use only authorized law-enforcement delay; an oral request is limited to 30 days unless written support follows. | Official request and revised clock |
| HC-B10 | Retain proof of notices or non-breach determination and required administrative controls. | Protected complete case file/hold |

### C.6 HITECH effects

| ID | Effect | How the hospital addresses it |
|---|---|---|
| HC-H01 | Direct BA Security responsibility and liability | Effective scope, contracts, and supplier safeguards |
| HC-H02 | Breach notification | The discovery/assessment/notice process above |
| HC-H03 | BA privacy-contract compliance | Permitted purposes, delegated functions, and subcontractor restrictions |
| HC-H04 | Self-paid restrictions, minimum necessary, EHR accounting/electronic access, and remuneration limits | Patient/release workflows, including current-law and Ciox distinctions |
| HC-H05 | Marketing/fundraising changes | Authorization/remuneration and dependable opt-out suppression |
| HC-H06 | Enforcement, audits, and penalties | Operating evidence, lawful cooperation, and corrective action; no static penalty assumptions |
| HC-H07 | Recognized security practices | Evidence of actual relevant implementation over the preceding 12 months, not immunity |
| HC-H08 | Separate health-IT/non-HIPAA PHR programs where applicable | Additional applicability review; a data platform is not automatically a certified EHR or a substitute FTC/HIPAA regime |

### C.7 Transactions and regulatory cooperation

| ID | Requirement | Practical interpretation |
|---|---|---|
| HC-A01 | Standard administrative transactions/operating rules | Validate applicable claims, eligibility, referral, status, enrollment, payment/remittance, premium, and coordination exchanges through qualified components. |
| HC-A02 | Code sets and identifiers | Maintain effective-dated required codes, NPI/EIN and mapping evidence; tables do not automatically implement licensed guides. |
| HC-A03 | Trading-partner/clearinghouse duties | Do not defeat required standards through custom agreements; retain conformance and responsibility evidence. |
| HC-A04 | Finalized claims attachments/electronic signatures | Plan applicable X12 275/277 Version 6020 and adopted HL7 C-CDA/attachment/signature scope for May 26, 2028, not every analytics operation. |
| HC-A05 | Investigation cooperation, records, and lawful access | Produce approved evidence and preserve holds without indiscriminate regulator/admin accounts. |
| HC-A06 | Complaints, nonretaliation, and remediation | Maintain lawful reporting/cooperation channels, corrective action, and evidence supporting compliance claims. |

## Appendix D. Repository and original-claim coverage

### D.1 All eleven source files

Paths are relative to the repository root. The article is an explanatory synthesis, not a concatenation, so each source contributes to multiple parts.

| Source file | Information incorporated | Where to find it here |
|---|---|---|
| `HIPAA-Controls-README.md` | Fourteen introductory Fabric control areas and starter-checklist limitations | Parts 1, 3, 4, 7; Appendix C security safeguards |
| `regulatory-requirements\requirements.txt` | All 57 generic networking, identity, security, audit, governance, operational, eligibility, and design-validation notes | Parts 8, 11, 13; D.2 |
| `regulatory-requirements\refined-requirements.md` | Ten assurance gaps, corrected blanket claims, scope qualifications, official references | Parts 3, 6, 8, 11; D.2 |
| `market-research\requirements-matrix.md` | All 60 requirements, baseline variants, ratings, priorities, mappings, weights, POC and targets | Parts 1, 4, 7, 12, 13; Appendix B |
| `market-research\azure-databricks-assessment.md` | Every requirement family, material risks, deployment conditions, and product-source scope | Part 9; comparative catalogs and operating discussion |
| `market-research\snowflake-assessment.md` | Every requirement family, search/geography risks, recovery, purchasing, and source scope | Part 10; comparative catalogs and operating discussion |
| `market-research\microsoft-fabric-assessment.md` | Every requirement family, three strict-baseline gaps, identity/audit decisions, corrections and lifecycle | Part 8; parts 11-13 and Appendix B |
| `Databricks\README.md` | Hospital report navigation, principal shared-responsibility finding, research-only boundary | Parts 1 and 9; Appendix C |
| `Databricks\HIPAA-HITECH-Azure-Databricks.md` | All 138 named rows, current-law qualifications, identifier/deadline tables, architecture, limitations, readiness and implementation sequence | Parts 2-7, 9, 13; Appendices A and C |
| `Fabric\README.md` | Hospital Fabric report navigation, purpose, traceability, and document-review boundary | Parts 1 and 8; Appendices C and D |
| `Fabric\HIPAA-HITECH-Microsoft-Fabric.md` | The same 138 IDs with Fabric mechanisms; all fourteen starter-control updates and all 57 claim reviews; contracts, modes, policy/label scope, evidence, deadlines, recovery, gates | Parts 2-8, 11, 13; Appendices A, C, and D.3 |

### D.2 All 57 original Fabric entries

Each number refers to `requirements.txt`, not a new verified finding. Primary allocations reproduce the refined ten-gap document. Parts 8 and 11 explain corrections and uncertainty.

| Gap | Original entries | Topics preserved |
|---|---|---|
| G01 | 2, 4, 5, 6, 9, 39, 41, 42 | Semantic models, Copilot, private SQL hostname, Direct Lake/gateways, sharing, metrics, outbound gateway rules, SQL database support |
| G02 | 11, 12, 14, 46, 52 | Elevated/default/write roles, external-engine enforcement, serving-lakehouse authorization checks |
| G03 | 10, 13, 20, 33, 34, 35 | Delegated/user SQL execution, storage attribution, Conditional Access scope, CAE, licensing |
| G04 | 16, 17, 18, 19, 21, 22 | Audit enablement/loss, sensitive-log readers, Direct Lake coverage, filtering, enabled actions |
| G05 | 7, 15, 23 | Monitoring retention, permission history, historical lineage/provenance |
| G06 | 24, 25, 36, 37, 38, 57 | Tenant audit/admin permissions, domains, policy targeting, Govern visibility, required control approvals |
| G07 | 3, 26, 27, 28, 29, 30, 31, 40 | Monitoring network/version/Log Analytics, ingestion, billing/throttling, sharing/recreation, Spark startup |
| G08 | 8, 55 | Private-compatible controlled delivery and complete study/environment footprint accounting |
| G09 | 32, 47, 49, 53 | Artifact/key coverage, sensitive metadata, optional de-identification service, CMK/BYOK scope validation |
| G10 | 1, 43, 44, 45, 48, 50, 51, 54, 56 | Lifecycle, rule/engine previews, BAA scope, compliance/reference claims, NIST assertions, document provenance |

Topology, author/version metadata, control mappings, and tenant approvals require implementation-specific evidence. These generic questions describe no particular organization's configurations or decisions. An uninspected artifact is not a basis for inferring its contents or declaring a deployed violation.

### D.3 Complete reading guide to the 57-claim Fabric review

These statuses summarize section 13 of the hospital Fabric report. **Confirmed** is scoped public support, not a deployed finding; **Qualified** narrows an overbroad statement; **Updated** identifies changed documentation; **Unresolved** means conflict or insufficient support; **Design check** requires deployment-specific evidence. Where the repository or official pages disagree, the qualification below preserves that disagreement rather than silently replacing it. [^repo-fabric-hospital][^repo-refined]

| Original # | Review status | Corrected interpretation and evidence needed |
|---|---|---|
| 1 | Confirmed; dated | Managed-solution restriction is October 1, 2026; support ends December 31, 2027; customer-managed package dates from August 3, 2026. |
| 2 | Confirmed; scoped | Detailed workspace-private guidance excludes semantic models; default-model/matrix conflicts do not prove universal Power BI incompatibility. |
| 3 | Updated; conflict retained | Current Monitoring Item documents Private Link; legacy does not. The workspace support matrix still conflicts. |
| 4 | Confirmed; scoped | Copilot is unsupported in the documented private/closed scenarios; exact feature eligibility is required. |
| 5 | Updated | Private SQL hostnames/strings exist; required DNS/routing and the particular Databricks client connection are untested. |
| 6 | Qualified | Arbitrary cross-private-boundary Direct Lake is not guaranteed; gateways/DirectQuery are possible paths, not the only imaginable supported topology. |
| 7 | Updated | Current monitoring's 30 days is a configurable default, not a permanent maximum. |
| 8 | Qualified | Deployment pipelines have private-workspace limitations; Git and appropriately supported APIs/tooling are separate options. |
| 9 | Confirmed; scoped | Workspace-private item sharing is unsupported; existing shared-link behavior must be checked before restrictions change. |
| 10 | Qualified | Delegated storage uses the owner, but properly configured SQL/model authorization and human correlation can still exist. |
| 11 | Qualified | Broad roles are powerful; user-identity SQL RLS has a documented elevated-role exception. Avoid universal bypass/protection claims. |
| 12 | Confirmed; scoped | DefaultReader tracks ReadAll for lakehouse/mirrored databases and Read for mirrored catalogs; examine combined grants. |
| 13 | Qualified | User mode supplies OneLake table enforcement; delegated SQL controls are a different valid mechanism requiring deliberate administration. |
| 14 | Confirmed | ReadWrite roles cannot carry RLS/CLS; separate restricted readers from writers. |
| 15 | Updated | Permission-change events exist; complete historical effective access still needs cross-layer snapshots and changes. |
| 16 | Confirmed | SQL auditing is off by default and must be configured. |
| 17 | Confirmed | SQL configured events can be lost under load; there is no demonstrated lossless all-engine ledger. |
| 18 | Qualified | Audit-folder privileges and SQL-endpoint browsing differ; query text can contain PHI and must be protected. |
| 19 | Qualified | Direct Lake reads are not all SQL reads; SQL checks or DirectQuery fallback can still occur. |
| 20 | Qualified | Backend/temporary-grant attribution needs initiator/query/model correlation, not an assumption that all records identify the same actor. |
| 21 | Confirmed | Predicate-excluded events are removed before the SQL audit write. |
| 22 | Confirmed | Predicates only apply to enabled actions/groups. |
| 23 | Qualified | Current lineage relationships are not a complete immutable historical research-provenance record. |
| 24 | Qualified; scoped | Purview search/export roles differ from Exchange cmdlet/enablement roles; establish approved least-privilege access or central SOC evidence delivery. |
| 25 | Qualified | Endpoint roles and OAuth scopes are distinct; follow actual delegated/app requirements and preview status for each admin API. |
| 26 | Unresolved; version-specific | Current monitoring's omission of an earlier Log Analytics coexistence restriction does not establish support. |
| 27 | Qualified; legacy | Legacy lacks documented category filtering; do not generalize that restriction to every current collection mechanism. |
| 28 | Confirmed | Monitoring consumes Fabric capacity and needs sizing/funding. |
| 29 | Confirmed; scoped | Power BI/Activator throttling and monitoring ingestion/Eventhouse/dashboard behavior differ. |
| 30 | Qualified; legacy | Legacy sharing roles and query permissions are not a universal current-item permission prescription. |
| 31 | Qualified; legacy | Read-only monitoring is documented; roughly 15-minute recreation is legacy guidance, not a general SLA. |
| 32 | Qualified | Reporting artifacts are outside workspace CMK; eligible import-model BYOK is a distinct narrower mechanism. |
| 33 | Qualified | CA is not proven workspace/domain-targetable, and it is not limited to user/group conditions. |
| 34 | Confirmed | Fabric does not support CAE session control in the reviewed guidance; measure revocation. |
| 35 | Confirmed; scoped | General CA requires P1/equivalent entitlement; risk policies require P2; related controls need their own licenses. |
| 36 | Confirmed | Domains organize/govern content; assignment is not item authorization. |
| 37 | Qualified | Tenant administration does not imply tenant-wide targeting or unrestricted evidence visibility for everyone. |
| 38 | Confirmed; scoped | Govern has a Private Link limitation; use an approved administration/evidence alternative. |
| 39 | Scoped limitation; source conflict | Hospital review follows the tenant overview's exclusion; the Metrics app page supports tenant Private Link and excludes workspace isolation on its installation workspace. Resolve the actual scenario. |
| 40 | Qualified | Managed-VNet starter pools/startup have documented constraints; a three-to-five-minute estimate is not a demonstrated SLA. |
| 41 | Confirmed; scoped | Private Link restricts portal rule configuration; supported REST/gateway paths still need authorization and network proof. |
| 42 | Confirmed; scoped | SQL database workspace-private support differs from tenant-private support and from warehouse/SQL analytics endpoints. |
| 43 | Unresolved; status conflict | Connection-rule UI and item/workload matrix use different GA/preview labels; approve the exact combination. |
| 44 | Confirmed; scoped | Several native OneLake paths are GA, while Eventhouse RLS/authorized third-party integration remain preview in the reviewed table. |
| 45 | Confirmed default caution | Core Online Services exclude Previews; examine governing terms and explicit exceptions before PHI admission. |
| 46 | Qualified | Unauthorized engines are evaluated as direct user access; unsafe filtering can block reads, not universally prohibit every third-party read. |
| 47 | Confirmed | Protected data does not guarantee hidden schema/model names; avoid PHI in metadata and Git. |
| 48 | Qualified | Product Terms explicitly name Fabric; missing generic-page references do not prove exclusion or complete feature coverage. |
| 49 | Qualified | No automatic legal de-identification is established; validated customer transformations are possible and an external service is optional. |
| 50 | Qualified | No complete Fabric-specific TRE blueprint was established; Azure TRE is an accelerator, not a Fabric certification. |
| 51 | Updated | Microsoft publishes a patient-data security scenario; a scenario is not hospital-specific operating proof. |
| 52 | Design check | Validate serving topology, public-access settings, memberships, source/model policies, and identity mode; test independent source access. |
| 53 | Design check; key qualification | Distinguish source CMK, model BYOK, default encryption, artifact exclusions, and each protection designation's evidenced scope. |
| 54 | Design check | Document applicable NIST revision/control, scope, evidence, and lawful alternatives in architecture comparisons. |
| 55 | Design check | Compare complete footprints using study/environment multipliers, shared resources, and separately counted recovery overhead. |
| 56 | Design check | Require author/owner, version, date, sources, exact item variants, assumptions, and approved change history for the assessment. |
| 57 | Design check | Verify required CA/session/label approvals, licenses, owners, and tests; unavailable safeguards need approved alternatives or remain blockers. |

The fourteen starter controls are covered by the shared Security catalog and the explanations of encryption, identity, audit, integrity, risk, access, workforce, incidents, recovery, facilities, endpoints, and media. The expanded Privacy, breach, HITECH, transaction, and admission sections supply areas the introductory checklist omitted. Stable source numbering, citations, and complete requirement catalogs preserve analytical traceability. [^repo-starter][^repo-fabric-hospital]

## References

Repository citations establish provenance for the synthesis and proposed methodologies. Primary citations support the legal and technical distinctions; they do not attest to hospital deployment. Product pages and contracts are mutable. Preserve the specific versions, actual signed/incorporated terms, and configuration evidence used for a real decision.

### Repository sources

[^repo-starter]: `HIPAA-Controls-README.md`. Introductory Fabric implementation ideas; includes placeholder references and is not operating evidence.
[^repo-original]: `regulatory-requirements\requirements.txt`. Fifty-seven generic working assumptions and design checks; stable numbering retained.
[^repo-refined]: `regulatory-requirements\refined-requirements.md`. Ten public-documentation assurance themes and corrections, reviewed September 30, 2026.
[^repo-matrix]: `market-research\requirements-matrix.md`. Sixty-requirement framework, applicability, ratings, strict variants, mappings, weights, POC, and proposed targets.
[^repo-databricks]: `market-research\azure-databricks-assessment.md`. Conditional Azure Databricks assessment and product-source register.
[^repo-snowflake]: `market-research\snowflake-assessment.md`. Conditional Snowflake-on-Azure assessment and product-source register.
[^repo-fabric]: `market-research\microsoft-fabric-assessment.md`. Conditional Fabric assessment, strict-baseline gaps, identity/audit design, and lifecycle.
[^repo-databricks-readme]: `Databricks\README.md`. Scope and principal finding of the hospital report.
[^repo-hospital]: `Databricks\HIPAA-HITECH-Azure-Databricks.md`. Detailed proposed hospital framework and its legal/product registers, researched September 30, 2026.
[^repo-fabric-readme]: `Fabric\README.md`. Scope, shared identifiers, and original-document review boundary of the Fabric hospital report.
[^repo-fabric-hospital]: `Fabric\HIPAA-HITECH-Microsoft-Fabric.md`. Companion 138-position hospital framework, fourteen starter-control updates, and all 57 Fabric claim reviews, researched September 30, 2026.

### Legal, regulatory, and methodology sources

[^law-scope]: 45 CFR Parts 160 and 164, applicability, definitions, organization, and BA provisions. `https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-160`; `https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164`
[^law-cloud]: HHS, HIPAA and cloud computing; Microsoft HIPAA/HITECH and shared-responsibility guidance. `https://www.hhs.gov/hipaa/for-professionals/special-topics/health-information-technology/cloud-computing/index.html`; `https://learn.microsoft.com/en-us/compliance/regulatory/offering-hipaa-hitech`
[^microsoft-terms]: Microsoft Product Terms, Privacy and Security/Core Online Services, explicitly listed services and preview exclusion; incorporated BAA guidance. The refined review used the May 2026 DPA; actual governing versions and express feature exceptions must be retained. `https://www.microsoft.com/licensing/terms/product/PrivacyandSecurityTerms/all`; `https://learn.microsoft.com/en-us/azure/compliance/offerings/offering-hipaa-us`; `https://www.microsoft.com/licensing/docs/documents/download/MicrosoftProductandServicesDPA(WW)(English)(May2026)(CR).docx`
[^law-security-general]: 45 CFR 164.306, Security Rule general requirements and required/addressable framework. `https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-C/section-164.306`
[^law-security-controls]: 45 CFR 164.308, 164.310, 164.312, and 164.314, administrative, physical, technical, and organizational safeguards. `https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-C`
[^law-documentation]: 45 CFR 164.316, required policies, documentation, six-year retention, availability, and updates. `https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-C/section-164.316`
[^law-privacy]: 45 CFR Part 164 Subpart E, including 164.502-.514, .520, and .522. `https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-E`
[^law-access]: 45 CFR 164.524, individual access, scope, timing, formats, and denials. `https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-E/section-164.524`
[^law-amendment]: 45 CFR 164.526, amendments and propagation. `https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-E/section-164.526`
[^law-accounting]: 45 CFR 164.528, disclosure accounting, exclusions, content, lookback, and timing. `https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-E/section-164.528`
[^law-privacy-administration]: 45 CFR 164.530, privacy administration and documentation. `https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-E/section-164.530`
[^law-deidentification]: 45 CFR 164.514 and HHS de-identification guidance, Safe Harbor, Expert Determination, code safeguards, and limited data sets. `https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-E/section-164.514`; `https://www.hhs.gov/hipaa/for-professionals/privacy/special-topics/de-identification/index.html`
[^law-disclosures]: 45 CFR 164.512, conditional public-interest and special-purpose disclosures. `https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-E/section-164.512`
[^law-breach]: 45 CFR 164.400-.414, breach assessment and notification. `https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-164/subpart-D`
[^law-hitech]: HITECH provisions, including 42 USC 17935, 17936, 17940, and 17941; legal distinctions are discussed in the hospital report's section 7. `https://usc-cdn.house.gov/view.xhtml?req=granuleid%3AUSC-prelim-title42-section17935&num=0&edition=prelim`; `https://usc-cdn.house.gov/view.xhtml?edition=prelim&num=0&req=granuleid%3AUSC-prelim-title42-section17941`
[^law-part2]: HHS, Part 2 overview and 2024 final-rule dates. `https://www.hhs.gov/hipaa/part-2/index.html`
[^law-research]: HHS, HIPAA research guidance; controlling waiver/representation provisions in 164.512(i). `https://www.hhs.gov/hipaa/for-professionals/special-topics/research/index.html`
[^law-fda]: FDA, Part 11 electronic-record/signature scope and application. `https://www.fda.gov/regulatory-information/search-fda-guidance-documents/part-11-electronic-records-electronic-signatures-scope-and-application`
[^law-transactions]: 45 CFR Part 162 and CMS Administrative Simplification resources. `https://www.ecfr.gov/current/title-45/subtitle-A/subchapter-C/part-162`; `https://www.cms.gov/training-education/look-up-topics/hipaa-administrative-simplification`
[^law-attachments]: CMS, claims attachments/electronic signatures final-rule fact sheet, effective May 26, 2026 and compliance May 26, 2028. `https://www.cms.gov/newsroom/fact-sheets/administrative-simplification-adoption-standards-health-care-claims-attachments-transactions`
[^law-proposal]: HHS Security Rule NPRM and OIRA agenda RIN 0945-AA22; forecast is not a final obligation. `https://www.hhs.gov/hipaa/for-professionals/security/hipaa-security-rule-nprm/index.html`; `https://www.reginfo.gov/public/do/eAgendaViewRule?RIN=0945-AA22&pubId=202510`
[^law-reproductive]: HHS, reproductive-health rule partial-vacatur notice and surviving NPP requirements. `https://www.hhs.gov/hipaa/for-professionals/special-topics/reproductive-health/final-rule-fact-sheet/index.html`
[^law-ciox]: HHS, January 23, 2020 right-of-access court-order notice. `https://www.hhs.gov/hipaa/court-order-right-of-access/index.html`
[^nist-hipaa]: NIST SP 800-66 Rev. 2, HIPAA Security Rule implementation and mapping resource. `https://csrc.nist.gov/pubs/sp/800/66/r2/final`
[^nist-controls]: NIST SP 800-53 Rev. 5, security/privacy control catalog; the illustrative crosswalk is reproduced from the repository matrix, not asserted as a completed baseline assessment. `https://csrc.nist.gov/pubs/sp/800/53/r5/upd1/final`

### Microsoft Fabric sources

[^fabric-overview]: Microsoft, Fabric overview and security overview. `https://learn.microsoft.com/en-us/fabric/fundamentals/microsoft-fabric-overview`; `https://learn.microsoft.com/en-us/fabric/security/security-overview`
[^fabric-network]: Microsoft, workspace and tenant Private Link scenarios/limitations, private SQL strings, management paths, and unresolved monitoring/Metrics conflicts. `https://learn.microsoft.com/en-us/fabric/security/security-workspace-level-private-links-support`; `https://learn.microsoft.com/en-us/fabric/security/security-private-links-overview`
[^fabric-availability]: Microsoft, item-level security feature matrix; check item variants and conflicts with detailed feature pages. `https://learn.microsoft.com/en-us/fabric/security/security-feature-availability`
[^fabric-releases]: Microsoft, update archive: May 2026 OneLake security/data-access-role GA and October/November 2025 workspace-CMK GA entries. Announcement months are not an independently established first-release day. `https://learn.microsoft.com/en-us/fabric/fundamentals/whats-new-archive`
[^fabric-reporting]: Microsoft, gateway-based Power BI access to restricted lakehouses and Direct Lake security integration. `https://learn.microsoft.com/en-us/fabric/security/security-workspace-private-links-example-power-bi-virtual-network`; `https://learn.microsoft.com/en-us/fabric/fundamentals/direct-lake-security-integration`
[^fabric-onelake]: Microsoft, OneLake permissions, roles, writer limitations, and engine support. `https://learn.microsoft.com/en-us/fabric/onelake/security/data-access-control-model`; `https://learn.microsoft.com/en-us/fabric/onelake/security/read-secured-data`
[^fabric-identity]: Microsoft, SQL analytics endpoint identity modes and semantic-model operation attribution. `https://learn.microsoft.com/en-us/fabric/onelake/security/sql-analytics-endpoint-onelake-security`; `https://learn.microsoft.com/en-us/fabric/enterprise/powerbi/semantic-model-operations`
[^fabric-workspace-identity]: Microsoft, workspace identity as a managed service principal, authorized use, lifecycle, and recovery limitations. `https://learn.microsoft.com/en-us/fabric/security/workspace-identity`
[^fabric-keys]: Microsoft, workspace CMK item coverage and separate Power BI BYOK scope. `https://learn.microsoft.com/en-us/fabric/security/workspace-customer-managed-keys`; `https://learn.microsoft.com/en-us/fabric/enterprise/powerbi/service-encryption-byok`
[^fabric-audit]: Microsoft, SQL audit defaults, permissions, action groups, predicates, and best-effort performance. `https://learn.microsoft.com/en-us/fabric/data-warehouse/sql-audit-logs`
[^fabric-diagnostics]: Microsoft, OneLake diagnostics, identity/workload coverage, delivery boundaries, and log protection. `https://learn.microsoft.com/en-us/fabric/onelake/onelake-diagnostics-overview`
[^fabric-monitoring]: Microsoft, updated and legacy workspace monitoring, retention, Private Links, and throttling. `https://learn.microsoft.com/en-us/fabric/fundamentals/workspace-monitoring-overview`
[^fabric-governance]: Microsoft, domains, DLP targets, Govern limitations, and tenant audit. `https://learn.microsoft.com/en-us/fabric/governance/domains`; `https://learn.microsoft.com/en-us/purview/dlp-powerbi-get-started`; `https://learn.microsoft.com/en-us/fabric/governance/onelake-catalog-govern`; `https://learn.microsoft.com/en-us/fabric/admin/track-user-activities`
[^fabric-protection]: Microsoft, supported label-protection/export paths and DLP item/mode/trigger/action restrictions. `https://learn.microsoft.com/en-us/fabric/governance/information-protection`; `https://learn.microsoft.com/en-us/purview/dlp-powerbi-get-started`
[^fabric-admin-evidence]: Microsoft, Purview audit roles/retention and endpoint-specific admin permissions. `https://learn.microsoft.com/en-us/purview/audit-get-started`; `https://learn.microsoft.com/en-us/purview/audit-log-retention-policies`; `https://learn.microsoft.com/en-us/rest/api/fabric/admin/items/list-items`
[^fabric-conditional]: Microsoft, Fabric Conditional Access/CAE limitations and Entra licensing/targeting. `https://learn.microsoft.com/en-us/fabric/security/security-conditional-access`; `https://learn.microsoft.com/en-us/entra/identity/conditional-access/overview`
[^fabric-metrics]: Microsoft, Capacity Metrics app and its Private Link distinctions. `https://learn.microsoft.com/en-us/fabric/enterprise/metrics-app`
[^fabric-managed-vnet]: Microsoft, managed virtual networks and Spark startup considerations. `https://learn.microsoft.com/en-us/fabric/security/security-managed-vnets-fabric-overview`
[^fabric-outbound]: Microsoft, outbound protection and connection-rule support. `https://learn.microsoft.com/en-us/fabric/security/workspace-outbound-access-protection-overview`; `https://learn.microsoft.com/en-us/fabric/security/workspace-outbound-access-protection-allow-list-connector`
[^fabric-healthcare]: Microsoft, healthcare data solutions lifecycle and customer-managed package. `https://learn.microsoft.com/en-us/industry/healthcare/healthcare-data-solutions/overview`
[^fabric-reference]: Microsoft, published patient-data security scenario; Azure TRE project status. `https://learn.microsoft.com/en-us/fabric/security/security-scenario`; `https://raw.githubusercontent.com/microsoft/AzureTRE/main/README.md`
[^fabric-deidentification]: Microsoft, separate Azure Health Data Services de-identification service. `https://learn.microsoft.com/en-us/azure/healthcare-apis/deidentification/overview`
[^fabric-recovery]: Microsoft, workspace/item retention, supported reconstruction, and OneLake soft-delete/paired-region/asynchronous-DR limits. `https://learn.microsoft.com/en-us/fabric/admin/retention-recovery`; `https://learn.microsoft.com/en-us/fabric/security/experience-specific-guidance`; `https://learn.microsoft.com/en-us/fabric/onelake/onelake-disaster-recovery`

### Azure Databricks sources

[^databricks-hipaa]: Microsoft, Azure Databricks HIPAA requirements, notebook-result options, metadata restrictions, and regional/preview support. `https://learn.microsoft.com/en-us/azure/databricks/security/privacy/hipaa`
[^databricks-profile]: Microsoft, compliance profile and enhanced-security settings, Premium/add-on, permanence, and VNet enforcement. `https://learn.microsoft.com/en-us/azure/databricks/security/privacy/security-profile`; `https://learn.microsoft.com/en-us/azure/databricks/security/privacy/enhanced-security-compliance`
[^databricks-keys]: Microsoft, customer-managed-key coverage by data location and compute plane. `https://learn.microsoft.com/en-us/azure/databricks/security/keys/customer-managed-keys`
[^databricks-governance]: Microsoft, Unity Catalog best practices and supported filters/masks. `https://learn.microsoft.com/en-us/azure/databricks/data-governance/unity-catalog/best-practices`; `https://learn.microsoft.com/en-us/azure/databricks/data-governance/unity-catalog/filters-and-masks/`
[^databricks-abac]: Microsoft, ABAC/filter/mask requirements and AI Search index noninheritance. `https://learn.microsoft.com/en-us/azure/databricks/data-governance/unity-catalog/abac/requirements`
[^databricks-network]: Microsoft, private-connectivity planes, serverless connectivity, and enforced egress-policy limitations. `https://learn.microsoft.com/en-us/azure/databricks/security/network/concepts/private-link`; `https://learn.microsoft.com/en-us/azure/databricks/security/network/serverless-network-security/serverless-private-link`; `https://learn.microsoft.com/en-us/azure/databricks/security/network/serverless-network-security/manage-network-policies`
[^databricks-audit]: Microsoft, audit system table, preview status, attribution, and workspace deletion. `https://learn.microsoft.com/en-us/azure/databricks/admin/system-tables/audit-logs`
[^databricks-system-tables]: Microsoft, system-table retention Beta, excluded schemas, recovery and real-time limits. `https://learn.microsoft.com/en-us/azure/databricks/admin/system-tables/`
[^databricks-diagnostics]: Microsoft, diagnostic coverage, including account/workspace differences. `https://learn.microsoft.com/en-us/azure/databricks/admin/account-settings/audit-logs`
[^databricks-operations]: Microsoft, deployment bundles, compute policies, and data disposal/VACUUM. `https://learn.microsoft.com/en-us/azure/databricks/dev-tools/bundles/`; `https://learn.microsoft.com/en-us/azure/databricks/admin/clusters/policies`; `https://learn.microsoft.com/en-us/azure/databricks/tables/operations/vacuum`
[^databricks-secrets]: Microsoft, secret access/redaction and Key Vault-backed scope requirements. `https://learn.microsoft.com/en-us/azure/databricks/security/secrets/`
[^databricks-recovery]: Microsoft, disaster recovery, managed-DR prerequisites/exclusions, and Mission Critical bundling/configuration. `https://learn.microsoft.com/en-us/azure/databricks/admin/disaster-recovery`; `https://learn.microsoft.com/en-us/azure/databricks/admin/managed-disaster-recovery`; `https://learn.microsoft.com/en-us/azure/databricks/admin/mission-critical`

### Snowflake sources

[^snowflake-editions]: Snowflake, editions, Business Critical/PHI positioning, and signed BAA prerequisite. `https://docs.snowflake.com/en/user-guide/intro-editions`
[^snowflake-ml]: Snowflake, ML platform capabilities. `https://docs.snowflake.com/en/developer-guide/snowflake-ml/overview`
[^snowflake-policies]: Snowflake, row-access and column-security controls and workload identity federation. `https://docs.snowflake.com/en/user-guide/security-row-intro`; `https://docs.snowflake.com/en/user-guide/security-column-intro`; `https://docs.snowflake.com/en/user-guide/workload-identity-federation`
[^snowflake-collaboration]: Snowflake, Secure Data Sharing and clean-room scope/variants. `https://docs.snowflake.com/en/user-guide/data-sharing-intro`; `https://docs.snowflake.com/en/user-guide/cleanrooms/introduction`
[^snowflake-search]: Snowflake, Cortex Search owner's-rights model. `https://docs.snowflake.com/en/user-guide/snowflake-cortex/cortex-search/cortex-search-overview`
[^snowflake-inference]: Snowflake, effective cross-region inference parameters and defaults. `https://docs.snowflake.com/en/user-guide/snowflake-cortex/cross-region-inference`
[^snowflake-history]: Snowflake, supported Access History and 365-day window. `https://docs.snowflake.com/en/sql-reference/account-usage/access_history`
[^snowflake-recovery]: Snowflake, Time Travel, Fail-safe, and account replication/failover. `https://docs.snowflake.com/en/user-guide/data-time-travel`; `https://docs.snowflake.com/en/user-guide/data-failsafe`; `https://docs.snowflake.com/en/user-guide/account-replication-intro`
[^snowflake-network-keys]: Snowflake, Azure Private Link, outbound connectivity, and Tri-Secret Secure. `https://docs.snowflake.com/en/user-guide/privatelink-azure`; `https://docs.snowflake.com/en/user-guide/private-connectivity-outbound`; `https://docs.snowflake.com/en/user-guide/security-encryption-tss`
[^snowflake-budgets]: Snowflake, warehouse resource-monitor scope and separate budget needs. `https://docs.snowflake.com/en/user-guide/resource-monitors`
