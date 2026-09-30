# Snowflake on Azure: hospital and research security/compliance assessment

**As of:** September 30, 2026.  
**Requirements:** `requirements-matrix.md`, all 60 IDs. Its ratings and regulatory source register apply here.  
**Disposition:** **Conditional shortlist**, especially for governed warehouse analytics and controlled collaboration. Not authorized for production PHI by this assessment.

## 1. Scope and overall finding

The baseline is commercial Snowflake on Azure in a US account region, **Business Critical**, a signed Snowflake BAA, and approved table, network, sharing, and AI/ML configurations. Snowflake's editions documentation identifies Business Critical for sensitive/PHI workloads and requires a signed BAA before PHI storage. Azure hosting does not itself supply the hospital's Snowflake contract. [S01]

The strongest security case is a governed query boundary: carefully scoped identities and roles, native row/masking policies, supported access history, controlled sharing, and managed recovery capabilities. The largest residual risks are **privileged policy changes, external/staged/copied data, search and inference services with different authorization/geography, and the cost/operations consequences of required features**. [S04-S14, S20]

**Analyst conclusion:** A strong candidate for a hospital that wants managed SQL analytics and controlled collaboration while retaining responsibility for data-use policy, evidence collection, research validation, and receiving systems. Do not portray contemporary Snowflake as SQL-only: its documented ML platform includes feature engineering, CPU/GPU training, model registry, and monitoring capabilities. Their hospital suitability and PHI eligibility must still be demonstrated. [S19]

## 2. Strengths and limitations

| Dimension | Documented strength | Hospital implication / limitation |
|---|---|---|
| Regulated workload baseline | Business Critical PHI positioning and BAA requirement. [S01] | Edition is a contractual/feature prerequisite, not proof of a compliant hospital configuration. |
| Human and workload identity | Federated SSO and workload identity federation, including Azure identities. [S02, S03] | Validate actual connector/driver support and remaining credential paths. |
| Query authorization | Enterprise-or-higher row-access and column-masking policy controls. [S04, S05] | Policy administration must be separate from researcher access; raw files and receiving systems need their own controls. |
| Audit | Access History provides object/column-oriented query evidence with a documented 365-day view window. [S06] | Scope/latency and other log sources must be assessed; this is not a universal lossless trail. |
| Private access and keys | Business Critical private connectivity and Tri-Secret Secure options. [S07-S09] | Ingress, outbound access, stage traffic, key availability, and recipient accounts are separate design concerns. |
| Collaboration | Secure Data Sharing and clean rooms support restricted collaboration patterns. [S15, S20] | Legal authority, inference risk, outputs, and copied results remain hospital responsibilities. |
| Recovery | Time Travel and account replication/failover capabilities. [S10-S12] | Fail-safe is vendor-mediated, best effort, and not an ordinary archival or guaranteed rapid-restore service. |
| Research/ML | End-to-end ML capabilities over governed data. [S19] | Container/notebook/model/network/region eligibility requires individual review. |

### Two AI risks that need explicit controls

**Cortex Search operates with owner's rights.** Query permission on a search service is not the same as caller permission on every original table row. **Analyst implication:** do not assume existing source-table researcher restrictions automatically protect every search result. Scope the indexed corpus and service grants to the intended audience; where application filtering is needed, enforce it in trusted server-side authorization and test attempts to omit or alter filters. [S14]

**Cortex cross-region inference can move prompt/response processing outside the account region.** The reviewed documentation states that new organizations after **March 9, 2026** use `ANY_REGION` as a default, while other accounts can have different inherited defaults. Data residency of stored tables therefore does not prove inference residency. Set and verify an approved routing value, including `DISABLED` where home-region-only processing is required, and confirm resulting model availability. [S13]

These are documented design hazards, not claims that the hospital has exposed data. No assertion is made here that every Cortex, clean-room, or ML feature is included in the hospital's BAA.

## 3. Complete requirements gap analysis

**M:** documented native capability; **C:** material conditions; **G:** documented baseline gap; **U:** insufficient evidence. All configuration remains untested.

### Legal and research

| ID | Rating | Evidence / remaining gap | Required closure |
|---|---|---|---|
| L01 | C | Signed BAA is required before PHI storage; hospital contract absent. [S01] | Counsel confirms Business Critical agreement, BAA, and subcontractor terms. |
| L02 | U | Entire selected service/region/preview inventory is not proven contractually eligible. | Obtain written scope for Cortex, search, clean rooms, ML, containers, support, and DR. |
| L03 | U | No hospital-required current scoped assurance reports reviewed. | Review reports, exceptions, bridge letters, and any applicable authorization. |
| L04 | C | Edition/capabilities do not perform the hospital risk assessment. [S01, REG01] | Approve responsibility allocation across Snowflake, Azure, clients, and hospital. |
| L05 | C | Record-specific legal use/disclosure rules are hospital policy. [REG03] | Tag applicable data and enforce approved roles/purpose policies. |
| L06 | C | Row policies/sharing cannot grant research authority. [S04, S20, REG06] | Validate investigator, purpose, study, sponsor, and agreement requirements. |
| L07 | C | No validated regulated application or signature workflow supplied. [REG05] | Establish applicability and validate the actual records/signatures process. |
| L08 | C | Account region, replication targets, and inference processing can differ. [S12, S13] | Approve every location and pin inference/replication policies. |
| L09 | U | Contractual incident/notification/evidence terms unavailable. | Counsel approves terms, accountable parties, and escalation. |
| L10 | C | Time Travel/Fail-safe create distinct historical-data lifecycle requirements. [S10, S11] | Align holds/deletion/retention with legal and research schedules. |

### Identity and study boundaries

| ID | Rating | Evidence / remaining gap | Required closure |
|---|---|---|---|
| I01 | M | Federated authentication and SSO are documented. [S02] | Enforce approved MFA and test clients, guests, emergency and fallback paths. |
| I02 | M | Workload identity federation supports short-lived cloud-provider authentication. [S03] | Verify selected Azure workloads/drivers; remove unnecessary long-lived credentials. |
| I03 | C | Policy controls support separate policy administration. [S04, S05] | Separate administrative, policy, writer, researcher, and audit authorities. |
| I04 | C | Role/policy/sharing boundaries must reflect studies and environments. [S04, S20] | Deny cross-study access and broad inherited role activation. |
| I05 | C | Table/view policies are not identical to raw-stage, external-engine, or search authorization. [S04, S05, S14] | Test every permitted SQL/file/search/BI/sharing path. |
| I06 | C | Row policy does not prevent inserts or modifications of visible rows. [S04] | Restrict writer roles independently of read filtering. |
| I07 | C | Privilege and support oversight were not assessed. | Implement approved elevation, emergency access, and vendor-support procedures. |
| I08 | C | Study/guest expiry and active-session behavior untested. [S02, S03] | Reconcile identities and prove revocation across tokens, clients, and shares. |

### Network, encryption, and secrets

| ID | Rating | Evidence / remaining gap | Required closure |
|---|---|---|---|
| N01 | C | Azure Private Link is Business Critical functionality; public-path enforcement is separate. [S07] | Configure private-only controls and test UI/API/SQL/stage/DNS paths. |
| N02 | C | Private outbound connectivity is configured per supported feature, not inferred from ingress. [S08] | Restrict integrations, destinations, stages, applications, and other exfiltration channels. |
| N03 | C | Azure networking and feature-specific external connectivity have prerequisites. [S07, S08] | Prove EHR/on-premises/partner integration and failover routing. |
| N04 | M | Automatic encryption is documented in the edition feature matrix. [S01] | Inventory external storage, clients, integrations, and certificate handling. |
| N05 | C | Tri-Secret Secure adds customer key control; external/feature-specific coverage remains separate. [S09] | Verify each PHI artifact, table type, external volume, export, and recovery path. |
| N06 | C | Revoking the customer key can prevent Snowflake decryption. [S09] | Test approved rotation, recovery, access control, and key outage procedures. |
| N07 | C | No deployed secrets/metadata/query-literal hygiene assessed. | Protect integrations/keys; minimize PHI in identifiers, SQL, logs, notebooks, and source code. |
| N08 | C | Required clients/AI/ML features have distinct networking prerequisites. [S07, S08, S19] | Prove all selected workloads work under approved isolation without public exceptions. |

### Governance and research workflows

| ID | Rating | Evidence / remaining gap | Required closure |
|---|---|---|---|
| G01 | C | Sensitive classification supports native/custom categories; Enterprise-or-higher. [S16] | Validate clinical/free-text/genomic detection, custodians, and false negatives. |
| G02 | C | Data lineage is documented; historical study reproducibility is broader. [S17] | Preserve cohort, consent, policy, software, and analysis-release versions. |
| G03 | C | Mapping-table policies can express access conditions; legal consent rules are external input. [S04] | Implement trusted eligibility records and downstream-copy disposition. |
| G04 | C | Dynamic masking/external tokenization do not themselves establish HIPAA de-identification. [S05, REG04] | Validate legally approved transformations and release risk. |
| G05 | C | Secure sharing offers selected read-only object access. [S20] | Approve recipients, account/contract eligibility, region, and copied-result controls. |
| G06 | C | Clean rooms constrain analyses but are not a complete managed researcher endpoint/enclave. [S15] | Validate template/output/inference controls, endpoint access, and release review. |
| G07 | C | Search owner's rights and inference routing differ from ordinary table-query assumptions. [S13, S14] | Approve service eligibility and implement trusted retrieval/geography/output controls. |
| G08 | C | External data/engine access creates additional policy boundaries. [S08, S20] | Reproduce authorization, lineage, and disposition on exported or externally read data. |
| G09 | C | Query/share revocation cannot be assumed to recall already copied outputs. [S20] | Apply BI/endpoint/export safeguards and recipient retention obligations. |
| G10 | C | Generic data/ML capability does not validate clinical data semantics. [S19] | Test required FHIR/HL7/OMOP/DICOM transformations and quality. |
| G11 | C | ML lifecycle functionality is not clinical-model/medical-device approval. [S19, REG05] | Obtain intended-use, validation, safety, and monitoring review. |

### Audit and incident evidence

| ID | Rating | Evidence / remaining gap | Required closure |
|---|---|---|---|
| A01 | C | Access History records object/column access for supported activity. [S06] | Combine query/login/security/integration/client logs; test coverage and denied actions. |
| A02 | C | User/query identifiers exist; delegated procedures/apps can add another identity layer. [S06, S14] | Preserve requesting-user-to-service identity correlation. |
| A03 | U | Native history does not establish a reviewed lossless all-path guarantee. | Obtain scope/latency guarantees and reconcile under load/failure. |
| A04 | C | Historical effective entitlement requires grants, role memberships, policies, and identity changes. | Archive snapshots/deltas; reconstruct indirect access and policy versions. |
| A05 | C | Access History's 365-day window is not independent long-term WORM retention. [S06] | Export permitted evidence to a protected archive under records/SOC ownership. |
| A06 | C | Query/log/lineage content may expose clinical literals or identifying metadata. [S06, S17] | Limit evidence readers and use approved minimization/retention. |
| A07 | C | No independent SOC alerting/collector operation demonstrated. | Detect exports, shares, grants, integrations, audit changes, and collection lag. |
| A08 | C | Data/query history is evidence input, not a complete incident process. [S06] | Rehearse scope, containment, preservation, and counsel-led notification. |
| A09 | C | Required evidence-view access needs a least-privileged collection design. [S06] | Approve collector grants without routine ACCOUNTADMIN researcher access. |

### Resilience and delivery

| ID | Rating | Evidence / remaining gap | Required closure |
|---|---|---|---|
| R01 | C | Time Travel restores supported objects; Fail-safe is best effort/vendor-mediated. [S10, S11] | Protect configuration and independent recovery assets; test destructive scenarios. |
| R02 | C | Account-object failover/failback requires Business Critical; topology needs configuration. [S12] | Include clients, identities, keys, stages, applications, and external resources in full recovery. |
| R03 | C | Safe hospital/research contention behavior not measured. | Use appropriate workload separation and benchmark peak concurrency. |
| R04 | C | No approved hospital CI/CD, policy promotion, or regulated-release process supplied. | Version SQL/policies/configuration/models; implement approved tests and rollback. |
| R05 | C | Managed service does not maintain every hospital package/client/container. [S19] | Assign dependency, connector, model, and operational change ownership. |
| R06 | C | No clinical ingestion/analysis reconciliation supplied. | Validate identifiers, units, terminology, cohort membership, and results. |
| R07 | C | Historical and external/shared data complicate exit/disposition. [S10, S11, S20] | Test exports, policy migration, retention exceptions, and contract termination. |

### Commercial and operations

| ID | Rating | Evidence / remaining gap | Required closure |
|---|---|---|---|
| O01 | C | Business Critical, networking, history, replication, and AI add cost dimensions. [S01, S08, S10-S12] | Model full lifecycle/storage/egress/SOC/staffing costs, not only warehouse credits. |
| O02 | C | Resource monitors cover warehouses, not serverless/AI; budgets are separately needed. [S18] | Monitor each charge class and avoid unsafe hospital-workload suspension. |
| O03 | C | Actual study/environment/account/schema design not supplied. | Automate scoped grants, policy inheritance, evidence, and retirement. |
| O04 | C | Clean-room variants and feature availability differ; multi-collaborator experience includes preview. [S15] | Approve the released feature inventory and support lifecycle. |
| O05 | C | Managed SQL reduces some infrastructure tasks, not hospital governance/SOC work. | Fund policy, privacy, network, keys, research engineering, and evidence operation. |
| O06 | C | Required edition and integrations must be priced and institutionally approved. [S01, S07-S09] | Confirm BAA, tier, Azure endpoints/keys, IdP, and collector permissions. |
| O07 | C | No clinical/research workload or migration benchmark was run. | Compare realistic controlled SQL/ML/concurrency/recovery workloads and fully loaded cost. |

## 4. Material gap register and mitigations

Severity reflects **analyst risk judgment**, not findings from an inspected Snowflake account.

| Risk | Severity | Requirements | Mitigation and owner | Closure evidence |
|---|---|---|---|---|
| PHI used without appropriate contract/service eligibility | Critical approval blocker | L01-L02, L08-L09 | Legal/procurement confirms purchased edition, signed terms, and every service/region. | Executed BAA, service inventory, scoped confirmation. |
| Search results reveal a broader corpus than the caller is entitled to see | High | I05, G03, G07 | Data security/application owner scopes service/corpus and enforces trusted authorization. | Two-study RAG tests, denied-service tests, filter-bypass tests. |
| Inference processing crosses a prohibited geography/cloud boundary | High | L08, G07 | Platform/legal owner sets and monitors approved Cortex routing and model availability. | Effective parameter evidence and scoped processing confirmation. |
| Stage, export, integration, or shared results escape query policy | High | N02, G05, G08-G09 | Network/privacy teams restrict destinations and approve receiving-system controls. | Direct-file/export/share negative tests and recipient agreements. |
| Audit history is treated as complete, immutable, or sufficient for all incident evidence | High | A01-A05 | SOC/records teams validate supported event scope and independent archive/entitlement reconstruction. | Reconciled access suite, collection-failure alarms, historical access reconstruction. |
| Restore/failover excludes configuration, identities, integrations, or external data | High | R01-R02 | Resilience owner builds end-to-end recovery and dependency tests. | Timed restore/failover/failback, including keys and clients. |
| Warehouse-only budget limits miss serverless/AI charges or stop critical analytics | Medium | O01-O02 | FinOps/platform teams implement charge-class budgets and safe workload policies. | Stress-tested alerts/limits and service-continuity evidence. |

**Key distinction:** There is no demonstrated universal Snowflake product gap preventing regulated hospital use in this review. However, a native governed SQL path is not proof of a governed search, container, stage, or exported-data path. Most C rows are architectural/operating controls; U rows require stronger evidence, not optimistic scoring.

## 5. Research, recovery, and purchasing implications

Clean rooms can be useful for approved multi-party analyses and privacy-preserving configurations. They do not replace research authorization, a re-identification analysis, or approved output review. The reviewed introduction also distinguishes a newer multi-collaborator preview and lists deployment/region limits; do not assume one collaboration feature's maturity or coverage applies to all variants. [S15]

Time Travel normally starts with a one-day retention period; Enterprise-or-higher permanent objects can be configured up to 90 days, while temporary/transient objects differ. Fail-safe then provides a separate nonconfigurable seven-day recovery period where applicable and is not routine customer-controlled historical access. Neither mechanism is the hospital's full records archive or a guaranteed short RTO. [S10, S11]

**Choose conditionally** for governed warehouse analytics, controlled sharing, and an operating model comfortable with managed service controls. **Keep research/ML in the comparison** rather than rejecting it on an outdated SQL-only characterization. **Do not approve yet** where applicable legal scope, search authorization, all-path audit evidence, or recovery requirements remain unresolved.

## 6. Primary source register

Reviewed September 30, 2026. Regulatory source IDs use the REG prefix and resolve in `requirements-matrix.md`.

| ID | Official Snowflake documentation | Source location |
|---|---|---|
| S01 | Snowflake editions; Business Critical and BAA prerequisites | `https://docs.snowflake.com/en/user-guide/intro-editions` |
| S02 | Federated authentication and SSO | `https://docs.snowflake.com/en/user-guide/admin-security-fed-auth-overview` |
| S03 | Workload identity federation | `https://docs.snowflake.com/en/user-guide/workload-identity-federation` |
| S04 | Row access policies | `https://docs.snowflake.com/en/user-guide/security-row-intro` |
| S05 | Column-level security | `https://docs.snowflake.com/en/user-guide/security-column-intro` |
| S06 | ACCESS_HISTORY view | `https://docs.snowflake.com/en/sql-reference/account-usage/access_history` |
| S07 | Azure Private Link | `https://docs.snowflake.com/en/user-guide/privatelink-azure` |
| S08 | Private connectivity for outbound traffic | `https://docs.snowflake.com/en/user-guide/private-connectivity-outbound` |
| S09 | Tri-Secret Secure | `https://docs.snowflake.com/en/user-guide/security-encryption-tss` |
| S10 | Time Travel | `https://docs.snowflake.com/en/user-guide/data-time-travel` |
| S11 | Fail-safe | `https://docs.snowflake.com/en/user-guide/data-failsafe` |
| S12 | Account replication and failover | `https://docs.snowflake.com/en/user-guide/account-replication-intro` |
| S13 | Cortex cross-region inference | `https://docs.snowflake.com/en/user-guide/snowflake-cortex/cross-region-inference` |
| S14 | Cortex Search; owner's-rights model | `https://docs.snowflake.com/en/user-guide/snowflake-cortex/cortex-search/cortex-search-overview` |
| S15 | Snowflake Data Clean Rooms introduction | `https://docs.snowflake.com/en/user-guide/cleanrooms/introduction` |
| S16 | Sensitive data classification | `https://docs.snowflake.com/en/user-guide/classify-intro` |
| S17 | Data lineage | `https://docs.snowflake.com/en/user-guide/ui-snowsight-lineage` |
| S18 | Resource monitors | `https://docs.snowflake.com/en/user-guide/resource-monitors` |
| S19 | Snowflake ML overview | `https://docs.snowflake.com/en/developer-guide/snowflake-ml/overview` |
| S20 | Secure Data Sharing | `https://docs.snowflake.com/en/user-guide/data-sharing-intro` |

**Evidence limitations:** No hospital Snowflake account, signed terms, scoped third-party reports, clinical validation, billing quote, private-network workflow, or recovery exercise was inspected. The individual AI/ML/search/collaboration feature's PHI eligibility is intentionally not inferred from general Business Critical availability.
