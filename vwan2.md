# Overview
Last update: 9 July 2024

- [Mainstream Service](https://learn.microsoft.com/en-us/azure/reliability/availability-service-by-category#available-services-by-region-category)
  - Available in all **recommended regions** within 90 days of region GA
  - Demand-driven in **alternate regions** (already deployed to large subset)
- Centrally manage network, routing and security
- Hub-Spoke
- Scale, Performance and Security
- [Docs](https://learn.microsoft.com/en-us/azure/virtual-wan/)
- [Pricing](https://azure.microsoft.com/en-us/pricing/details/virtual-wan/#pricing)
- [Limits](https://learn.microsoft.com/en-us/azure/azure-resource-manager/management/azure-subscription-service-limits#virtual-wan-limits)
- [Security Baseline](https://learn.microsoft.com/en-us/security/benchmark/azure/baselines/virtual-wan-security-baseline)

# Architectures
- Secure hub, single region with routing intent
- Secure hub, single region with custom route tables, static routes, propagation and association
- Unsecured hub, single region with custom route tables, static routes, propagation and association
- Multi-hub, secured and secured or secured and unsecured or unsecured and unsecured, single region
- Cross-region, single vWAN, hub in one region (Spokes from remote region can connect to hub in another region)
- Cross-region, multiple hubs secured or unsecured

## [Global Transit Network Architecture](https://learn.microsoft.com/en-us/azure/virtual-wan/virtual-wan-global-transit-network-architecture)

- Hub enables transitive connectivity between endpoints distributed across different spokes e.g.
  - VNets
  - Physical branch site
  - Remote user
  - Internet
- [Any-to-any](https://learn.microsoft.com/en-us/azure/virtual-wan/virtual-wan-global-transit-network-architecture#anytoany)
- Easier to manage than full mesh networks

  ### Connectivity
    
  <img src="https://learn.microsoft.com/en-us/azure/virtual-wan/media/virtual-wan-global-transit-network-architecture/any-any.png" alt="anytoany" title="anytoany" width="500">

  #### Branch Connectivity

  - Virtual WAN Partner devices (e.g. SD-WAN or VPN CPE)
  - Site-to-site VPN connectivity (S2S)
  - Private connectivity (ExpressRoute)
  - VPN ExpressRoute inter-connectivity
  - [How to connect SD-WAN CPE to vWAN](https://learn.microsoft.com/en-us/azure/virtual-wan/virtual-wan-configure-automation-providers)
  - [Partner Branch IPsec connectivity](https://learn.microsoft.com/en-us/azure/virtual-wan/virtual-wan-locations-partners#partners)
  - [Partner SD-WAN CONNECTIVITY NVAs](https://learn.microsoft.com/en-us/azure/virtual-wan/about-nva-hub#partners)
    - Barracuda Networks
    - Cisco SD-WAN
    - VMware SD-WAN
    - Versa Networks
    - Aruba EdgeConnect	
  - [Partner SECURITY NVAs](https://learn.microsoft.com/en-us/azure/virtual-wan/about-nva-hub#partners)
    - Check Point CloudGuard Network Security for vWAN
    - Fortinet Next-Generation Firewall (NGFW)
    - (Preview) Cisco Secure Firewall Threat Defense for Azure Virtual WAN
  - [Partner CONNECTIVITY & SECURITY NVAs](https://learn.microsoft.com/en-us/azure/virtual-wan/about-nva-hub#partners)
    - Fortinet Next-Generation Firewall (NGFW)
  
  ##### Branch-to-VNet (a)
    - Gateway transit not required (vWAN enables automatic gateway transit)
  ##### Branch-to-branch (b)
  ##### Branch-to-hub-hub-to-Branch (f)
  ##### Branch-to-hub-hub-to-VNet (g)

  #### User Connectivity
  - Remote user VPN connectivity (P2S)
  
  ##### Remote User-to-VNet (c)
  ##### Remote User-to-branch (d)

  #### Intra-Cloud Connectivity
  - Transitive connectivity for Vnets
  - Cross-region VNet connection
    - Single Hub:
      
      <img src="https://learn.microsoft.com/en-us/azure/virtual-wan/media/virtual-wan-global-transit-network-architecture/transit-network.png" alt="1vwan" title="1vwan" width="500">
      
      - vWAN hub in region with most spokes (branches, VNets, users)
      - Connect spokes in other regions to the hub e.g. VNet in one region can be connected to a vWAN hub in a different region
      - Good option when an enterprise footprint is mostly in one region with a few remote spokes
    - Hub-to-hub:
      
      <img src="https://learn.microsoft.com/en-us/azure/virtual-wan/media/virtual-wan-global-transit-network-architecture/cross-region.png" alt="2vwan" title="2vwan" width="500">
      
      - When optimal (latency-wise) to access the cloud from a region closest to physical site and users
      - When multiple hubs are enabled in a single vWAN, the hubs are automatically interconnected via hub-to-hub links, thus enabling global connectivity between branches and Vnets that are distributed across multiple regions.
      - Hubs in same vWAN, can be associated with different regional access and security policies
    ##### VNet-to-VNet (e)
    - VNet-to-hub-hub-to-VNet (h)

# [Routing](https://learn.microsoft.com/en-us/azure/virtual-wan/about-virtual-hub-routing)
- Hub Router
- BGP
## Concepts

### Hub RT
- Hub RT can contain one or more routes
- A route includes its name, a label, a destination type, a list of destination prefixes, and next hop information for a packet to be routed.
- A Connection typically has a routing configuration that associates or propagates to a route table.
  
### Hub Routing Intent & Policies
- Traffic via Azure Firewall or Next-Gen Firewall NVA or SaaS solution in Hub
- Each Hub can have, at most, one Internet Traffic Routing Policy and one Private Traffic Routing Policy, each with a Next Hop resource.
- Private Traffic includes both branch and Vnet address prefixes (Routing Policies considers them as one entity within the Routing Intent concepts)
- Simple, declarative routing policies to send Internet-bound and Private traffic to security solutions (e.g. Azure Firewall, NVA or Saa) solutions in vWAN hub
- It is **not possible** to configure Routing Policies if hub isn't deployed with Azure Firewall, NVA or SaaS solution
- Routing Intent simplifies routing by managing route table associations and propagations for all connections (Vnet, S2S VPN, P2S VPN and ExpressRoute). VWANs with custom route tables and customized policies therefore can't be used with the Routing Intent constructs.
- Two types:
  Note: Max 1 of each/vWAN Hub i.e. a single next hop.
  1. **Internet Traffic Routing Policy**:
    - vWAN advertises a default (0.0.0.0/0) route to all spokes, Gateways and NVA (deployed in the hub or spoke)
    - When an Internet Traffic Routing Policy is configured on a hub, all branch and Vnet connections to that Hub will forward Internet-bound traffic to the Azure Firewall resource or Third-Party Security provider specified as part of the Routing Policy.
  2. **Private Traffic Routing Policy**: 
    - Both branch and Vnet address prefixes
    - When a Private Traffic Routing Policy is configured on a hub, all branch and Vnet traffic in and out of the Hub including inter-hub traffic will be forwarded to the Next Hop Azure Firewall resource that was specified in the Private Traffic Routing Policy.
 - [Use cases and traffic flows](https://learn.microsoft.com/en-us/azure/virtual-wan/how-to-routing-policies#use-cases)
 - [Additional known limitations, considerations, prerequisites and rollback strategy](https://learn.microsoft.com/en-us/azure/virtual-wan/how-to-routing-policies#knownlimitations)
 - [Prefix advertisements to on-premises after routing intent is configured on a virtual hub](https://learn.microsoft.com/en-us/azure/virtual-wan/how-to-routing-policies#prefixadvertisments)

### Connections (routing configuration)
- Connection Types:
  1.  VPN connection: Connects a VPN site to a virtual hub VPN gateway.
  2.  ExpressRoute connection: Connects an ExpressRoute circuit to a virtual hub ExpressRoute gateway.
  3.  P2S configuration connection: Connects a User VPN (Point-to-site) configuration to a virtual hub User VPN (Point-to-site) gateway.
  4.  Hub virtual network connection: Connects virtual networks to a virtual hub.
- By default, all connections **associate and propagate to the Default route table**
  - Each hub has its own Default RT, which can be edited to add a static route(s).
  - Routes added statically take precedence over dynamically learned routes for the same prefixes.

### Associations
- A connection is associated to 1x RT
- Association allows traffic from that connection to be sent to the destination indicated as routes in the RT
- A RT can have multiple associated connections
- All VPN, ExpressRoute, and User VPN connections are associated to the same (default) RT
    
<img src="https://learn.microsoft.com/en-us/azure/virtual-wan/media/about-virtual-hub-routing/concepts-association.png" alt="association" title="association" width="750">

### Propagations
- Connections dynamically propagate routes to a RT
- S2S, ExpressRoute, and P2S connection routes are propagated from the hub to on-premises router using BGP
- Routes can be propagated to one or multiple RTs
- A 'None RT' is available for each hub
  - Propagating to None RT implies no routes are required to be propagated from the connection
  - VPN, ExpressRoute, and User VPN connections propagate routes to the same set of route tables    

<img src="https://learn.microsoft.com/en-us/azure/virtual-wan/media/about-virtual-hub-routing/concepts-propagation.png" alt="association" title="association" width="750">

### Labels
- Logically group RT
- Helpful during propagation of routes from connections to multiple RTs e.g., the Default Route Table has a built-in label called 'Default'. When users propagate connection routes to 'Default' label, it automatically applies to all the Default Route Tables across every hub in the Virtual WAN.
- If no label is specified in the list of labels that a VNet connection is propagating to, then the Vnet connection will automatically propagate to the 'Default' label.

### Static Routes in Vnet connection
- To direct traffic from hub through a next hop IP, which could be an NVA provisioned in a Spoke VNet attached to a hub
- Static route is composed of a route name, list of destination prefixes, and a next hop IP.

### Hub Reset
- A way to bring any failed resources such as RTs, hub router, or the hub resource itself back to its rightful provisioning state
- Consider resetting the hub prior to contacting Microsoft for support
- Doesn't reset any of the gateways in a hub
 
## [Configure vHub routing](https://learn.microsoft.com/en-us/azure/virtual-wan/how-to-virtual-hub-routing)
- RT:
  - Name, Route Name, Destination Type, Destination Prefix (aggregate e.g. VNet 1: 10.1.0.0/24 and VNet 2: 10.1.1.0/24 can be aggregated as 10.1.0.0/16), Next Hop, Next Hop IP
  - Labels
  - Associations:
    - **Associate connections to the RT** e.g. Branches (Yes/No - applies to all connections), Vnets (choose Vnets)
  - Propagations:
    - Set **routes to propagate from connections to RT** e.g. Branches (Yes/No - choose labels to propagate branch connections to), Vnets (choose Vnets)
- Vnet Connection:
  - Hub, Vnet
  - Routing:
    - Propagate to none (Yes/No)
    - Associate to RT
    - Propagate to RTs
    - Propagate to Labels
    - Static Routes
    - Bypass next Hop IP for workloads **within this VNet**
      - Lets you deploy NVAs and other workloads into the same VNet without forcing all the traffic through the NVA
    - Propagate static route (Yes/No)
      - These routes can be propagated inter-hub, except for the default route 0/0.
      - This feature is in the process of rolling out. If you need this feature enabled please open a support case - as at July 2024

## Considerations
- All branch connections need to be associated to the Default RT. (All branches will then learn the same prefixes)
- All branch connections need to propagate their routes to the same set of RTs e.g., if you decide that branches should propagate to the Default RT, this configuration should be consistent across all branches. As a result, all connections associated to the Default RT will be able to reach all of the branches.
- When you use Azure Firewall in multiple regions, all spoke Vnets must be associated to the same RT e.g., having a subset of the VNets going through the Azure Firewall while other VNets bypass the Azure Firewall in the same hub isn't possible.
- You can specify multiple next hop IP addresses on a single Vnet connection. However, Vnet Connection doesn't support ‘multiple/unique’ next hop IP to the ‘same’ NVA in a SPOKE Vnet 'if' one of the routes with next hop IP is indicated to be public IP address or 0.0.0.0/0 (internet)
  
**//// Important Note ////**

- All information pertaining to 0.0.0.0/0 route is confined to a local hub's route table. **This route doesn't propagate across hubs**

**////**
  
- You can only use Virtual WAN to program routes in a spoke if the prefix is shorter (less specific) than the Vnet prefix e.g., in the diagram above the spoke VNET1 has the prefix 10.1.0.0/16: in this case, Virtual WAN wouldn't be able to inject a route that matches the Vnet prefix (10.1.0.0/16) or any of the subnets (10.1.0.0/24, 10.1.1.0/24). In other words, Virtual WAN can't attract traffic between two subnets that are in the same Vnet.
- While it's true that 2 hubs on the same virtual WAN will announce routes to each other (as long as the propagation is enabled to the same labels), this only applies to dynamic routing. Once you define a static route, this isn't the case.

# ExpressRoute
  
  - Private communication between on-premises networks across different locations using ExpressRoute circuits and Microsoft’s global network
  - ExpressRoute Skus supported by vWAN: Local, Standard, and Premium
## ExpressRoute-to-ExpressRoute Transit
  - Customers must choose between [two configuration options](https://learn.microsoft.com/en-us/azure/virtual-wan/how-to-routing-policies#expressroute):

### Option 1: ExpressRoute Global Reach
  -   ExpressRoute-to-ExpressRoute transit connectivity with ExpressRoute **Global Reach** added to ExpressRoute circuits
  -   Global Reach is an ExpressRoute add-on feature that allows you to link ExpressRoute circuits in different peering locations together to make a private network.
  - ExpressRoute-to-ExpressRoute transit connectivity between circuits with Global Reach **will not transit via the vWAN hub** because Global Reach enables a more optimal path over the global backbone
      
### Option 2: Routing Intent private policy
  - The [Routing Intent](https://learn.microsoft.com/en-us/azure/virtual-wan/how-to-routing-policies#expressroute) feature can be used with private traffic routing policies to enable ExpressRoute transit connectivity via a security appliance deployed in the vWAN Hub.
  - **This option doesn't require Global Reach**
  - **Note:** To enable ExpressRoute-to-ExpressRoute transit connectivity via a Firewall appliance in the hub with private routing policies, open a support case with Microsoft Support. This option is not compatible with Global Reach and requires Global Reach to be disabled to ensure proper transit routing between all ExpressRoute circuits connected to Virtual WAN.
- Connectivity across ExpressRoute circuits via a Firewall appliance in the hub with routing intent private routing policy is available in the following configurations:
  - Both ExpressRoute circuits are connected to the same hub and a private routing policy is configured on that hub.
  - ExpressRoute circuits are connected to different hubs and a private routing policy is configured on both hubs. Therefore, both hubs must have a security solution deployed.

## [Encryped ExpressRoute (S2S VPN tunnel over ExpressRoute Circuit)](https://learn.microsoft.com/en-us/azure/virtual-wan/how-to-routing-policies#encryptedER)

- [Performance recommendations](https://learn.microsoft.com/en-us/azure/virtual-wan/how-to-routing-policies#performance)

# [Private Link and DNS](https://learn.microsoft.com/en-us/azure/architecture/networking/guide/private-link-virtual-wan-dns-guide?toc=%2Fazure%2Fvirtual-wan%2Ftoc.json&bc=%2Fazure%2Fvirtual-wan%2Fbreadcrumb%2Ftoc.json)
- It is typical to require DNS resolution for workloads in vWAN spokes to connect to PaaS services e.g. a storage account using private link.
- It isn't possible to link private DNS zones to vWAN hubs, so any DNS resolution that happens within the hub isn't aware of private zones. Specifically, this is a problem for Azure Firewall, the configured DNS provider for workload spokes, which is using DNS for FQDN resolution. Azure DNS is called from the firewall instead of from the workload's network, so any private DNS zone links on the workload network aren't used in the resolution. To configure the regional firewall to be the spoke's dns provider, set the custom DNS server on the spoke virtual network to point to the private IP of the firewall instead of to the normal Azure DNS value.
- [Private Link Working & Non-working Examples for illustration](https://learn.microsoft.com/en-us/azure/architecture/networking/guide/private-link-virtual-wan-dns-guide?toc=%2Fazure%2Fvirtual-wan%2Ftoc.json&bc=%2Fazure%2Fvirtual-wan%2Fbreadcrumb%2Ftoc.json#working-scenario)
- [Virtual Hub Extension Pattern](https://learn.microsoft.com/en-us/azure/architecture/networking/guide/private-link-virtual-wan-dns-virtual-hub-extension-pattern): Guidance to securely expose shared services (e.g., Bastion & DNS) to spokes that cannot be deployed directly in a virtual hub
  - [Single region working DNS example of Extension Pattern](https://learn.microsoft.com/en-us/azure/architecture/networking/guide/private-link-virtual-wan-dns-single-region-workload)
    - Cross-region recommendation is to establish a hub extension per regional hub where private endpoint DNS resolution is required
    - For **higher resiliency and increased load handling**, deploy multiple DNS Private Resolver instances per region, with Azure DNS proxy configured with multiple IP addresses for proxied resolution

# Links
- [Migrate Hub-Spoke to vWAN](https://learn.microsoft.com/en-us/azure/virtual-wan/migrate-from-hub-spoke-topology)
- [SD-WAN Architecture](https://learn.microsoft.com/en-us/azure/virtual-wan/sd-wan-connectivity-architecture)
  - Direct Interconnect model (CPE is directly connected to vWAN hubs via IPsec connections)
  - Direct Interconnect model with NVA-in-VWAN-hub (Proprietary end-to-end SD-WAN by connecting branch CPE to same brand NVA in vWAN hub)
  - Indirect Interconnect model (SD-WAN virtual CPE is deployed in an enterprise Vnet)
  - Managed Hybrid WAN model using s managed service provider MSP (similar to direct/indirect but design, orchestration and operations done by SD-WAN provider)
