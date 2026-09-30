# Azure Databricks in a hospital: HIPAA/HITECH

**Research date:** September 30, 2026.

Start with [HIPAA/HITECH requirements and implementation](HIPAA-HITECH-Azure-Databricks.md).

The report contains a requirement-by-requirement matrix covering Security Rule safeguards, Privacy Rule obligations and patient rights, disclosure permissions, breach notification, HITECH changes, and applicable electronic transaction requirements. Each matrix maps the obligation to an Azure Databricks implementation, accountable hospital roles, and evidence to retain.

It also includes a recommended hospital architecture, platform prerequisites, known limitations, deadline tables, a production-readiness checklist, and primary-source references.

**Principal finding:** Azure Databricks can support a HIPAA-regulated hospital platform, but it does not make the hospital compliant by itself. The design needs the appropriate contractual coverage and HIPAA compliance security profile, properly configured Azure and Unity Catalog controls, and hospital-owned privacy, security, patient-rights, and incident-response processes.

**Status:** Research and proposed design only. No hospital environment was inspected or configured, and no compliance certification or legal determination is asserted.
