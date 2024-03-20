Last updated: March 2024

# General
- [The five Rs of rationalization](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/digital-estate/5-rs-of-rationalization#the-five-rs-of-rationalization)
- [Contoso Demo](https://review.learn.microsoft.com/en-us/asm-demo-script/script/cloud-management-migrate-demo?branch=main)

# Azure Migrate
- Integrate other tools and [ISV offerings (e.g. Carbonite, Cloudamize, Lakeside, etc)](https://learn.microsoft.com/en-us/azure/migrate/migrate-services-overview#isv-integration)
- Servers, databases and web apps
- VDI to AVD
- Data migration via Azure Data Box
- [Movere](https://www.movere.io/) is deprecated as of 1 March 2024
- [The MEG](aks.ms/migrate)
- Once you confirm the target region for migration in the Azure Migrate project, it cannot be changed for the project.
## Compare agentless versus agent-based migration
- Lightweight agent on each machine you want to replicate for migration. 
- Agent coordinates replication data from each machine and prepares it to send to Azure. 
- No need to power down systems during migration, ensuring a continuous operating cycle.
- Cost of managing and keeping agents up-to-date. 
- Agentless migration provides a cheaper solution and avoids the management overhead. 
- Agentless requires systems to be offline during migration. Generally, if a system is business-critical, you should use an agent-based option. 
- For non-critical or remote systems, short offline periods present no great problem, and you can use agentless migration.
## Azure Migrate Appliance Permissions
- Owner or Contributor role is required to configure and register the Azure Migrate appliance
- The appliance stores Microsoft Entra app information and appliance configuration settings in an Azure Key Vault
## Replication
- The resources used by Azure Migrate Server Migration are
  - Service bus: Sends replication orchestration information to the Azure Migrate appliance
  - Gateway storage account: Stores VM state information
  - Log storage account: stores replication logs
  - Key vault: Manages connection strings for the service bus, and access keys for the storage accounts used in replication
- Replicate up to 100 virtual machines per batch using the Azure Migrate: Server Migration tool
- A non-production VNET built for testing is recommended when testing your migration
## Business Case/TCO
- Build a business case to understand the return on investment for migrating your servers, SQL Server deployments and ASP.NET web apps to Azure. Eliminate the guess work in your cost planning process and get data driven insights to understand:
  1. On-premises vs Azure total cost of ownership and year on year cashflow analysis
  2. Resource utilization-based insights to identify servers and workloads that are ideal for cloud
  3. Quick wins for migration and modernization including end of support Windows OS and SQL versions

## Discovery and Assessment
- Lightweight Azure Migrate appliance
- Agentless discovery

### On-premises VMware VMs
- Agentless or agent-based
- For agentless migration, the Migration and modernization tool uses the same appliance that is used by Discovery and assessment tool for discovery and assessment of servers.
- For agent-based migration, the Migration and modernization tool uses a replication appliance.

### On-premises Hyper-V VMs
- The Migration and modernization tool uses provider agents installed on Hyper-V host for the migration.
  
### SQL
  - [Azure Migrate supports discovery and assessment of SQL Server deployments running in VMware, Microsoft Hyper-V, and Bare-metal/ physical environments as well as IaaS services of other public clouds for migration to SQL Server on Azure VM or modernization to Azure SQL Managed Instance or Azure SQL Database.
    It now supports the As On-Premises sizing criteria which enables sizing recommendations based on the instance size on-premises.
    When performance data is not available, it will fallback to As On-Premises sizing](https://go.microsoft.com/fwlink/?linkid=2249165)

### Web app
  - [Discovery and assessment of ASP.NET web apps running on IIS servers in VMware environment](https://go.microsoft.com/fwlink/?linkid=2168189)

## Migration

### Migration Bandwidth

#### Agentless
- [Gauge bandwidth for agentless](https://learn.microsoft.com/en-us/azure/migrate/common-questions-server-migration#how-do-i-gauge-the-bandwidth-requirement-for-my-migrations)
  - Time to complete initial replication = {size of disks (or used size if available) * 0.7 (assuming a 30 percent compression average – conservative estimate)}/bandwidth available for replication
- [Throttle Replication for agentless VMware replication](https://learn.microsoft.com/en-us/azure/migrate/common-questions-server-migration#how-do-i-throttle-replication-in-using-azure-migrate-appliance-for-agentless-vmware-replication)
  - NetQosPolicy on Azure Migrate appliance
  - Scheduled Task to scale as needed
- Churn on agentless replication
  - Because agentless replication folds in data, the churn pattern is more important than the churn rate.
  - When a file is written again and again, the rate doesn't have much impact. However, a pattern in which every other sector is written causes high churn in the next cycle.
  - Because we minimize the amount of data we transfer, we allow the data to fold as much as possible before we schedule the next cycle.
- Replication cycle frequency for agentless
  - The formula to schedule the next replication cycle is (previous cycle time / 2) or one hour, whichever is higher
  - If a VM takes four hours for a delta cycle, the next cycle is scheduled in two hours, and not in the next hour. The process is different immediately after initial replication, when the first delta cycle is scheduled immediately.

#### Agent-based
- Gauge Bandwidth for Agent-based
  - For an agent-based method of replication, the [Deployment Planner](https://learn.microsoft.com/en-us/azure/migrate/agent-based-migration-architecture#plan-vmware-deployment) can help profile the environment for the data churn and help predict the necessary bandwidth requirement.

### SQL
  - Azure Database Migration Service
    - Migrate on-premises databases to Azure VMs running SQL Server, Azure SQL Database, or SQL Managed Instances

### Web app 
- Web app migration assistant	Assess on-premises web apps and migrate them to Azure.	Azure App Service Migration Assistant is a standalone tool to assess on-premises websites for migration to Azure App Service.
  - Use Migration Assistant to migrate .NET and PHP web apps to Azure. Learn more about Azure App Service Migration Assistant.

### Data
- Azure Data Box
  - [Migrate large amounts of offline data](https://learn.microsoft.com/en-us/azure/databox/)

### Server Migration (VMware to Azure)
1. Set up prerequisites and ensure both environments have the necessary permissions.
[![VMware Source Environment](https://learn.microsoft.com/en-us/training/m365/m365-azure-migrate-set-up/media/vmware-starting-environment.png "VMware Source Environment")](https://learn.microsoft.com/en-us/training/m365/m365-azure-migrate-set-up/media/vmware-starting-environment.png "VMware Source Environment")
2. Set up an Azure Migrate project, including the tools for assessment and migration.
[![Azure Migrate](https://learn.microsoft.com/en-us/training/m365/m365-azure-migrate-set-up/media/migrate-azure-tools.png "Azure Migrate")](https://learn.microsoft.com/en-us/training/m365/m365-azure-migrate-set-up/media/migrate-azure-tools.png "Azure Migrate")
[![Select Tools](https://learn.microsoft.com/en-us/training/m365/m365-azure-migrate-set-up/media/choose-assessment.png "Select Tools")](https://learn.microsoft.com/en-us/training/m365/m365-azure-migrate-set-up/media/choose-assessment.png "Select Tools")
[![Select Tools](https://learn.microsoft.com/en-us/training/m365/m365-azure-migrate-set-up/media/choose-migration.png "Select Tools")](https://learn.microsoft.com/en-us/training/m365/m365-azure-migrate-set-up/media/choose-migration.png "Select Tools")
[Demo: Set up Azure Migrate](https://www.microsoft.com/en-us/videoplayer/embed/RE5bjXL?postJsllMsg=true)
3. Download a virtual machine appliance and deploy it in VMware environment to discover virtual machines and dependencies.
4. Review the assessment and dependencies from Azure Migrate while scoping the target migration.
[Demo: Deploy Appliance, Discover and Assess Servers, view Dependencies](https://www.microsoft.com/en-us/videoplayer/embed/RE4sToH?postJsllMsg=true)
5. Replicate virtual machines from VMware into Azure storage.
[![Replication Workflow](https://learn.microsoft.com/en-us/training/wwl-azure/migrate-on-premises-workloads-azure/media/6654-replication-process.png)](https://learn.microsoft.com/en-us/training/wwl-azure/migrate-on-premises-workloads-azure/media/6654-replication-process.png)
[Demo: Replicate Machines](https://www.microsoft.com/en-us/videoplayer/embed/RE4sVXi?postJsllMsg=true)
7. Test virtual machines using a test virtual network.
8. Migrate virtual machines into production using a production virtual network.
[Demo: Deploy Appliance](https://www.microsoft.com/en-us/videoplayer/embed/RE4sToH?postJsllMsg=true)
#### VMware Prerequisites
- [General](https://aka.ms/vmwareprereqs)
- [Prepare an Azure user account](https://learn.microsoft.com/en-us/azure/migrate/tutorial-discover-vmware#prepare-an-azure-user-account)
- [Prepare VMware](https://learn.microsoft.com/en-us/azure/migrate/tutorial-discover-vmware#prepare-vmware)
	- [Create an account to access vCenter Server](https://learn.microsoft.com/en-us/azure/migrate/tutorial-discover-vmware#create-an-account-to-access-vcenter-server)
> 	Global Read-only Permissions (can opt out)
- [Create an account to access servers](https://learn.microsoft.com/en-us/azure/migrate/tutorial-discover-vmware#create-an-account-to-access-servers)
> 	Windows: Administrator
> 	To discover SQL Server instances and databases, the Windows or SQL Server account must be a member of the sysadmin server role
> 	Linux: sudo with permissions to execute ls and netstat commands

## Modernization

### SQL to SQL on VM, SQL on SQL MI or Azure SQL Database

### Web apps to Azure App Service or AKS
- [Containerize ASP.NET applications running on Windows servers and Java web applications running on Apache Tomcat on Linux servers](https://go.microsoft.com/fwlink/?linkid=2156058)

# SQL

## [Choose a migration strategy](https://learn.microsoft.com/en-us/azure/azure-sql/migration-guides/virtual-machines/sql-server-to-sql-on-azure-vm-migration-overview?view=azuresql#migrate)
  - [Lift and shift](https://learn.microsoft.com/en-us/azure/azure-sql/migration-guides/virtual-machines/sql-server-to-sql-on-azure-vm-migration-overview?view=azuresql#lift-and-shift)
    Note: It's now possible to lift and shift both your failover cluster instance and availability group solution to SQL Server on Azure VMs using Azure Migrate.
  - Azure SQL migration extension for Azure Data Studio
  - Distributed availability group
  - Backup to a file
  - Backup to URL
  - Database Migration Assistant (DMA)
  - Detach and attach
  - Log shipping
  - Convert on-premises machine to Hyper-V VHDs, upload to Azure Blob storage, and then deploy a new virtual machine using uploaded VHD
  - Ship hard drive using Windows Import/Export Service

## Assessment
  - Data Migration Assistant
    - Assess SQL Server databases for migration to Azure SQL Database, Azure SQL Managed Instance, or Azure VMs running SQL Server.
    - Stand-alone tool to assess SQL Servers and pinpoint potential problems blocking migration.
    - Identifies unsupported features, new features that can benefit you after migration, and the right path for database migration.
  - [Public Preview: Azure SQL migration assessment enabled by Azure Arc - 14 Feb 2024](https://microsoft.seismic.com/app?ContentId=1e87f7a2-2b8a-4329-b3b9-43cd4740dfa0#/doccenter/a5266a70-9230-4c1e-a553-c5bddcb7a896/doc/%252Fdde0caec0e-9236-f21b-2991-5868e63d3984%252FdfYTZjNDRiZDMtMzEwZS1kNWZkLTNjOGEtNjliYWJjMjhmMmUw%252CPT0%253D%252CUHJldmlldw%253D%253D%252Flff78fb41d-b692-46a6-b369-78b31eef744d/grid/)

# [RDS to AVD](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-rds-to-wvd)

# [VMware to AVS](https://learn.microsoft.com/en-us/training/modules/deploy-azure-vmware-solution/)
