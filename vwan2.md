# Overview
Last update: 9 July 2024

- [Mainstream Service](https://learn.microsoft.com/en-us/azure/reliability/availability-service-by-category#available-services-by-region-category)
  - Available in all **recommended regions** within 90 days of the region general availability
  - Demand-driven in **alternate regions** (already deployed into a large subset of alternate regions)
- Central operational interface for networking, routing and security
- Hub-Spoke architecture
- Scale, Performance and Security
- [Docs](https://learn.microsoft.com/en-us/azure/virtual-wan/)
- [Pricing](https://azure.microsoft.com/en-us/pricing/details/virtual-wan/#pricing)
- [Limits](https://learn.microsoft.com/en-us/azure/azure-resource-manager/management/azure-subscription-service-limits#virtual-wan-limits)
- [Security Baseline](https://learn.microsoft.com/en-us/security/benchmark/azure/baselines/virtual-wan-security-baseline)

# [Global Transit Network Architecture](https://learn.microsoft.com/en-us/azure/virtual-wan/virtual-wan-global-transit-network-architecture)

- Hub enables transitive connectivity between endpoints distributed across different spokes e.g.
  - Virtual network (VNets)
  - Physical branch site
  - Remote user
  - Internet
- [Any-to-any](https://learn.microsoft.com/en-us/azure/virtual-wan/virtual-wan-global-transit-network-architecture#anytoany)
- Easier to manage than full mesh networks

  ## Connectivity
    
  <img src="https://learn.microsoft.com/en-us/azure/virtual-wan/media/virtual-wan-global-transit-network-architecture/any-any.png" alt="anytoany" title="anytoany" width="500">

  ### Branch Connectivity

  - Virtual WAN Partner devices (e.g. SD-WAN or VPN CPE)
  - Site-to-site VPN connectivity (S2S)
  - Private connectivity (ExpressRoute)
  - VPN ExpressRoute inter-connectivity
  
  #### Branch-to-VNet (a)
    - Gateway transit not required (vWAN enables automatic gateway transit)
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
  #### Branch-to-branch (b)
  #### Branch-to-hub-hub-to-Branch (f)
  #### Branch-to-hub-hub-to-VNet (g)

  ### User Connectivity
  - Remote user VPN connectivity (P2S)
  
  #### Remote User-to-VNet (c)
  #### Remote User-to-branch (d)

  ### Intra-Cloud Connectivity
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
    #### VNet-to-VNet (e)
    - VNet-to-hub-hub-to-VNet (h)

# Architectures
- Secure hub, single region with routing intent
- Secure hub, single region with custom route tables, static routes, propagation and association
- Unsecured hub, single region with custom route tables, static routes, propagation and association
- Multi-hub, secured and secured or secured and unsecured or unsecured and unsecured, single region
- Cross-region, single vWAN, hub in one region (Spokes from remote region can connect to hub in another region)
- Cross-region, multiple hubs secured or unsecured

# Routing
- Hub Router
- BGP
- Concepts:
  - Hub RT: 1 or more routes
  - Hub Routing Intent & Policies: Traffic via Azure Firewall or Next-Gen Firewall NVA or SaaS solution in Hub
  - Connections: Routing configuration.
    - Connection Types:
      1.  VPN connection: Connects a VPN site to a virtual hub VPN gateway.
      2.  ExpressRoute connection: Connects an ExpressRoute circuit to a virtual hub ExpressRoute gateway.
      3.  P2S configuration connection: Connects a User VPN (Point-to-site) configuration to a virtual hub User VPN (Point-to-site) gateway.
      4.  Hub virtual network connection: Connects virtual networks to a virtual hub.
    - By default, all connections associate and propagate to the Default route table
- [Configure vHub routing](https://learn.microsoft.com/en-us/azure/virtual-wan/how-to-virtual-hub-routing)
  - RT:
    - Name, Route Name, Destination Type, Destination Prefix (aggregate e.g. VNet 1: 10.1.0.0/24 and VNet 2: 10.1.1.0/24 can be aggregated as 10.1.0.0/16), Next Hop, Next Hop IP
    - Labels (group RTs)
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

## Routing intent

- Simple, declarative routing policies to send Internet-bound and Private traffic to security solutions (e.g. Azure Firewall, NVA or Saa) solutions in vWAN hub
- It is **not possible** configure Routing Policies if hub isn't deployed with Azure Firewall, NVA or SaaS solution
- Routing Intent simplifies routing by managing route table associations and propagations for all connections (Vnet, S2S VPN, P2S VPN and ExpressRoute). VWANs with custom route tables and customized policies therefore can't be used with the Routing Intent constructs.
- Two types:
  Note: Max 1 of each/vWAN Hub i.e. a single next hop.
  1. Internet Traffic Routing Policy: vWAN advertises a default (0.0.0.0/0) route to all spokes, Gateways and Network Virtual Appliances (deployed in the hub or spoke)
  2. Private Traffic Routing Policy: Both branch and Vnet address prefixes
 - [Use cases and traffic flows](https://learn.microsoft.com/en-us/azure/virtual-wan/how-to-routing-policies#use-cases)
 - [Additional known limitations, considerations, prerequisites and rollback strategy](https://learn.microsoft.com/en-us/azure/virtual-wan/how-to-routing-policies#knownlimitations)
 - [Prefix advertisements to on-premises after routing intent is configured on a virtual hub](https://learn.microsoft.com/en-us/azure/virtual-wan/how-to-routing-policies#prefixadvertisments)

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
