Last Updated: March 2024

- [ALZ Bicep](https://learn.microsoft.com/en-us/azure/architecture/landing-zones/bicep/landing-zone-bicep)
- [ALZ - Terraform](https://learn.microsoft.com/en-us/azure/architecture/landing-zones/terraform/landing-zone-terraform)
- [Review Checklist](https://github.com/Azure/review-checklists)

# [Design Areas](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/landing-zone/design-areas)

## 1. [Billing and Microsoft Entra Tenant](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/landing-zone/design-area/azure-billing-ad-tenant)

## 2. [Identity and access management](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/landing-zone/design-area/identity-access)
- [Glossary of updated terminology](https://learn.microsoft.com/en-us/entra/fundamentals/new-name#glossary-of-updated-terminology):
  - Azure Active Directory Free =	Microsoft Entra ID Free
  - Azure Active Directory Premium P1	= Microsoft Entra ID P1
  - Azure Active Directory Premium P2	= Microsoft Entra ID P2
  - Azure Active Directory for education = Microsoft Entra ID for education
  - Azure Active Directory Premium P1	= Microsoft Entra ID P1
  - Azure Active Directory Premium P1 for students = Microsoft Entra ID P1 for students
  - Azure Active Directory Premium P1 for faculty =	Microsoft Entra ID P1 for faculty
  - Azure Active Directory Premium P1 for government = Microsoft Entra ID P1 for government
  - Azure Active Directory Premium P2	= Microsoft Entra ID P2
  - Azure Active Directory Premium P2 for students = Microsoft Entra ID P2 for students
  - Azure Active Directory Premium P2 for faculty = Microsoft Entra ID P2 for faculty
  - Azure Active Directory Premium P2 for government = Microsoft Entra ID P2 for government
  - Azure Active Directory F2 =	Microsoft Entra ID F2
  - Azure AD tenant = Microsoft Entra tenant
  - Azure AD account = Microsoft Entra account
- User Types:
  - Cloud Identity
  - Directory-synchronized Identities
    - [Azure AD Connect Sync (now Microsoft Entra Connect Sync)](https://learn.microsoft.com/en-us/entra/identity/hybrid/connect/whatis-azure-ad-connect-v2)
    - [Azure AD Cloud Sync (now Microsoft Entra Cloud Sync)](https://learn.microsoft.com/en-us/entra/identity/hybrid/cloud-sync/what-is-cloud-sync)
      - Provisioning from AD to Microsoft Entra ID is orchestrated in Microsoft Online Services.
      - Organization only needs to deploy, in on-premises or IaaS-hosted environment, a light-weight agent (acts as a bridge between Microsoft Entra ID and AD)
      - Provisioning configuration is stored in Microsoft Entra ID and managed as part of the service.
      - [Comparison with Microsoft Entra Connect Sync](https://learn.microsoft.com/en-us/entra/identity/hybrid/cloud-sync/what-is-cloud-sync#comparison-between-microsoft-entra-connect-and-cloud-sync)
  - Guest Users (Invited via known email, needs to create an associated Microsoft account (MSA) if their email address isn't associated with one)
- Bulk Management
  -  Add users to Microsoft Entra ID programmatically using (if you're sharing the same directory):
    -  Microsoft Graph API
    -  Microsoft 365 Admin Center
    -  Microsoft Intune Admin console
    -  New-MgUser PowerShell command 
- Groups:
  - Static
  - Dynamic
  - No nested groups
- RBAC
  - Built-in roles e.g. Owner, Contributor and Reader
  - Role definitions in Json = Properties + Permissions (Actions/NotActions and DataActions/NotDataActions) + Scope
  - Custom Roles

## 3. [Resource Organization](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/landing-zone/design-area/resource-org)

## 4. [Network topology and connectivity](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/landing-zone/design-area/network-topology-and-connectivity)

### VNET
-  A NAT gateway is the recommended way to provide outbound connectivity for VMs in the subnet.
  -  Subnet creation now offers the ability to disable default outbound access (by selecting to create a 'private subnet') Note: This setting can't be changed after the subnet is created.

## 5. [Security](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/landing-zone/design-area/security)

### Defender for Cloud
- [Youtube](https://www.youtube.com/playlist?list=PL3ZTgFEc7LysiX4PfHhdJPR7S8mGO14YS)
- [Community Repo](https://github.com/Azure/Microsoft-Defender-for-Cloud)
- Defender for Containers has a new version of the defender agent that now uses Inspektor Gadget instead of Sysdig, available now! Also, Defender for Container’s AKS threat detection features are now fully supported in all cloud environments. Finally, Open Container Initiative image formation specification is now supported by MDVM for AWS, Azure and GCP clouds.
- Log analytics agent, also known as Microsoft Monitoring Agent (MMA) is retiring in August 2024.
The MDC plans that require MMA, Defender for Servers, and Defender for SQL on machines, are updated. Customers will need to migrate to AMA where applicable.
Most Defender for Servers P2 features are available through the Defender for Endpoint integration or agentless scanning. The rest of the features will be in GA by MMA’s retirement or will be deprecated.
Defender for SQL on machines will require AMA and can be seamlessly migrated through the auto-provisioning process.
[DfC: Prepare for retirement of the Log Analytics agent](https://learn.microsoft.com/en-us/azure/defender-for-cloud/prepare-deprecation-log-analytics-mma-agent)

## 6. [Management](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/landing-zone/design-area/management)

### Monitor
- Azure Monitor Workspace vs. Log Analytics Workspace
  - [Log Analytics workspaces contain logs and metrics data from multiple Azure resources, whereas Azure Monitor workspaces currently contain only metrics related to Prometheus](https://learn.microsoft.com/en-Us/azure/azure-monitor/essentials/azure-monitor-workspace-overview)

### Storage
- [Public Preview: Azure Storage Actions – Streamline management of storage objects - 20 Feb 2024](https://microsoft.seismic.com/app?ContentId=ea9923b6-05ca-4efd-ab9a-b59c6a0963af#/doccenter/a5266a70-9230-4c1e-a553-c5bddcb7a896/doc/%252Fdde0caec0e-9236-f21b-2991-5868e63d3984%252FdfYTZjNDRiZDMtMzEwZS1kNWZkLTNjOGEtNjliYWJjMjhmMmUw%252CPT0%253D%252CUHJldmlldw%253D%253D%252Flf6905c880-7e7b-4627-8f89-0bd0d83cb71a/grid/?anchorId=78ee1704-9bf5-4767-8218-25b82752055b)
  - Automate data protection, tagging, and cost optimization operations for billions of objects in Azure storage accounts with an interactive no-code experience, without provisioning or managing any infrastructure.
  - [Azure Storage Actions](https://learn.microsoft.com/en-us/azure/storage-actions/storage-tasks/)

### Update Management
- [Azure Update Manager](https://learn.microsoft.com/en-us/azure/update-manager/)
- [Move from Automation Update Management to Azure Update Manager](https://learn.microsoft.com/en-us/azure/update-manager/guidance-migration-automation-update-management-azure-update-manager?tabs=update-mgmt#azure-portal-experience-preview)
  - Portal experience in preview

## 7. [Governance](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/landing-zone/design-area/governance)

### Policies
- [Azure Policy built-in policy definitions](https://learn.microsoft.com/en-us/azure/governance/policy/samples/built-in-policies)
- [Enterprise Azure Policy as Code (EPAC)](https://azure.github.io/enterprise-azure-policy-as-code/)

### Cost Management
- [Azure Reservation auto-renew opt-in](https://learn.microsoft.com/en-us/azure/cost-management-billing/reservations/reservation-renew)

## 8. [Platform automation and DevOps](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/landing-zone/design-area/platform-automation-devops)

### Github
- [General Availability: GitHub Copilot Enterprise - 27 Feb 2024](https://microsoft.seismic.com/app?ContentId=6ce9e658-abb1-4157-b451-4e064220d3f5#/doccenter/a5266a70-9230-4c1e-a553-c5bddcb7a896/doc/%252Fdde0caec0e-9236-f21b-2991-5868e63d3984%252FdfYTZjNDRiZDMtMzEwZS1kNWZkLTNjOGEtNjliYWJjMjhmMmUw%252CPT0%253D%252CR2VuZXJhbCBBdmFpbGFiaWxpdHk%253D%252Flf63bba962-2758-49bc-8e99-fde4006e2d3b/grid/)
  - Copilot Enterprise extends beyond the IDE to across GitHub.com, allowing the use of full context from an enterprise’s codebase to personalize Copilot across the developer workflow. 

