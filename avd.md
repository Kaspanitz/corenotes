Last Update: March 2024

# Overview
- Use single-session to assign devices to a single user (personal), or use multi-session for scalability (pooled)
- Full desktop or individual app (RemoteApp)
- O365, LOB apps in Win32, MSIX, and Appx format
- Replace RDS
- On-premises via Azure Stack HCI (AVD GA now)
- Automatically increase or decrease capacity based on time of day, specific days of the week, or as demand changes with autoscale, helping to manage cost
- Only manage the image and virtual machines, not the infrastructure
- [Licenses](https://learn.microsoft.com/en-us/azure/virtual-desktop/prerequisites?tabs=portal#operating-systems-and-licenses)
  - [Apply Windows license to session host VMs](https://learn.microsoft.com/en-us/azure/virtual-desktop/apply-windows-license)
  - Automatic license if you create a host pool and its session host VMs in the Azure portal
  - Automatic license if you create a host pool and its session host VMs using the GitHub Azure Resource Manager template
  - Can manually apply a license to an existing session host VM via [PowerShell](https://learn.microsoft.com/en-us/azure/virtual-desktop/apply-windows-license#manually-apply-a-windows-license-to-a-windows-client-session-host-vm)
  - [PowerShell to verify that session host VM is utilizing the licensing benefit](https://learn.microsoft.com/en-us/azure/virtual-desktop/apply-windows-license#verify-your-session-host-vm-is-utilizing-the-licensing-benefit)
    - Get-AzVM -ResourceGroupName <resourceGroupName> -Name <vmName>
    - LicenseType : Windows_Client
- [AVD Tour Video](https://www.youtube.com/watch?v=aPEibGMvxZw)
- [Intro Video](https://www.youtube.com/watch?v=b5dJllgc-ew&t=20s)
- Other Azure virtual desktop solutions:
  - [VMware Horizon Cloud on Microsoft Azure](https://azure.microsoft.com/services/virtual-desktop/vmware-horizon-cloud) is a VMware service that simplifies the delivery of virtual desktops and applications on Azure by extending Azure Virtual Desktop.
  - [Citrix Virtual Apps and Desktops for Azure](https://azure.microsoft.com/services/virtual-desktop/citrix-virtual-apps-desktops-for-azure) is a desktop and app virtualization service that you can use to provision Windows desktops and apps on Azure with Citrix and Azure Virtual Desktop.
  - [Azure Lab Services](https://azure.microsoft.com/services/lab-services) provides computer labs in the cloud.
  - [Microsoft Dev Box Preview](https://azure.microsoft.com/services/dev-box) is a service that gives developers access to ready-to-code, project-specific workstations that are preconfigured and centrally managed in the cloud.
- Linux Session Hosts
  - Not supported
  - [Community Hack](https://avdpunks.com/avd/2022/05/23/AVDxLinux.html)

# Architecture
## AVD Landing Zone
- [AVD LZ Accelerator](https://github.com/Azure/avdaccelerator)
- [AVD Review Checklist](https://github.com/Azure/review-checklists)
- [AVD Service Limits](https://learn.microsoft.com/en-us/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-virtual-desktop-service-limits)
- [AVD Experience Estimator](https://azure.microsoft.com/en-gb/products/virtual-desktop/assessment/#estimation-tool)
- [Estimating Bandwidth Utilization](https://learn.microsoft.com/en-us/azure/virtual-desktop/rdp-bandwidth)
- [Understanding AVD Network Connectivity](https://learn.microsoft.com/en-us/azure/virtual-desktop/network-connectivity)
- [Azure Private Link with AVD](https://learn.microsoft.com/en-us/azure/virtual-desktop/private-link-overview)
  - You can use Azure Private Link with Azure Virtual Desktop to privately connect to your remote resources. By creating a private endpoint, traffic between your virtual network and the service remains on the Microsoft network, so you no longer need to expose your service to the public internet. You also use a VPN or ExpressRoute for your users with the Remote Desktop client to connect to the virtual network. Keeping traffic within the Microsoft network improves security and keeps your data safe. This article describes how Private Link can help you secure your Azure Virtual Desktop environment.
  - [Bypass Proxy Servers](https://learn.microsoft.com/en-us/azure/virtual-desktop/proxy-server-support)
## Components
  [![rel](https://learn.microsoft.com/en-us/azure/architecture/example-scenario/wvd/images/azure-virtual-desktop-component-relationships.png)](https://learn.microsoft.com/en-us/azure/architecture/example-scenario/wvd/images/azure-virtual-desktop-component-relationships.png)
### Host Pools
- [Host Pools](https://learn.microsoft.com/en-us/azure/virtual-desktop/terminology#host-pools): collection of Azure virtual machines that register to Azure Virtual Desktop as session hosts when you run the Azure Virtual Desktop agent.
  - Personal Host Pool
    - LB: User sessions are always load balanced to the session host the user is assigned to. If the user isn't currently assigned to a session host, the user session is load balanced to the next available session host in the host pool.
    - Max Session: 1
    - Assignment: Users can either be directly assigned to session hosts or be automatically assigned to the first available session host. Users always have sessions on the session hosts they are assigned to.
    - Scaling: [Autoscale](https://learn.microsoft.com/en-us/azure/virtual-desktop/autoscale-scaling-plan) for personal host pools starts session host virtual machines according to schedule or using Start VM on Connect and then deallocates/hibernates session host virtual machines based on the user session state (log off/disconnect).
    - Updates: Updated with Windows Updates, [Microsoft Configuration Manager](https://learn.microsoft.com/en-us/azure/virtual-desktop/configure-automatic-updates), or other software distribution configuration tools.
    - User Data: Each user only ever uses one session host, so they can store their user profile data on the operating system (OS) disk of the VM.
  - Pooled Host Pool
    - LB: User sessions are load balanced to session hosts in the host pool based on user session count. You can choose which [load balancing algorithm](https://learn.microsoft.com/en-us/azure/virtual-desktop/host-pool-load-balancing) to use: breadth-first or depth-first.
    - Max Session: As configured by the maximum session limit value of the properties of a host pool. Under high concurrent connection load when multiple users connect to the host pool at the same time, the number of sessions created on a session host can exceed the maximum session limit.
    - Assignment: Users aren't assigned to session hosts. After a user signs out and signs back in, their user session might get load balanced to a different session host.
    - Scaling: [Autoscale](https://learn.microsoft.com/en-us/azure/virtual-desktop/autoscale-scaling-plan) for pooled host pools turns VMs on and off based on the capacity thresholds and schedules the customer defines.
    - Updates: Updated by redeploying session hosts from updated images instead of traditional updates.
    - [Manage single-session W10/11 hosts with Intune](https://learn.microsoft.com/en-us/mem/intune/fundamentals/azure-virtual-desktop?toc=%2Fazure%2Fvirtual-desktop%2Ftoc.json&bc=%2Fazure%2Fvirtual-desktop%2Fbreadcrumb%2Ftoc.json)
      -  Use existing configurations and secure VMs with compliance policy and conditional access. Intune management doesn't depend on or interfere with Azure Virtual Desktop management of the same VM. See link for limitations.
    -  [Manage multi-session W10/11 hosts with Intune](https://learn.microsoft.com/en-us/mem/intune/fundamentals/azure-virtual-desktop-multi-session?toc=%2Fazure%2Fvirtual-desktop%2Ftoc.json&bc=%2Fazure%2Fvirtual-desktop%2Fbreadcrumb%2Ftoc.json)
    - User Data: Users can connect to different session hosts every time they connect, so they should store their user profile data in [FSLogix](https://learn.microsoft.com/en-us/fslogix/configure-profile-container-tutorial)
  - Can set host pool to be [Validation Environment](https://learn.microsoft.com/en-us/azure/virtual-desktop/configure-validation-environment) to monitor service updates before the service applies them to production or non-validation environment
    - Service updates (monthly, but can be more frequent if critical) are applied to validation environments first. Microsoft keeps an eye on this before applying updates to production environments. Ensure that a few users sign on to the validation environment to test.
### Application Groups
- [Application Group](https://learn.microsoft.com/en-us/azure/virtual-desktop/deploy-azure-virtual-desktop#create-an-application-group)
  - Logical grouping of applications installed on session hosts in the host pool
  - RemoteApp or Desktop type
### Workspaces
- [Workspaces](https://learn.microsoft.com/en-us/azure/virtual-desktop/deploy-azure-virtual-desktop#create-a-workspace)
  - Logical grouping of application groups in Azure Virtual Desktop
### [Identity](https://learn.microsoft.com/en-us/azure/virtual-desktop/authentication)
- [Supported identity scenarios](https://learn.microsoft.com/en-us/azure/virtual-desktop/prerequisites?tabs=portal#supported-identity-scenarios)
  - Entra ID is mandatory across all scenarios
  - Single Entra ID Tenant only
  - Doesn't support B2B, B2C, or personal Microsoft accounts
  - When using hybrid identities, either the UPN or the SID must match across AD DS and Microsoft Entra ID
- On-premises identity: User identities that exist only in Active Directory Domain Services (AD DS) aren’t supported. This includes standalone Active Directory deployments with Active Directory Federation Services (AD FS).
- Hybrid identity: AVD supports hybrid identities through Microsoft Entra ID, including those federated using AD FS. You can manage these user identities in AD DS and sync them to Microsoft Entra ID using Microsoft Entra Connect.
- Cloud-only identity: AVD supports cloud-only identities when using Microsoft Entra joined VMs. These users are created and managed directly in Microsoft Entra ID.
- [Session Host Authentication](https://learn.microsoft.com/en-us/azure/virtual-desktop/authentication#session-host-authentication)
- [Microsoft Entra joined session hosts in AVD](https://learn.microsoft.com/en-us/azure/virtual-desktop/azure-ad-joined-session-hosts#connect-using-the-other-clients). Limitations include:
  - AVD (classic) doesn't support Microsoft Entra joined VMs.
  - Microsoft Entra joined VMs don't currently support external identities, such as Microsoft Entra B2B and Microsoft Entra B2C.
  - Microsoft Entra joined VMs can only access Azure Files shares or Azure NetApp Files shares for hybrid users using Microsoft Entra Kerberos for FSLogix user profiles.
  - The Remote Desktop app for Windows doesn't support Microsoft Entra joined VMs.
- Deploying FSLogix Profile Container with different identity scenarios:
  - [Set up FSLogix Profile Container with Azure Files and Active Directory Domain Services or Microsoft Entra Domain Services](https://learn.microsoft.com/en-us/azure/virtual-desktop/fslogix-profile-container-configure-azure-files-active-directory)
  - [Set up FSLogix Profile Container with Azure Files and Microsoft Entra ID](https://learn.microsoft.com/en-us/azure/virtual-desktop/create-profile-container-azure-ad)
  - [Set up FSLogix Profile Container with Azure NetApp Files](https://learn.microsoft.com/en-us/azure/virtual-desktop/create-fslogix-profile-container)
#### [Multiple forests with AD DS and Microsoft Entra ID](https://learn.microsoft.com/en-us/azure/architecture/example-scenario/wvd/multi-forest)
[![1](https://learn.microsoft.com/en-us/azure/architecture/example-scenario/wvd/images/azure-virtual-desktop-multi-forest-adds.png#lightbox)](https://learn.microsoft.com/en-us/azure/architecture/example-scenario/wvd/images/azure-virtual-desktop-multi-forest-adds.png#lightbox)
#### [Multiple forests with AD DS, Microsoft Entra ID, and Microsoft Entra Domain Services](https://learn.microsoft.com/en-us/azure/architecture/example-scenario/wvd/multi-forest-azure-managed)
[![2](https://learn.microsoft.com/en-us/azure/architecture/example-scenario/wvd/images/wvd-multi-forest-aadds-edited.svg)](https://learn.microsoft.com/en-us/azure/architecture/example-scenario/wvd/images/wvd-multi-forest-aadds-edited.svg)
## Clients
- No need to open ports
- [Windows App (Preview) - Microsoft Store](https://learn.microsoft.com/en-us/windows-app/get-started-connect-devices-desktops-apps?tabs=windows-avd%2Cwindows-w365%2Cwindows-devbox%2Cmacos-rds%2Cmacos-pc&pivots=azure-virtual-desktop)
- [Remote Desktop Client](https://learn.microsoft.com/en-us/azure/virtual-desktop/users/connect-windows?toc=%2Fazure%2Fvirtual-desktop%2Ftoc.json&bc=%2Fazure%2Fvirtual-desktop%2Fbreadcrumb%2Ftoc.json&pivots=remote-desktop-msi)
  - Windows Desktop
  - Web Browser
  - macOS
  - iOS/iPADOS
  - Android/Chrome OS
  - Thin Clients
## [Enterprise Example](https://learn.microsoft.com/en-us/azure/architecture/example-scenario/wvd/windows-virtual-desktop)
[![enterprise](https://learn.microsoft.com/en-us/azure/architecture/example-scenario/wvd/images/windows-virtual-desktop.png)](https://learn.microsoft.com/en-us/azure/architecture/example-scenario/wvd/images/windows-virtual-desktop.png)
## Resilience
- [AVD Service Architecture and Resilience](https://learn.microsoft.com/en-us/azure/virtual-desktop/service-architecture-resilience)
- [AVD DR](https://learn.microsoft.com/en-us/azure/virtual-desktop/disaster-recovery)
- [AVD DR Scenarios](https://learn.microsoft.com/en-us/training/modules/business-continuity-disaster-recovery-azure-virtual-desktop/4-identify-correct-dr-scenario)
  - Scenario 1: Local corruption of data, metadata, or resources
  - Scenario 2: Single datacenter of availability zone failure within an Azure region
    - Consider adding sufficient session capacity when deploying session hosts across mulltiple Availability Zones
  - Scenario 3: Azure region outage
### [Multi-Region BCDR Example](https://learn.microsoft.com/en-us/azure/architecture/example-scenario/wvd/azure-virtual-desktop-multi-region-bcdr)
#### Personal (Persistent) Host Pool
[![personalhost](https://learn.microsoft.com/en-us/azure/architecture/example-scenario/wvd/images/azure-virtual-desktop-bcdr-personal-host-pool.png#lightbox)](https://learn.microsoft.com/en-us/azure/architecture/example-scenario/wvd/images/azure-virtual-desktop-bcdr-personal-host-pool.png#lightbox)
#### Pooled Host Pool
[![pooledhost](https://learn.microsoft.com/en-us/azure/architecture/example-scenario/wvd/images/azure-virtual-desktop-bcdr-pooled-host-pool.png)](https://learn.microsoft.com/en-us/azure/architecture/example-scenario/wvd/images/azure-virtual-desktop-bcdr-pooled-host-pool.png)

# Deployment
- Azure portal, Azure CLI, PowerShell and REST API
- [Quick Setup Video](https://www.youtube.com/watch?v=u99cY0MXZds)
## Images
- Own image or Azure Gallery Note: If using the gallery image with M365 apps preinstalled, the audio and video redirect optimisations are enabled automatically.
- Supported VM OS Images
  - Windows 11 Enterprise multi-session
  - Windows 11 Enterprise
  - Windows 10 Enterprise multi-session, version 1909 or later
  - Windows 10 Enterprise, version 1909 or later
  - Windows 7 Enterprise
  - Windows Server 2022
  - Windows Server 2019
  - Windows Server 2016
  - Windows Server 2012 R2
- [Custom Image Templates](https://learn.microsoft.com/en-us/azure/virtual-desktop/custom-image-templates)
  - Create and manage custom image templates to use when adding session hosts. Easily add common customizations or your own custom scripts. Custom image templates uses Azure Image Builder and tailored for Azure Virtual Desktop.
  - [Scaling, Imaging and Monitoring Video](https://www.youtube.com/watch?v=L9j_pSBVGy4&list=PLXtHYVsvn_b-bLI3NQ5X1tcPC6HCNBkpx&index=4)
  - Source Image can be:
    - Platform Image (marketplace)
      - Distribution targets can be managed image or Azure compute gallery
    - Managed Image
    - Azure Compute Gallery (Azure Compute Gallery allows you to manage image region replication, versioning and sharing of custom images. Azure Image Builder supports distributing with this service, so you can distribute images to regions supported by Azure Compute Galleries.)
  - Examples of built-in scripts that can be added to a custom image template:
    - Install language packs.
    - Set the default language of the operating system.
    - Enable time zone redirection.
    - Disable Storage Sense.
    - Install FSLogix and configure Profile Container.
    - Enable FSLogix with Kerberos.
    - Enable RDP Shortpath for managed networks.
    - Enable screen capture protection.
    - Configure Teams optimizations.
    - Configure session timeouts.
    - Add or remove Microsoft Office applications.
    - Apply Windows Updates.
## Configuration Options
- [Configuration Video](https://www.youtube.com/watch?v=QmWVmMFVF0Q)
  - Secure Auth
  - RDP Shortpath
  - FSLogix
  - HA/FO
    - Site Recovery for session hosts
    - ZRS for FSLogix containers
  - Confidential Computing
- [Session host virtual machine sizing guidelines](https://learn.microsoft.com/en-us/windows-server/remote/remote-desktop-services/virtual-machine-recs)
- [Configure device redirection](https://learn.microsoft.com/en-us/azure/virtual-desktop/configure-device-redirections)
  - Printers
  - USB devices
  - Audio output
  - Smart cards
  - Clipboard
  - Microphones
  - Cameras
## Automated Scaling
- [Scaling, Imaging and Monitoring Video](https://www.youtube.com/watch?v=L9j_pSBVGy4&list=PLXtHYVsvn_b-bLI3NQ5X1tcPC6HCNBkpx&index=4)
- Scaling for pooled, and [personal host pools](https://techcommunity.microsoft.com/t5/azure-virtual-desktop-blog/personal-desktop-autoscale-on-azure-virtual-desktop-generally/ba-p/3993783) now
- Schedule:
  - Day(s) to repeat
  - Ramp-up (start time, lb algorithm, min % hosts, capacity threshold % i.e. percentage of available host pool capacity that will trigger a scaling action. This is based on max session limit as defined on host pool)
  - Peak hours (adjust during run time, start time, lb algorithm, capacity threshold %)
  - Ramp-down (start time, lb algorithm, min % hosts, capacity threshold % i.e. percentage of available host pool capacity that will trigger a scaling action. This is based on sessions as defined on session hosts, force logoff users, delay before logging out users and shutdown, notification message)
  - Off-peak hours (adjust during run time, start time, lb algorithm, capacity threshold %)
- Azure Saving Plan and Reserved VM Instances to save cost
- [Start VM on Connect](https://learn.microsoft.com/en-us/azure/virtual-desktop/start-virtual-machine-connect?tabs=azure-portal)
## Profiles
- Pooled requires roaming profiles. Personal can store profiles locally.
### FSLogix
- Acquired Nov 2018
- High performance
- Can extend to additional folders
- Designed for roaming profiles
- Stores complete user profile in a single container
- At sign-in, container is dynamically attached to the computing environment as VNH and Hyper-V VHDX
- [FSLogix configuration examples](https://learn.microsoft.com/en-us/fslogix/concepts-configuration-examples)
- [FSLogix profile containers and Azure Files](https://learn.microsoft.com/en-us/azure/virtual-desktop/fslogix-containers-azure-files?toc=/azure/architecture/toc.json&bc=/azure/architecture/_bread/toc.json)
- [Storage options for FSLogix profile containers in Azure Virtual Desktop](https://learn.microsoft.com/en-us/azure/virtual-desktop/store-fslogix-profile)
  - Azure Files
  - Azure NetApp Files
  - Storage Spaces Direct
  - [Compare](https://learn.microsoft.com/en-us/azure/virtual-desktop/store-fslogix-profile#azure-platform-details)
  - [Tier Sizing](https://learn.microsoft.com/en-us/azure/virtual-desktop/store-fslogix-profile#azure-files-tiers)
  - [Profile Storage Sizing](https://learn.microsoft.com/en-us/windows-server/remote/remote-desktop-services/virtual-machine-recs)
  - [Azure Files Sizing Guidance for AVD](https://learn.microsoft.com/en-us/azure/storage/files/storage-files-scale-targets#azure-files-sizing-guidance-for-azure-virtual-desktop)
#### Resilience
  - [Cloud Cache](https://learn.microsoft.com/en-us/fslogix/concepts-fslogix-cloud-cache) to mitigate short-term/intermittent connectivity problems
    - Works with Profile and ODFC containers to provide resiliency and high availability
    - Uses the locally mounted container to provide periodic updates to the remote storage providers.
    - [FSLogix cloud cache, HA and DR Video](https://www.youtube.com/watch?v=B_R2Bi5SqVo&t=6s)
## Applications
### App Attach
- With both MSIX app attach and app attach, applications aren't installed locally on session hosts or images, making it easier to create custom images for your session hosts, and reducing operational overhead and costs for your organization. Applications run within containers, which separate user data, the operating system, and other applications, increasing security and making them easier to troubleshoot.
- [Compare MSIX App Attach and App Attach](https://learn.microsoft.com/en-us/azure/virtual-desktop/app-attach-overview?pivots=msix-app-attach)
#### MSIX App Attach
- Supports only AD DS
- MSIX app attach requires that your application images are stored on an SMB version 3 file share, which is then mounted on each session host during sign-in. MSIX app attach doesn't have dependencies on the type of storage fabric the file share uses. We recommend using Azure Files as it's compatible with the supported identity providers you can use for MSIX app attach, and offers great value between cost and management overhead. You can also use Azure NetApp Files, but that requires your session hosts are joined to Active Directory Domain Services.
#### App Attach (Preview)
- Improves the administrative and user experiences
- Supports Microsoft Entra ID and AD DS
- App attach requires that your application images are stored on an SMB file share, which is then mounted on each session host during sign-in. App attach doesn't have dependencies on the type of storage fabric the file share uses. We recommend using Azure Files as it's compatible with Microsoft Entra ID or Active Directory Domain Services, and offers great value between cost and management overhead. You can also use Azure NetApp Files, but that requires your session hosts to be joined to Active Directory Domain Services.
# User Connections
- During feed discovery, the desktops and apps available to the user are populated in the app on their local device. The feed contains all the information needed to connect.
  - The user might be located anywhere in the world. Azure Traffic Manager routes the user's device to the closest instance of the Azure Virtual Desktop web service based on the [geographic traffic-routing method](https://learn.microsoft.com/en-us/azure/traffic-manager/traffic-manager-routing-methods#geographic-traffic-routing-method), which uses source IP address of the user's device.
[![Feed](https://learn.microsoft.com/en-us/azure/virtual-desktop/media/service-architecture-resilience/feed-discovery-single.svg)](https://learn.microsoft.com/en-us/azure/virtual-desktop/media/service-architecture-resilience/feed-discovery-single.svg)
- RDP Connection:
  - One of two connection types is made, depending on the configuration and available network protocols:
    - Reverse connect transport: after both client and session host connected to the gateway service, it starts relaying the RDP traffic using Transmission Control Protocol (TCP) between the client and session host. Reverse connect transport is the default connection type.
    - [RDP Shortpath](https://learn.microsoft.com/en-us/azure/virtual-desktop/rdp-shortpath?tabs=managed-networks): a direct User Datagram Protocol (UDP)-based transport is created between the user's device and the session host, bypassing the gateway service.
      - RDP Shortpath can be used in two ways:
        - Managed networks, where direct connectivity is established between the client and the session host when using a private connection, such as a virtual private network (VPN).
        - Public networks, where direct connectivity is established between the client and the session host when using a public connection. There are two connection types when using a public connection, which are listed here in order of preference:
          - A direct UDP connection using the Simple Traversal Underneath NAT (STUN) protocol between a client and session host.
          - An indirect UDP connection using the Traversal Using Relay NAT (TURN) protocol with a relay between a client and session host. This is in preview.
[![connect](https://learn.microsoft.com/en-us/azure/virtual-desktop/media/service-architecture-resilience/feed-discovery-multi.svg)](https://learn.microsoft.com/en-us/azure/virtual-desktop/media/service-architecture-resilience/feed-discovery-multi.svg)

# Manage
- [Drain session hosts for maintenance in Azure Virtual Desktop](https://learn.microsoft.com/en-us/azure/virtual-desktop/drain-mode?tabs=portal)
- [Scaling, Imaging and Monitoring Video](https://www.youtube.com/watch?v=L9j_pSBVGy4&list=PLXtHYVsvn_b-bLI3NQ5X1tcPC6HCNBkpx&index=4)
- [Analyze connection quality in AVD](https://learn.microsoft.com/en-us/azure/virtual-desktop/connection-latency)

# Security
- [Security best practices for Azure Virtual Desktop](https://learn.microsoft.com/en-us/azure/virtual-desktop/security-guide)
- [Azure security baseline for Azure Virtual Desktop](https://learn.microsoft.com/en-us/security/benchmark/azure/baselines/virtual-desktop-security-baseline)
- [Screen Capture Protection](https://learn.microsoft.com/en-us/azure/virtual-desktop/screen-capture-protection)
- [Watermarking](https://learn.microsoft.com/en-us/azure/virtual-desktop/watermarking)
- [Clipboard transfer direction] (Preview)(https://learn.microsoft.com/en-us/azure/virtual-desktop/clipboard-transfer-direction-data-types?tabs=intune)
- [Trusted Launch VMs](https://techcommunity.microsoft.com/t5/azure-virtual-desktop-blog/azure-virtual-desktop-support-for-trusted-launch-virtual/ba-p/3053043)
  - Protect against the installation of malware-based rootkits and boot kits with Secure Boot.
  - Provide your VM with its own dedicated Trusted Platform Module instance with a vTPM.
  - Protect Windows kernel-mode processes against injection and execution of malicious or unverified code with Hypervisor Code Integrity.
  - Isolate and protect secrets so that only privileged system software can access them with Windows Defender Credential Guard.
  - Ability to perform feature updates when using Windows 11 Enterprise or Windows 11 Enterprise multi-session.
- [Confidential VMs](https://techcommunity.microsoft.com/t5/azure-virtual-desktop-blog/announcing-general-availability-of-confidential-vms-in-azure/ba-p/3857974)
  - On top of Trusted Launch, Confidential VMs offers higher confidentiality and integrity guaranteed with hardware-based trusted execution environment.
- [Zero Trust Architecture](https://learn.microsoft.com/en-us/security/zero-trust/azure-infrastructure-avd?context=%2Fazure%2Fvirtual-desktop%2Fcontext%2Fcontext)
[![ZT](https://learn.microsoft.com/en-us/security/zero-trust/media/avd/ref-arch-avd.svg)](https://learn.microsoft.com/en-us/security/zero-trust/media/avd/ref-arch-avd.svg)
