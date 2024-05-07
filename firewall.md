# General
- Firewall as a service
- [Fully stateful](https://learn.microsoft.com/en-us/azure/firewall/basic-features#network-traffic-filtering-rules)
  - This means it can distinguish legitimate packets for different types of connections. Whether it’s east-west or north-south traffic, Azure Firewall keeps track of the connection state and ensures that rules are enforced and logged across multiple subscriptions and virtual networks.
- Built-in high availability
- Unrestricted cloud scalability
- [East-west and north-south traffic inspection](https://learn.microsoft.com/en-us/azure/architecture/framework/security/design-network-flow#east-west-and-north-south-traffic)
- **Bring your own public IP addresses** to Azure Firewall by using the Custom IP Prefix feature. This allows you to bring your own public IPv4 ranges to Azure and use them with Azure resources, including Azure Firewall. You must own and have registered the range with a Routing Internet Registry, and the ranges must be no smaller than a /241. Once onboarded, these IPs can be associated with any resource that supports Standard SKU public IPs, such as virtual machines, Standard Public Load Balancers, Azure Firewalls, and more. The process of onboarding can be done through the Azure portal, Azure PowerShell, Azure CLI, or by using Azure Resource Manager (ARM) templates
  - [Custom IP Address Prefix](https://azure.microsoft.com/en-us/blog/bring-your-own-ip-addresses-byoip-to-azure-with-custom-ip-prefix/)

# SKUs
[![compare](https://learn.microsoft.com/en-us/azure/firewall/media/choose-firewall-sku/azure-firewall-sku-table-large.png#lightbox)](https://learn.microsoft.com/en-us/azure/firewall/media/choose-firewall-sku/azure-firewall-sku-table-large.png#lightbox)
## [Standard](https://learn.microsoft.com/en-us/azure/firewall/features)
- L3-L7 filtering
- Threat intelligence feeds directly from Microsoft Cyber Security 
  - Alert and deny traffic from/to known malicious IP addresses and domains that are updated in real time to protect against new and emerging attacks
- Built-in high availability
- Availability Zones
  - At deployment time only!
  - Added costs for inbound and outbound data transfers associated with Availability Zones
  - Can be deployed zone specific for proximity
  - Scales by zone, for each zone
- Unrestricted cloud scalability
  - No need to budget for peak times
- Application FQDN filtering rules
  - Group of fully qualified domain names (FQDNs) associated with well-known Microsoft services
  - Limit outbound HTTP/S traffic or Azure SQL traffic to a specified list of fully qualified domain names (FQDN) including wild cards. This feature doesn't require TLS termination.
- Network traffic filtering rules
  - Create allow or deny network filtering rules by source and destination IP address, port, and protocol
- [FQDN tags](https://learn.microsoft.com/en-us/azure/firewall/fqdn-tags)
  - Allow well-known Azure service network traffic e.g. Windows Update
- [Service tags](https://learn.microsoft.com/en-us/azure/firewall/service-tags)
  - Group of IP address prefixes associated with Azure Services to help minimize complexity for security rule creation.
  - Can't create own service tag, nor specify which IP addresses are included within a tag. Microsoft manages the address prefixes encompassed by the service tag, and automatically updates the service tag as addresses change
  - E.g. Office 365 and Virtual Network Service Tags e.g. ActionGroup
- DNS proxy
  - Azure Firewall process and forward DNS queries from VNET(s) to a defined DNS server
- Custom DNS
- FQDN in network rules
- Deploy Azure Firewall without public IP address in Forced Tunnel mode
- Outbound SNAT support
  - Azure Firewall randomly selects the source public IP address to use for a connection
  - If you have any downstream filtering on your network, you need to allow all public IP addresses associated with your firewall
  - Consider using a [public IP address prefix](https://learn.microsoft.com/en-us/azure/virtual-network/ip-services/public-ip-address-prefix) (range) to simplify this configuration
- Inbound DNAT support
- Multiple public IP addresses
  - Up to 250
- Azure Monitor logging
- Forced tunneling
- Web categories
- [Certifications](https://learn.microsoft.com/en-us/azure/firewall/compliance-certifications)
 
[![std](https://learn.microsoft.com/en-us/azure/firewall/media/overview/firewall-standard.png)](https://learn.microsoft.com/en-us/azure/firewall/media/overview/firewall-standard.png)

## Premium
[![prem](https://learn.microsoft.com/en-us/azure/firewall/media/premium-features/premium-overview.png)](https://learn.microsoft.com/en-us/azure/firewall/media/premium-features/premium-overview.png)
- TLS inspection - decrypts outbound traffic, processes the data, then encrypts the data and sends it to the destination.
- IDPS - A network intrusion detection and prevention system (IDPS) allows you to monitor network activities for malicious activity, log information about this activity, report it, and optionally attempt to block it.
- URL filtering - extends Azure Firewall’s FQDN filtering capability to consider an entire URL along with any additional path. For example, www.contoso.com/a/c instead of www.contoso.com.
- Web categories - administrators can allow or deny user access to website categories such as gambling websites, social media websites, and others.

## Basic
[![basic](https://learn.microsoft.com/en-us/azure/firewall/media/overview/firewall-basic-diagram.png)](https://learn.microsoft.com/en-us/azure/firewall/media/overview/firewall-basic-diagram.png)

# Deploy
- [Azure Firewall + Hybrid Network](https://learn.microsoft.com/en-us/azure/firewall/tutorial-hybrid-portal-policy)
- [Azure Firewall + Application Gateway (5 scenarios)](https://learn.microsoft.com/en-us/azure/architecture/example-scenario/gateway/firewall-application-gateway)
  - [Zero Trust](https://learn.microsoft.com/en-us/azure/architecture/example-scenario/gateway/application-gateway-before-azure-firewall)
- [Azure Firewall + NAT Gateway](https://learn.microsoft.com/en-us/azure/nat-gateway/tutorial-hub-spoke-nat-firewall?toc=%2Fazure%2Ffirewall%2Ftoc.json)
  - Increase SNAT ports = more scalable method of outbound connectivity
  - Azure Firewall provides 2,496 SNAT ports per public IP address configured per backend Virtual Machine Scale Set instance (minimum of two instances). You can associate up to 250 public IP addresses to Azure Firewall. Depending on your architecture requirements and traffic patterns, you may require more SNAT ports than what Azure Firewall can provide. You may also require the use of fewer public IPs while also requiring more SNAT ports. A better method for outbound connectivity is to use NAT gateway. NAT gateway provides 64,512 SNAT ports per public IP address and can be used with up to 16 public IP addresses.
  - **Frequent Request**: How can I specify a particular public IP address to be used for outbound connectivity on an Azure Firewall that has multiple IP addresses configured?
    - Currently, Azure Firewall does not support specifying a particular public IP address for outbound connectivity when multiple IP addresses are configured1. The firewall randomly selects the source public IP address to use for a connection. However, there are a couple of workarounds you might consider: 
      - Use an IP address prefix for your outbound public IP addresses: This way, you will know the range to whitelist1. You can refer to this guide on how to use a public IP address prefix.
      - Deploy a NAT gateway to the Azure Firewall subnet: This will route all outbound traffic through the NAT gateway and use its public IP address1. Here is an overview of NAT gateway.
Please note that these are just workarounds and may not perfectly fit your requirements. The feature to specify a particular public IP for outbound connectivity on Azure Firewall is on the roadmap but there’s no ETA yet. You can upvote the feature in the [feedback forum](https://feedback.azure.com/d365community/idea/59931e39-8426-ec11-b6e6-000d3a4f0789)
  - [![NAT](https://learn.microsoft.com/en-us/azure/nat-gateway/media/tutorial-hub-spoke-nat-firewall/resources-diagram.png#lightbox)](https://learn.microsoft.com/en-us/azure/nat-gateway/media/tutorial-hub-spoke-nat-firewall/resources-diagram.png#lightbox)
- [Azure Firewall + DDoS protected VNET](https://learn.microsoft.com/en-us/azure/firewall/tutorial-protect-firewall-ddos)
  - [DDoS Pricing](https://azure.microsoft.com/pricing/details/ddos-protection/)
  - [DDoS IP Protection vs. DDoS Network Protection](https://learn.microsoft.com/en-us/azure/ddos-protection/ddos-protection-sku-comparison)
  - [Built-in i.e. Infrastructure-level DDoS: Are services unsafe in Azure without the service?](https://learn.microsoft.com/en-us/azure/ddos-protection/ddos-faq#are-services-unsafe-in-azure-without-the-service-)
    - Services running on Azure are inherently protected by the default infrastructure-level DDoS protection. However, the protection that safeguards the infrastructure has a much higher threshold than most applications have the capacity to handle, and does not provide telemetry or alerting, so while a traffic volume may be perceived as harmless by the platform, it can be devastating to the application that receives it.
  - [DDoS Reference Architectures](https://learn.microsoft.com/en-us/azure/ddos-protection/ddos-protection-reference-architectures)
- [Azure Firewall + Load Balancer (internal or public)](https://learn.microsoft.com/en-us/azure/firewall/integrate-lb)
  - Benefit: Traffic Distribution. The Azure Load Balancer can optimally distribute traffic to services across global Azure regions1. This can help ensure efficient use of resources and improve application responsiveness.
  - [Public LB has assymetric routing considerations](https://learn.microsoft.com/en-us/azure/firewall/integrate-lb#fix-the-routing-issue)
- [Deploy and configure Azure Firewall and policy](https://learn.microsoft.com/en-us/azure/firewall/tutorial-firewall-deploy-portal-policy)
- [Backup Azure Firewall with Logic Apps](https://techcommunity.microsoft.com/t5/azure-network-security-blog/backup-azure-firewall-and-azure-firewall-policy-with-logic-apps/ba-p/3613928)
## IP Addresses
- [Table showing Azure Firewall only supports static IPv4](https://learn.microsoft.com/en-us/azure/virtual-network/ip-services/public-ip-addresses#at-a-glance)
- [IP SKU](https://learn.microsoft.com/en-us/azure/virtual-network/ip-services/public-ip-addresses#sku)
  - Basic
    - AvZones are not supported
    - DDoS IP Protection is not supported
    - On September 30, 2025, Basic SKU public IPs will be retired
  - [Upgrade a public IP address](https://learn.microsoft.com/en-us/azure/virtual-network/ip-services/public-ip-upgrade-portal)
    - In the majority of cases, Public IPs upgraded from Basic to Standard SKU continue to have no availability zones. This means they cannot be associated with an Azure resource that is either zone-redundant or tied to a pre-specified zone in regions where this is offered. (In rare cases where the Basic Public IP has a specific zone assigned, it will retain this zone when upgraded to Standard.)
- [Create a public IP address](https://learn.microsoft.com/en-us/azure/virtual-network/ip-services/create-public-ip-portal?tabs=option-1-create-public-ip-standard)
- [Create a public IP address prefix](https://learn.microsoft.com/en-us/azure/virtual-network/ip-services/create-public-ip-prefix-portal?tabs=create-default)
- [Create a custom IPv4 address prefix](https://learn.microsoft.com/en-us/azure/virtual-network/ip-services/create-custom-ip-address-prefix-portal)
- [Create a custom IPv6 address prefix](https://learn.microsoft.com/en-us/azure/virtual-network/ip-services/create-custom-ip-address-prefix-ipv6-portal)
## [Inbound Rules - DNAT](https://learn.microsoft.com/en-us/azure/firewall/tutorial-firewall-dnat-policy)

## Outbound Rules - SNAT

## [WAF Review](https://learn.microsoft.com/en-us/azure/well-architected/service-guides/azure-firewall?toc=%2Fazure%2Ffirewall%2Ftoc.json&bc=%2Fazure%2Ffirewall%2Fbreadcrumb%2Ftoc.json)

# Management
## Workbooks
- [Using Azure Firewall Workbooks](https://learn.microsoft.com/en-us/azure/firewall/firewall-workbook)
## IP Groups
  - IP Groups in Azure Firewall allow you to group and manage IP addresses for firewall rules in the following ways:
    - Source Address in DNAT Rules:
      You can use an IP Group as a source address in Destination Network Address Translation (DNAT) rules.
      DNAT rules modify the destination IP address of incoming traffic.
    - Source or Destination Address in Network Rules:
      An IP Group can serve as either a source or destination address in network rules.
      Network rules control traffic based on source and destination IP addresses.
    - Source Address in Application Rules:
      IP Groups can also be used as a source address in application rules.
      Application rules allow or deny traffic based on application-specific criteria.
    - Key points about IP Groups:
      - An IP Group can contain:
        - A single IP address.
        - Multiple IP addresses.
        - One or more IP address ranges.
        - A combination of addresses and ranges.
    - IP Groups can be reused across multiple firewalls, regions, and subscriptions in Azure.
    - Group names must be unique.
    - You can configure IP Groups using the Azure portal, Azure CLI, or REST API.
      For example, an IP Group could include a range of IP addresses representing specific services or applications. These groups enhance flexibility and simplify rule management within Azure Firewall.     - To create, browse, and manage IP Groups, you can use the Azure portal or command-line tools.
    - [Parallel IP Group updates (preview)](https://learn.microsoft.com/en-us/azure/firewall/ip-groups#parallel-ip-group-updates-preview)
## Policies
  - Even if you have a single Azure Firewall, using an Azure Firewall Policy is still recommended.
  - An Azure Firewall Policy is a global resource that can be used across multiple Azure Firewall instances.
  - It’s designed for use in both Secured Virtual Hubs and Hub Virtual Networks.
  - Policies work seamlessly across regions and subscriptions.
  - Creation and Association:
    - You can create and manage policies through various methods, including the Azure portal, REST API, templates, Azure PowerShell, CLI, and Terraform.
    - These policies can be associated with one or more virtual hubs or VNets.
    - The firewall itself can reside in any subscription associated with your account and in any region.
  - Classic Rules vs. Policies:
   - While Azure Firewall supports both Classic rules and policies, policies are the recommended configuration.
   - Here’s a comparison:
    - Policies contain NAT, Network, Application rules, custom DNS settings, IP Groups, Threat Intelligence settings, IDPS, TLS Inspection, Web Categories, and URL Filtering.
    - Classic rules cover NAT, Network, and Application rules, custom DNS settings, IP Groups, and Threat Intelligence settings.
    - Policies provide central management using Firewall Manager, making it easier to handle multiple firewalls.
  - Azure Firewall supports three policy types:
    - Basic: Includes NAT rules, Network rules, Application rules, IP Groups, and Threat Intelligence (alerts).
    - Standard: Adds Custom DNS, DNS proxy, Web Categories, and Threat Intelligence.
    - Premium: Includes all Standard features plus TLS Inspection, URL Filtering, and IDPS.
  - Hierarchical Policies:
    - You can create new policies from scratch or inherit from existing ones.
    - Inheritance allows DevOps to create local firewall policies on top of organization-mandated base policies.
    - Policies created with non-empty parent policies inherit all rule collections from the parent policy.
  - In summary, even with a single Azure Firewall, using a policy streamlines management, ensures consistency, and provides flexibility for future scaling.
  - For performance optimization and best practices, consider organizing rules into Rule Collection Groups, using IP Groups, and exploring Policy Analytics
## Hierarchy
  - Firewall Policy:
    A top-level resource that contains security and operational settings for Azure Firewall.
    Used to manage rule sets that the Azure Firewall uses to filter traffic.
    Organizes, prioritizes, and processes the rule sets based on a hierarchy.
  - Rule Collection Groups:
    The first unit processed by Azure Firewall.
    Groupings of rule collections.
    Follow a priority order based on values.
    Three default rule collection groups:
    Default DNAT (Destination Network Address Translation) rule collection group (Priority: 100)
    Default Network rule collection group (Priority: 200)
    Default Application rule collection group (Priority: 300)
    Custom rule collection groups can be created with desired priority values.
  - Rule Collections:
    Belong to a rule collection group.
    Contain one or multiple rules.
    The second unit processed by the firewall.
    Follow a priority order based on values.
    Types of rule collections:
    - DNAT: For Destination NAT rules.
    - Network: For network rules.
    - Application: For application rules.
  - Rules:
    Belong to a rule collection.
    Specify which traffic is allowed or denied in your network.
    The third unit processed by the firewall.
    Processed in a top-down approach.
    If no rule allows the traffic, it is denied by default.

## Scenario: Controlling Traffic for an Enterprise Application
### Background
- An enterprise has deployed a critical application in Azure.
- The application consists of web servers, database servers, and APIs.
- The enterprise wants to ensure secure and efficient communication between these components.
### Components
- Azure Firewall Policy:
  - The enterprise creates an Azure Firewall Policy to manage rule sets for traffic filtering.
  - The policy will organize rules based on a hierarchy.
- Rule Collection Groups:
  The enterprise defines custom rule collection groups:
  - WebServersRCG: Contains rules specific to web servers.
  - DatabaseServersRCG: Contains rules for database servers.
  - APIServersRCG: Contains rules for APIs.
  Each group has a different priority value.
- Rule Collections:
  Within each rule collection group:
  - WebServersRCG:
    - Contains rules allowing HTTP and HTTPS traffic to web servers.
    - Prioritizes web server communication.
  - DatabaseServersRCG:
    - Contains rules allowing SQL traffic to database servers.
    - Prioritizes database server communication.
  - APIServersRCG:
    - Contains rules allowing API traffic to APIs.
    - Prioritizes API communication.
- Rules:
  Within each rule collection:
  - WebServersRCG:
    - Rule 1: Allow inbound HTTP (port 80) traffic to web servers.
    - Rule 2: Allow inbound HTTPS (port 443) traffic to web servers.
  - DatabaseServersRCG:
    - Rule 1: Allow inbound SQL (port 1433) traffic to database servers.
  - APIServersRCG:
    - Rule 1: Allow inbound API (custom port) traffic to APIs.
- IP Groups:
  The enterprise creates IP groups:
  - WebServerIPs: Contains IP addresses of web servers.
  - DatabaseServerIPs: Contains IP addresses of database servers.
  - APIServerIPs: Contains IP addresses of APIs.
  These IP groups are used in rule definitions.
- Operational Flow:
- Traffic Flow:
  - Incoming traffic hits the Azure Firewall.
  - The firewall processes rules in the following order:
    - WebServersRCG: Web server-specific rules.
    - DatabaseServersRCG: Database server-specific rules.
    - APIServersRCG: API-specific rules.
  - If a rule allows the traffic, it proceeds to the appropriate component.
- Example Scenarios:
  - A user accesses the application’s web interface:
    - Traffic matches WebServersRCG rules and is allowed.
  - A database replication process occurs:
    - Traffic matches DatabaseServersRCG rules and is allowed.
- An external API sends data to the application:
  - Traffic matches APIServersRCG rules and is allowed.
- Benefits:
  - Centralized Management: Azure Firewall Policy simplifies rule management.
  - Granular Control: Rule collections allow fine-grained control.
  - IP Group Reusability: IP groups streamline rule definitions.
This example demonstrates how these components work together to secure and optimize traffic flow within an enterprise application.
### Examples of inherited Organizational Policies
- Base Firewall Policy:
  - Created by the security team in the security team resource group.
  - Contains IT security-specific rules:
    - Deny all inbound traffic by default.
    - Allow specific protocols (e.g., HTTP, HTTPS, SQL) based on business needs.
    - Threat Intelligence settings (alerts).
  - Provides a common set of rules for all firewalls.
- Application Team Policies:
  - Each application team (Sales, Database, Engineering) creates its own firewall policy.
  - These policies inherit from the base firewall policy.
  - Application-specific rules are defined in each policy:
    - Sales Team:
      - Inherits base policy rules.
      - Adds specific rules for sales-related services (e.g., CRM, sales portal).
    - Database Team:
      - Inherits base policy rules.
      - Adds rules for database communication (e.g., SQL traffic).
    - Engineering Team:
      - Inherits base policy rules.
      - Adds rules for engineering tools and services.
