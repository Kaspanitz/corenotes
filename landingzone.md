Last Updated: March 2024

- [ALZ Bicep](https://learn.microsoft.com/en-us/azure/architecture/landing-zones/bicep/landing-zone-bicep)
- [ALZ - Terraform](https://learn.microsoft.com/en-us/azure/architecture/landing-zones/terraform/landing-zone-terraform)
- [Review Checklist](https://github.com/Azure/review-checklists)

# [Design Areas](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/landing-zone/design-areas)


## 1. [Billing and Microsoft Entra Tenant](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/landing-zone/design-area/azure-billing-ad-tenant)

## 2. [Identity and access management](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/landing-zone/design-area/identity-access)

## 3. [Resource Organization](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/landing-zone/design-area/resource-org)

## 4. [Network topology and connectivity](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/landing-zone/design-area/network-topology-and-connectivity)

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

### Storage
- [Public Preview: Azure Storage Actions – Streamline management of storage objects - 20 Feb 2024](https://microsoft.seismic.com/app?ContentId=ea9923b6-05ca-4efd-ab9a-b59c6a0963af#/doccenter/a5266a70-9230-4c1e-a553-c5bddcb7a896/doc/%252Fdde0caec0e-9236-f21b-2991-5868e63d3984%252FdfYTZjNDRiZDMtMzEwZS1kNWZkLTNjOGEtNjliYWJjMjhmMmUw%252CPT0%253D%252CUHJldmlldw%253D%253D%252Flf6905c880-7e7b-4627-8f89-0bd0d83cb71a/grid/?anchorId=78ee1704-9bf5-4767-8218-25b82752055b)
  - Automate data protection, tagging, and cost optimization operations for billions of objects in Azure storage accounts with an interactive no-code experience, without provisioning or managing any infrastructure.
  - [Azure Storage Actions](https://learn.microsoft.com/en-us/azure/storage-actions/storage-tasks/)

## 7. [Governance](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/landing-zone/design-area/governance)

### Policies
- [Azure Policy built-in policy definitions](https://learn.microsoft.com/en-us/azure/governance/policy/samples/built-in-policies)
- [Enterprise Azure Policy as Code (EPAC)](https://azure.github.io/enterprise-azure-policy-as-code/)


## 8. [Platform automation and DevOps](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/landing-zone/design-area/platform-automation-devops)

### Github
- [General Availability: GitHub Copilot Enterprise - 27 Feb 2024](https://microsoft.seismic.com/app?ContentId=6ce9e658-abb1-4157-b451-4e064220d3f5#/doccenter/a5266a70-9230-4c1e-a553-c5bddcb7a896/doc/%252Fdde0caec0e-9236-f21b-2991-5868e63d3984%252FdfYTZjNDRiZDMtMzEwZS1kNWZkLTNjOGEtNjliYWJjMjhmMmUw%252CPT0%253D%252CR2VuZXJhbCBBdmFpbGFiaWxpdHk%253D%252Flf63bba962-2758-49bc-8e99-fde4006e2d3b/grid/)
  - Copilot Enterprise extends beyond the IDE to across GitHub.com, allowing the use of full context from an enterprise’s codebase to personalize Copilot across the developer workflow. 

