# Virtual WAN
- Central operational interface for networking, routing and security
- Hub-Spoke any-to-any architecture
- Scale
- Performance
- Global transit network architecture
  - 'Hub' enables transitive connectivity between endpoints that might be distributed across different types of 'spokes' e.g.
    - Virtual network (VNets)
    - Physical branch site
    - Remote user
    - Internet

## Features
- Branch connectivity:
  - Virtual WAN Partner devices (e.g. SD-WAN or VPN CPE)
  - Site-to-site VPN connectivity (S2S)
  - Private connectivity (ExpressRoute)
  - VPN ExpressRoute inter-connectivity
- User connectivity:
  - Remote user VPN connectivity (P2S)
- Intra-cloud connectivity:
  - transitive connectivity for VNETs
  - cross-region VNet connection
    - VNet in one region can be connected to a vWAN hub in a different region
    - Establish a single vWAN hub in the region with most spokes (branches, VNets, users), then connecting spokes in other regions to the hub
      (good option when an enterprise footprint is mostly in one region with a few remote spokes)
- Routing, Azure Firewall, and encryption for private connectivity
