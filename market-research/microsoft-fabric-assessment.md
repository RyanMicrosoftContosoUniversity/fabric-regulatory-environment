# Microsoft Fabric: hospital and research security/compliance assessment

**As of:** September 30, 2026.  
**Requirements:** `requirements-matrix.md`, all 60 IDs. Its definitions and regulatory source register apply here.  
**Disposition:** **Conditional shortlist** for Microsoft-centered analytics and BI; **redesign required** for the evaluated strict workspace-private, all-artifact-CMK profile. Not authorized for production PHI by this assessment.

## 1. Scope and overall finding

The baseline is a commercial tenant with reviewed US home/capacity regions, suitable capacity, Entra identity, OneLake security, relevant Purview controls/licenses, and workspace-private inbound restrictions for protected engineering data.

For the N08 gap, the **strict policy variant** requires every PHI-bearing workspace, including the BI serving workspace, to use workspace-level inbound restrictions. For N05, the variant requires customer-controlled keys for every policy-designated PHI artifact, including applicable BI artifacts. These are hospital policy assumptions, **not universal HIPAA requirements**.

Fabric's strengths include integrated engineering/warehouse/BI experiences, Entra authentication, encryption, OneLake policy controls, Microsoft governance integrations, and emerging cross-workload diagnostics. The key limitation is that **security capabilities are not uniform across item types, network modes, identities, and audit sources**. A secure lakehouse does not automatically establish equivalent protection for a semantic model, an exported file, a SQL endpoint, or a delegated shortcut. [F01, F05-F08, F13-F14]

The Microsoft HIPAA overview describes the BAA framework and lists Power BI, but it does not independently establish the scope of every Fabric workload and preview. The hospital must obtain the actual applicable contractual/service inventory. Absence of the Fabric name from that overview is **not proof that Fabric is excluded**, just as a general Microsoft compliance claim is not proof that every feature is covered. [REG02]

**Analyst conclusion:** Viable when the hospital accepts a documented architecture with explicitly governed serving paths. Do not approve a design that depends on the three identified G ratings while claiming that Private Link, CMK, or SQL auditing applies uniformly to the whole solution.

## 2. Current strengths and material limitations

| Dimension | Documented strength | Hospital limitation / implication |
|---|---|---|
| Core security | Entra-authenticated interactions and encryption at rest/in transit. [F01] | Hospital access, clinical privacy, endpoint, and operating controls remain necessary. |
| OneLake governance | The update archive records OneLake security/data-access-role GA in **May 2026**. [F21] | Confirm item/engine-specific support; GA of the framework is not proof that all paths enforce all policies. |
| Customer keys | Workspace CMK was announced GA in **November 2025**. [F21] | Coverage is item-specific; the all-artifact policy remains a gap for BI scenarios. [F04, F22] |
| Private engineering access | Workspace Private Link supports many engineering and warehouse items. [F02] | Semantic models, deployment pipelines, and SQL database workspace-private scenarios have documented exclusions. |
| Outbound controls | OAP provides default blocking with workload-specific approved exceptions. [F03] | Validate the actual item/connector/rule mechanism, support status, and diagnostics interactions. |
| Governance integration | Purview information protection and DLP provide supported policy/enforcement paths. [F13, F14] | Labels alone are not a complete all-engine authorization or all-export guarantee. |
| Monitoring | Current workspace monitoring supports Private Links and configurable retention. [F11] | Legacy monitoring differs; independent security alerting and evidence retention still need design. |
| Healthcare delivery | Official healthcare solution documentation and a customer-managed source package exist. [F20] | Managed-solution deployment eligibility changes October 1, 2026; support ends December 31, 2027. |

### The three baseline product/design gaps

**N08 - Strict workspace-private BI coexistence:** Semantic models are not supported in workspace-private workspaces. Native deployment pipelines also have an inbound-restriction limitation. The strict variant cannot place all required BI and delivery components behind that workspace-level boundary unchanged. Alternatives include a separately reviewed tenant-private design, a permitted serving boundary, or de-identified/aggregated BI. Each changes the evaluated architecture and needs fresh evidence. [F02, F17]

**N05 - Uniform customer-controlled-key coverage:** Workspace CMK accepts only supported item types and cannot be enabled in a workspace containing unsupported items. Power BI's separate BYOK capability covers supported imported semantic-model data, not every BI artifact or mode. Therefore, an all-policy-designated-artifact CMK claim cannot be made for the evaluated BI solution. This is not a claim that Fabric lacks CMK or Power BI lacks any customer-key option. [F04, F22]

**A03 - Guaranteed lossless SQL audit:** SQL auditing prioritizes database availability/performance and can allow transactions without recording all configured events under high activity/network load. That is a documented mismatch to the strict lossless-audit requirement. Additional telemetry can improve coverage but does not turn this feature into a documented lossless guarantee. HIPAA audit-control compliance requires a contextual assessment; this gap does not by itself establish that every Fabric deployment violates HIPAA. [F09, REG01]

## 3. Complete requirements gap analysis

**M:** documented native capability; **C:** material conditions; **G:** documented baseline gap; **U:** insufficient evidence.

### Legal and research

| ID | Rating | Evidence / remaining gap | Required closure |
|---|---|---|---|
| L01 | C | Microsoft BAA framework exists; hospital agreement not supplied. [REG02] | Counsel confirms effective terms and covered processing relationship. |
| L02 | U | Exact Fabric item/feature/region/preview/AI eligibility not established by general compliance pages. [F01, REG02] | Obtain written scope for every PHI-bearing service and dependency. |
| L03 | U | No current scoped hospital-required assurance artifacts reviewed. | Review reports, exceptions, bridge letters, and any applicable government authorization. |
| L04 | C | SaaS security overview is not the hospital risk assessment. [F01, REG01] | Allocate Microsoft, tenant, capacity, workspace, SOC, and research responsibilities. |
| L05 | C | No deployed Part 2 or other category-specific legal workflow assessed. [REG03] | Define applicable records/purposes and enforce approved study/data policies. |
| L06 | C | OneLake/BI permissions do not create research authority. [F05-F08, REG06] | Tie study access to approved investigators, purposes, and agreements. |
| L07 | C | No validated electronic-record/signature application supplied. [REG05] | Establish applicability and retain workflow-specific validation and signatures. |
| L08 | C | Home region, capacity, replication, metadata, and Copilot processing require review. [F16, F19] | Approve the entire geographic processing/storage map. |
| L09 | U | Executed incident, notification, audit-right, and supplier terms unavailable. | Counsel approves obligations, evidence rights, and escalation. |
| L10 | C | No approved dataset/log/copy/hold/disposition schedules deployed. | Define schedules for OneLake, BI, exports, diagnostics, replicas, and research records. |

### Identity and study boundaries

| ID | Rating | Evidence / remaining gap | Required closure |
|---|---|---|---|
| I01 | M | Entra authentication and Conditional Access are documented. [F01, F12] | Test required downstream resources, guests, clients, and emergency paths. |
| I02 | C | Nonuser/workspace/service identities have item-specific supported paths. [F06, F08] | Inventory credentials and choose supported least-privileged short-lived identities. |
| I03 | C | Admin/Member/Contributor roles have broad data access beyond restricted-reader rules. [F05] | Keep restricted researchers out of elevated source-workspace roles. |
| I04 | C | Workspace/item boundaries and policy inheritance require explicit design. [F05, F06] | Test study/environment isolation, enumeration, shortcuts, and broad group grants. |
| I05 | C | Engine support, SQL identity mode, and Direct Lake variant change enforcement. [F06-F08] | Validate SQL/Spark/BI/API/storage/shortcut/third-party paths independently. |
| I06 | C | ReadWrite roles cannot include RLS/CLS constraints. [F06] | Separate controlled writers from readers with restricted cohort access. |
| I07 | C | Central tenant/capacity/workspace and vendor-support privilege controls unverified. | Implement time-bound elevation and independently retained review evidence. |
| I08 | C | Conditional Access has no Fabric CAE support. [F12] | Measure study/guest revocation across groups, tokens, sessions, and caches. |

### Network, encryption, and secrets

| ID | Rating | Evidence / remaining gap | Required closure |
|---|---|---|---|
| N01 | C | Workspace-private supported paths differ from tenant/admin API scope. [F02] | Test required endpoints and review publicly reachable control-plane operations separately. |
| N02 | C | OAP uses workload-specific private endpoints or connection rules. [F03] | Validate supported items/rules and deny unapproved outbound destinations. |
| N03 | C | Private SQL connection strings and gateway/cross-workspace designs are documented. [F02, F18] | Prove the specific EHR/Databricks/partner client, DNS, and identity combination. |
| N04 | M | Default encryption in transit/at rest is documented. [F01] | Verify exported, gateway, endpoint, and external-source boundaries. |
| N05 | G | Workspace CMK and separate Power BI BYOK do not establish all-artifact coverage. [F04, F22] | Redesign data/artifact scope or obtain an approved policy exception where permissible. |
| N06 | C | CMK key rotation/revocation has defined operational dependencies. [F04] | Test the actual key lifecycle and recovery; coordinate Key Vault ownership. |
| N07 | C | Metadata/log/SQL/notebook/secret hygiene was not assessed. [F06, F09] | Avoid sensitive names/literals; protect traces, source control, and outputs. |
| N08 | G | Required BI cannot remain in the strict workspace-private baseline unchanged. [F02, F17] | Review tenant-private/serving alternatives and retest every applicable gate. |

### Governance and research workflows

| ID | Rating | Evidence / remaining gap | Required closure |
|---|---|---|---|
| G01 | C | Purview labels/DLP and supported enforcement paths exist. [F13, F14] | Validate clinical/genomic/free-text detection, ownership, licenses, and coverage. |
| G02 | C | Workspace lineage is documented; historical released-study provenance is broader. [F15] | Archive input/code/policy/consent versions and approved analysis releases. |
| G03 | C | Row/column policies are not an automatic consent/expiry application. [F06] | Implement trusted study eligibility and approved derived-copy disposition. |
| G04 | C | Platform policy controls are not a de-identification determination. [REG04] | Validate free text/images/genomic linkage and legally approved releases. |
| G05 | C | Sharing/shortcut/identity and private-network limitations are design-dependent. [F02, F06] | Approve recipients and enforce legal, identity, copy, and revocation controls. |
| G06 | C | Notebook/BI security is not a full researcher enclave/output-release process. | Add endpoint restrictions, approved packages, export controls, and review. |
| G07 | C | Copilot privacy commitments do not prove every AI feature's PHI eligibility. [F19] | Review scope, grounding, geography, agents/tools, prompts, traces, and outputs. |
| G08 | C | Unsupported filtering paths can be blocked rather than transparently filtered. [F06] | Validate actual external-engine access and preserve equivalent policy when exporting. |
| G09 | C | Purview/BI protections apply to supported enforcement/export paths. [F13, F14] | Test CSV/Excel/PBIX, subscriptions, embedded access, and client caches. |
| G10 | C | Healthcare accelerator availability/support is changing. [F20] | Validate supported customer-managed integration, semantics, terminology, and maintenance. |
| G11 | C | No clinical intended-use/model-safety validation evidence supplied. | Separate research-only from clinical use and require application/model approval. |

### Audit and incident evidence

| ID | Rating | Evidence / remaining gap | Required closure |
|---|---|---|---|
| A01 | C | SQL, OneLake, engine, and tenant audit sources have different scopes. [F09-F11, F23] | Reconcile all PHI paths, including Direct Lake and direct-file access. |
| A02 | C | Delegated endpoint/shortcut identities can differ from the requesting user. [F06-F08] | Preserve engine-to-storage/user correlation or choose supported end-user identity paths. |
| A03 | G | SQL audit can omit configured events under high activity/load. [F09] | Do not claim lossless evidence; redesign or resolve the policy requirement explicitly. |
| A04 | C | Permission-change events exist; complete historical effective entitlement needs more. [F09, F23] | Retain grant/group/ownership/policy snapshots and change streams. |
| A05 | C | OneLake diagnostic immutability does not prevent workspace/lakehouse deletion. [F10] | Protect an independent archive and test privileged deletion/collector failure. |
| A06 | C | Audit-folder access can be broad and query statements can contain PHI. [F09] | Restrict log readers, minimize literals, and protect copied evidence. |
| A07 | C | Current monitoring and alert/report behavior differ under capacity throttling. [F11] | Maintain independent SOC alerting and collection-health signals. |
| A08 | C | Multiple log planes and delegated identities complicate incident reconstruction. [F09-F10, F23] | Rehearse correlated evidence preservation, containment, and notification. |
| A09 | C | Tenant audit access requires central permissions; item SQL audit has separate controls. [F09, F23] | Obtain approved collectors/evidence delivery rather than assuming workspace admin is sufficient. |

### Resilience and delivery

| ID | Rating | Evidence / remaining gap | Required closure |
|---|---|---|---|
| R01 | C | Native resilience/replication is not a tested independent recovery program. [F16] | Protect code, policy, data, identity, and audit recovery assets. |
| R02 | C | Regional failover can leave item experiences unavailable/read-only; OneLake scope is limited. [F16] | Measure complete processing/write recovery with workload-specific dependencies. |
| R03 | C | Shared capacity and throttling can affect required workloads/alerts. [F11] | Isolate appropriate capacity/workloads and test realistic concurrency/failure. |
| R04 | C | Native deployment pipelines cannot serve inbound-restricted workspaces unchanged. [F17] | Use approved Git/API-based delivery and retain negative tests/validation/rollback. |
| R05 | C | SaaS patching does not maintain every client, gateway, notebook dependency, or package. [F01] | Assign maintenance ownership and approved supported configurations. |
| R06 | C | Healthcare solution availability is not hospital data-integrity validation. [F20] | Reconcile clinical identifiers, terminology, units, cohorts, and outputs. |
| R07 | C | OneLake/BI/diagnostics/replicas create multiple exit/disposition surfaces. [F10, F16] | Test usable export, policy migration, retention exceptions, and termination. |

### Commercial and operations

| ID | Rating | Evidence / remaining gap | Required closure |
|---|---|---|---|
| O01 | C | Capacity, monitoring, replicas, gateways, governance licenses, and staff add costs. [F11, F13-F14, F16] | Cost the full approved architecture, including separate serving/audit boundaries. |
| O02 | C | Capacity controls/alerts have availability consequences. [F11] | Implement safe budgets with independent notification and hospital workload priority. |
| O03 | C | Study/environment/workspace/capacity footprint is design-dependent. [F05] | Estimate actual isolated deployment counts and automate lifecycle/policy tests. |
| O04 | C | Managed healthcare solution has a published deployment transition/end-of-support. [F20] | Avoid an unfunded retirement-bound dependency; fund customer-managed support. |
| O05 | C | Microsoft integration still needs central identity/Purview/SOC and workspace ownership. [F12-F14, F23] | Fund named control owners and support processes. |
| O06 | C | Appropriate licenses, tenant settings, keys, network approvals, and audit rights are required. [F04, F13-F14, F23] | Obtain central-team commitments before treating controls as deployable. |
| O07 | C | No controlled hospital/research benchmark was run. | Compare actual architecture, concurrency, latency, recovery, migration effort, and total cost. |

## 4. Identity and audit architecture decisions

### Choose authorization paths deliberately

The SQL analytics endpoint offers **user identity mode** for OneLake-governed reads and **delegated identity mode** for SQL-managed authorization; the reviewed documentation says new endpoints start delegated. Switching a mode is an architecture decision, not a cosmetic setting. [F07]

For a hospital relying on OneLake cohort policies, prefer a supported end-user-authorized path and test it. Direct Lake on OneLake and Direct Lake on SQL analytics endpoints differ. Fixed identities can be legitimate, but require correctly scoped source credentials, semantic-model policies, and a correlation strategy. Model-only RLS/OLS must not be used as proof of protection on other read paths. [F08]

Review `DefaultReader`, broad group memberships, ownership, and elevated roles. A restricted-reader policy cannot serve as the safety boundary for a researcher who also has broad write/administrative authority. This is a hospital design inference from the documented permission model, not a claim that no research notebook design can be secured. [F05, F06]

### Build multiple evidence planes, not a SQL-only trail

OneLake diagnostics records direct UI/API operations and temporary access for internal workloads, which requires engine-log correlation. Under OAP, cross-workspace diagnostic delivery is not supported; a same-workspace lakehouse is required. Design the independent evidence archive accordingly rather than assuming centralized diagnostics streaming will work unchanged. [F10]

SQL audit is off by default. Queries that read through Direct Lake without submitting SQL cannot be assumed to appear in a SQL-only audit trail; fallback SQL paths are different. Combine appropriate SQL, semantic-model/engine, OneLake, Entra, and tenant activity evidence. **Analyst implication:** object-access and storage-permission events do not necessarily enumerate every patient record a user received. [F08, F09]

Current workspace monitoring can use Private Links, and its 30-day retention is a **default**, not a universal immutable maximum. Monitoring Eventhouse/ingestion/dashboard behavior under throttling differs from Power BI report/Activator behavior. The hospital should keep security notifications independent of analytics availability. [F11]

## 5. Material gap register and mitigations

Severity is **analyst prioritization**, not findings from a tested hospital tenant.

| Risk | Severity | Requirements | Mitigation and owner | Closure evidence |
|---|---|---|---|---|
| Contract scope does not establish all selected Fabric/AI processing eligibility | Critical approval blocker | L01-L02, L08-L09 | Legal/procurement obtains an exact service/feature/region inventory and executed terms. | Scoped confirmation and approved inventory. |
| Strict network profile cannot accommodate required BI/delivery unchanged | High | N01, N08, R04 | Architecture/network teams review tenant-private or alternate serving/delivery designs. | Full private-workflow demonstration; no prohibited public PHI path. |
| All-artifact CMK policy is claimed despite unsupported artifacts | High | N05-N06 | Security/data owners scope PHI artifacts and applicable key controls; redesign or legally permissible policy exception. | Artifact/key map, recovery exercise, approved residual-risk record. |
| Broad researcher roles or delegated identities undermine cohort restrictions | High | I03-I06, A02 | Data security separates writers/readers, selects supported identity modes, and tests alternate paths. | Two-study SQL/Spark/BI/API/shortcut negative tests. |
| SQL-only, best-effort, or mutable evidence is represented as complete | High | A01, A03-A05 | SOC/records teams implement supported multi-plane evidence, reconciliation, and independent retention. | Reconciled access suite and collector/deletion/failure tests. |
| Recovery restores files/read access but not clinical/research processing | High | R01-R03 | Resilience owner includes every workload, code/configuration, keys, clients, and source dependency. | Timed full restore and write-processing recovery. |
| Central-tenant dependencies are assumed but not granted | High | I01, A09, O06 | Tenant security approves identity, Purview, audit, key, and integration responsibilities. | Named owners and formally approved permissions/evidence-delivery process. |
| Managed healthcare accelerator lifecycle is ignored | Medium | G10, O04 | Platform/procurement plans the documented transition and customer-managed maintenance. | Supported integration and funded transition/support plan. |

## 6. Verification of older working assumptions

Generic working notes raise useful assessment questions, but product claims need current public evidence and proposed controls need configuration-specific validation. This comparison does not report customer-specific architecture or institutional approval decisions.

| Working assumption | Current assessment |
|---|---|
| OneLake security is entirely preview | **Outdated blanket characterization:** GA announcement is recorded for May 2026. Engine/feature-specific restrictions still require review. [F21] |
| Workspace CMK does not exist / is entirely preview | **Outdated blanket characterization:** workspace CMK GA is recorded in November 2025. Supported-item and Power BI scope limits still matter. [F21, F04, F22] |
| Workspace monitoring cannot use Private Link and is capped permanently at 30 days | **Applies differently to legacy monitoring:** current monitoring documents Private Link support and adjustable retention. [F11] |
| There is no private SQL hostname for external clients | **Too broad:** workspace-private SQL connection strings/FQDNs are documented. A particular Databricks connector/authentication workflow still needs testing. [F02] |
| No permission history exists anywhere | **Too broad:** SQL permission-change audit groups and tenant activity exist. They are not an automatic complete effective-entitlement timeline. [F09, F23] |
| Microsoft has no official healthcare Fabric solution material | **Too broad:** official healthcare documentation and a source package exist; neither proves this hospital architecture's compliance. [F20] |
| Private Link, domain organization, labels, and BI RLS together prove end-to-end protection | **Not established:** authorization, key, network, export, and evidence paths must each be demonstrated. [F02, F05-F08, F13-F14] |
| Every preview is automatically outside every BAA | **Not a safe universal rule:** verify the actual contractual/feature terms. This review gives no blanket PHI approval to previews. |

**Lifecycle dates:** The managed healthcare solution's new-customer deployment restriction starts **October 1, 2026**, immediately after this assessment date. Its support ends **December 31, 2027**. A customer-managed source/documentation package became available **August 3, 2026**. This is a transition of the managed healthcare solution, **not a retirement of Microsoft Fabric itself**. [F20]

## 7. Selection recommendation

**Choose conditionally** when integrated Microsoft analytics/BI is important and the institution can approve a workload-specific security architecture. For raw PHI, control the lake and every serving/shortcut/API path independently; consider approved de-identified or aggregated serving datasets where they meet the use case.

**Do not select the strict baseline unchanged** if all PHI workspaces must support workspace-level private isolation, every designated artifact must have customer-controlled keys, and the required audit trail must be provably lossless. The documented G rows require architectural/policy resolution, not a favorable feature average. Alternative designs can change ratings, but only after legal scope, identity, private networking, keys, audit, and recovery have been demonstrated together.

## 8. Primary source register

Reviewed September 30, 2026. Regulatory source IDs use the REG prefix and resolve in `requirements-matrix.md`. Several reviewed pages were updated September 29, 2026; this is a documentation timestamp, not an inferred feature release date.

| ID | Official Microsoft documentation | Source location |
|---|---|---|
| F01 | Security in Microsoft Fabric | `https://learn.microsoft.com/en-us/fabric/security/security-overview` |
| F02 | Supported workspace-private-link scenarios | `https://learn.microsoft.com/en-us/fabric/security/security-workspace-level-private-links-support` |
| F03 | Workspace outbound access protection | `https://learn.microsoft.com/en-us/fabric/security/workspace-outbound-access-protection-overview` |
| F04 | Customer-managed keys for Fabric workspaces | `https://learn.microsoft.com/en-us/fabric/security/workspace-customer-managed-keys` |
| F05 | OneLake data security overview | `https://learn.microsoft.com/en-us/fabric/onelake/security/get-started-security` |
| F06 | OneLake security roles, permissions, and scopes | `https://learn.microsoft.com/en-us/fabric/onelake/security/data-access-control-model` |
| F07 | OneLake security for SQL analytics endpoints | `https://learn.microsoft.com/en-us/fabric/onelake/security/sql-analytics-endpoint-onelake-security` |
| F08 | Integrate Direct Lake security | `https://learn.microsoft.com/en-us/fabric/fundamentals/direct-lake-security-integration` |
| F09 | SQL audit logs in Fabric Data Warehouse | `https://learn.microsoft.com/en-us/fabric/data-warehouse/sql-audit-logs` |
| F10 | OneLake diagnostics | `https://learn.microsoft.com/en-us/fabric/onelake/onelake-diagnostics-overview` |
| F11 | Workspace monitoring overview; current and legacy | `https://learn.microsoft.com/en-us/fabric/fundamentals/workspace-monitoring-overview` |
| F12 | Conditional Access in Fabric | `https://learn.microsoft.com/en-us/fabric/security/security-conditional-access` |
| F13 | Information protection in Fabric | `https://learn.microsoft.com/en-us/fabric/governance/information-protection` |
| F14 | DLP for Fabric and Power BI | `https://learn.microsoft.com/en-us/purview/dlp-powerbi-get-started` |
| F15 | Lineage in Fabric | `https://learn.microsoft.com/en-us/fabric/governance/lineage` |
| F16 | Reliability in Fabric | `https://learn.microsoft.com/en-us/fabric/security/reliability-fabric` |
| F17 | CI/CD network security | `https://learn.microsoft.com/en-us/fabric/cicd/cicd-security` |
| F18 | Cross-workspace communication | `https://learn.microsoft.com/en-us/fabric/security/security-cross-workspace-communication` |
| F19 | Copilot privacy, security, and responsible AI | `https://learn.microsoft.com/en-us/fabric/fundamentals/copilot-privacy-security` |
| F20 | Healthcare data solutions in Microsoft Fabric | `https://learn.microsoft.com/en-us/industry/healthcare/healthcare-data-solutions/overview` |
| F21 | Fabric update archive; feature GA announcements | `https://learn.microsoft.com/en-us/fabric/fundamentals/whats-new-archive` |
| F22 | Power BI bring-your-own-key scope | `https://learn.microsoft.com/en-us/fabric/enterprise/powerbi/service-encryption-byok` |
| F23 | Track user activities and tenant audit access | `https://learn.microsoft.com/en-us/fabric/admin/track-user-activities` |

**Evidence limitations:** No hospital tenant, BAA, assurance reports, private-network proof-of-concept, institutional privileges, key configuration, clinical validation, or recovery exercise was inspected. Public documentation supports the described constraints but cannot close the hospital's production gates. Recheck each selected item/engine and contract at procurement and after material changes.
