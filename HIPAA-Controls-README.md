- **HIPAA Controls**:

  | HIPAA Requirement | Fabric Implementation Steps | Reference |
  |-------------------|------------------------------|-----------|
  | Data Encrypted at Rest | - Fabric encrypts all data at rest using AES-256 by default<br>- Optionally configure Customer Managed Keys (CMK) via Azure Key Vault for Fabric workspaces | https://learn.microsoft.com/en-us/fabric/security/security-fundamentals#encryption-at-rest |
  | Data encrypted in Transit | - All data transmitted between clients and Fabric services uses TLS 1.2+ <br>- Validate HTTPS endpoints for APIs and Connectors | https://learn.microsoft.com/en-us/fabric/security/security-fundamentals#encryption-in-transit
  | Unique User Identification | - All access authenticated via Microsoft Entra ID <br>- Enforce MFA and Conditional Access <br>- Assign least-privilege roles in Fabric Workspaces |https://learn.microsoft.com/en-us/fabric/security/security-fundamentals#identity-and-access-management
  | Audit Controls | - Enable Fabric Audit Logs in Admin Portal <br>- Configure SQL Endpoint Auditing for warehouses/lakehouses <br>- Forward logs to Microsoft Sentinel or SIEM for monitoring | https://learn.microsoft.com/en-us/purview/audit-solutions-overview
  | Integrity Controls | - Use OneLake redundancy and encryption <br>- Enable audit trails for all data changes <br>- Consider Purview Data Catalog for lineage tracking | https://learn.microsoft.com/en-us/fabric/security/security-fundamentals
  | Person/Entity Authentication | - Require MFA for all users via Azure AD <br>- Use Conditional Access to restrict access to compliant devices <br>- Enable passwordless or certificate-based authentication for stronger assurance | <placeholder>
  | Risk analysis and management | - Use Microsoft Defender for Cloud and Compliance Manager HIPAA Template <br>- Review Fabric Security posture and audit logs regularly | https://learn.microsoft.com/en-us/azure/security/fundamentals/physical-security
  | Information Access Management | - Apply RBAC in Fabric Workspaces <br>- Use Purview Sensitivity Labels for PHI datasets <br>- Implement DLP Policies for Fabric in Purview | <placeholder>
  | Workforce Security | - Manage access via Entra AD Groups <br>- Apply Conditional Access for device compliance <br>- Disable accounts immediately upon termination | https://learn.microsoft.com/en-us/azure/active-directory/conditional-access/overview
  | Security Incident Procedures | - Enable Microsoft Sentinel for incident defection <br>- Use Fabric audit logs for forensic analysis <br>- Follow HIPAA breach notification requirements | https://learn.microsoft.com/en-us/azure/sentinel/overview
  | Contingency Plan | - Use OneLake redundancy and export backups via APIs <br>- Consider multi-geo for disaster recovery <br>- Maintain break-glass admin accounts in Azure AD | <placeholder>
  | Facility Access Controls | - This is Microsoft Managed Infrastructure <br>- Covered by Azure Datacenter security (multi-layer physical controls) <br>- Review SOC 2 and ISO reports in Service Trust Portal | https://learn.microsoft.com/en-us/azure/security/fundamentals/physical-security
  | Workstation Security | - Enforce Intune Device compliance for Fabric access <br>- Apply Conditional Access to block unmanaged devices <br>- Use DLP to prevent PHI export to local drives | https://learn.microsoft.com/en-us/mem/intune/fundamentals/what-is-intune
  | Device/Media Controls | - Microsoft handles secure disposal of hardware per NIST 800-88 <br>- Avoid downloading PHI to portable media <br>- If necessary, encrypt and track any exported data | https://learn.microsoft.com/en-us/azure/security/fundamentals/physical-security#hardware-destruction


