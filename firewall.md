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







