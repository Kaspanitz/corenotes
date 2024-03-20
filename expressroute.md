# ExpressRoute
- Dedicated connection between an on-premises network and Azure
- Not subject to delays, disruptions, and fluctuating bandwidth that can occur in a VPN connection

[![ExR](https://learn.microsoft.com/en-us/training/modules/intro-to-azure-expressroute/images/expressroute-structure.png)](https://learn.microsoft.com/en-us/training/modules/intro-to-azure-expressroute/images/expressroute-structure.png)

## Connectivity Models
- Point-to-point Ethernet connection:
  - Direct Layer 2 and Layer 3 connectivity between an organization’s on-premises network and Microsoft’s clouds
- CloudExchange co-location:
  - Layer 2 and Layer 3 connections between infrastructure at a colocation facility and the Microsoft cloud.
  - For example, if your organization had servers hosted at a colocation facility, colocation at a cloud exchange allows you to connect from that facility to Microsoft’s clouds.
- Any-to-any (IPVPN) connection:
  - Integrate organization WAN with Microsoft Azure
  - The ExpressRoute connection to Microsoft’s clouds functions as another location on the organization private WAN.
- Direct model:
  - You can connect directly at a Microsoft peering location rather than connecting through a service provider who connects you through to the Microsoft clouds.
  - ExpressRoute Direct provides dual 100-Gbps or 10-Gbps connectivity.
  
[![conmodel](https://learn.microsoft.com/en-us/training/modules/intro-to-azure-expressroute/images/expressroute-connectivity-models.png)](https://learn.microsoft.com/en-us/training/modules/intro-to-azure-expressroute/images/expressroute-connectivity-models.png)

## Security
- Private connection, helps compliance

## Circuits
- Circuits provide a physical connection for the transmission of data through a provider’s edge routers to the Microsoft cloud edge routers.
- Circuits are provided across private and dedicated cable connections and never traverse the public internet.

## Routing
- You need to ensure that Border Gateway Protocol (BGP) sessions for routing domains are configured. Some partners do this configuration for you, and some partners require you to perform this task yourself. For each ExpressRoute circuit, it's necessary to configure redundant BGP sessions between Microsoft’s routers and your peering routers.
- Reserve several blocks of IP addresses in your internal network for routing traffic to Microsoft’s cloud. These address blocks can either be configured as a /29 subnet or two /30 subnets in your organization’s IP address space. The first of these subnets is used with the primary circuit to Microsoft’s clouds. The second is used for the secondary circuit. Both IPs in these special subnets are used to establish a BGP session. When the session is established, the first IP represents the Microsoft Cloud endpoint and the second IP represent the customer's endpoint.

## Peering
- Private peering:
  - Allows you to connect on premises hosts with Azure IaaS and PaaS services configured to work with Azure virtual networks.
  - All resources must be located in Azure virtual networks and allocated IP addresses in a private address space that doesn't overlap with your on-premises address space.
  - You can’t connect to an Azure resource’s public IP address, such as an IaaS VM’s public IP address through private peering.
- Microsoft peering:
  - Microsoft peering allows you to connect over ExpressRoute with Azure PaaS services, Microsoft 365 services, and Dynamics 365.
  - If you're using Microsoft peering and you want to have resources in Azure connect to hosts on-premises, you need to reserve an extra IP address block.

[![Peering](https://learn.microsoft.com/en-us/training/modules/intro-to-azure-expressroute/images/expressroute-circuit-peerings.png)](https://learn.microsoft.com/en-us/training/modules/intro-to-azure-expressroute/images/expressroute-circuit-peerings.png)

# High Availability and Failover
- Each ExpressRoute circuit has two connections from the connectivity provider to two separate edge routers at Microsoft.
- These connections provide ExpressRoute availability for a single location.
- If you want a higher degree of availability, you can configure ExpressRoute circuits in different peering locations.
- Adding different locations protects your ExpressRoute connection from regional outages.
- Another option is to have multiple ExpressRoute circuits configured through different providers, so that should one provider suffer an outage, you can fail over to a second provider.

## SKU Scope Access

[![skuscope](https://learn.microsoft.com/en-us/azure/expressroute/media/expressroute-faqs/sku-scope.png)](https://learn.microsoft.com/en-us/azure/expressroute/media/expressroute-faqs/sku-scope.png)

### ExpressRoute premium
- Increased routing table limit from 4000 routes to 10,000 routes for private peering.
- Increased number of VNets and ExpressRoute Global Reach connections that can be enabled on an ExpressRoute circuit (default is 10). For more information, see the ExpressRoute Limits table.
- Connectivity to Microsoft 365
- Global connectivity over the Microsoft core network. You can now link a VNet in one geopolitical region with an ExpressRoute circuit in another region.
- Examples:
  - You can link a VNet created in Europe West to an ExpressRoute circuit created in Silicon Valley.
  - On the Microsoft peering, prefixes from other geopolitical regions are advertised such that you can connect to, for example, SQL Azure in Europe West from a circuit in Silicon Valley.

### ExpressRoute Local
- Access only to one or two Azure regions in or near the same metro. In contrast, a Standard circuit gives you access to all Azure regions in a geopolitical area and a Premium circuit to all Azure regions globally.
- Specifically, with a Local SKU you can only advertise routes over Microsoft and private peering from the corresponding local region of the ExpressRoute circuit. You won't receive routes for other regions different than the defined local region.
- ExpressRoute Local might not be available for an ExpressRoute Location. For peering location and supported Azure local region, see [locations and connectivity providers](https://learn.microsoft.com/en-us/azure/expressroute/expressroute-locations-providers#partners).

### ExpressRoute Global Reach
-  Allows routing traffic between on-premises locations that have ExpressRoute connections across the Azure global network backbone.

## Gateways
