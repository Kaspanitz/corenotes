# Virtual WAN
Last update: 9 July 2024
- Central operational interface for networking, routing and security
- Hub-Spoke architecture
- Scale
- Performance
- [Global transit network architecture](https://learn.microsoft.com/en-us/azure/virtual-wan/virtual-wan-global-transit-network-architecture)
  - 'Hub' enables transitive connectivity between endpoints that might be distributed across different types of 'spokes' e.g.
    - Virtual network (VNets)
    - Physical branch site
    - Remote user
    - Internet
  - [Any-to-any](https://learn.microsoft.com/en-us/azure/virtual-wan/virtual-wan-global-transit-network-architecture#anytoany)
    - Less complex to manage than full mesh networks
    - Examples via transit hub(s):
      - Branch-to-VNet (a)
        - Explicit gateway transit not required - vWAN enables automatic gateway transit
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
      - Branch-to-branch (b)
      - Remote User-to-VNet (c)
      - Remote User-to-branch (d)
      - VNet-to-VNet (e)
      - Branch-to-hub-hub-to-Branch (f)
      - Branch-to-hub-hub-to-VNet (g)
      - VNet-to-hub-hub-to-VNet (h)
      - ExpressRoute Global Reach and Virtual WAN
        - Private communication between on-premises networks across different locations using ExpressRoute circuits and Microsoft’s global network
        ![anytoany](https://learn.microsoft.com/en-us/azure/virtual-wan/media/virtual-wan-global-transit-network-architecture/any-any.png "anytoany")

## Features
- Branch connectivity:
  - Virtual WAN Partner devices (e.g. SD-WAN or VPN CPE)
  - Site-to-site VPN connectivity (S2S)
  - Private connectivity (ExpressRoute)
  - VPN ExpressRoute inter-connectivity
- User connectivity:
  - Remote user VPN connectivity (P2S)
- Intra-cloud connectivity:
  - transitive connectivity for Vnets
  - cross-region VNet connection
    - Single Hub:
      - vWAN hub in region with most spokes (branches, VNets, users)
      - Connect spokes in other regions to the hub e.g. VNet in one region can be connected to a vWAN hub in a different region
      - Good option when an enterprise footprint is mostly in one region with a few remote spokes
        ![1hub](https://learn.microsoft.com/en-us/azure/virtual-wan/media/virtual-wan-global-transit-network-architecture/transit-network.png "1vwan")
    - Hub-to-hub:
      - When optimal (latency-wise) to access the cloud from a region closest to physical site and users
      - When multiple hubs are enabled in a single vWAN, the hubs are automatically interconnected via hub-to-hub links, thus enabling global connectivity between branches and Vnets that are distributed across multiple regions.
      - Hubs in same vWAN, can be associated with different regional access and security policies
        ![multiplehubs](https://learn.microsoft.com/en-us/azure/virtual-wan/media/virtual-wan-global-transit-network-architecture/cross-region.png "2vwan")
- Routing, Azure Firewall, and encryption for private connectivity
