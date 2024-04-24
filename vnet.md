# Flow Logs
## [VNET Flow Logs - GA (April 2024)](https://learn.microsoft.com/en-us/azure/network-watcher/vnet-flow-logs-overview)
- Logging at the VNET level, as opposed to at the subnet and NIC level with NSG Flow Logs
- All supported workloads within a VNET are recorded
- In addition to existing support to identify traffic that network security group rules allow or deny, VNet flow logs support identification of traffic that Azure Virtual Network Manager security admin rules allow or deny. VNet flow logs also support evaluating the encryption status of your network traffic in scenarios where you're using virtual network encryption.
- Feature of Azure Network Watcher
- Use them to log information about IP traffic flowing through a VNET
- Flow logs are the source of truth for all network activity
- Flow data from VNet flow logs is sent to Azure Storage. From there, you can access the data and export it to any visualization tool, security information and event management (SIEM) solution, or intrusion detection system (IDS). VNet flow logs overcome some of the limitations of NSG flow logs
# [Topology](https://github.com/Azure/fta-networking/blob/main/doc/topology.md)
[![hub-spoke](https://github.com/Azure/fta-networking/raw/main/png/topology-210726.png)](https://github.com/Azure/fta-networking/raw/main/png/topology-210726.png)

# [Bandwidth](https://azure.microsoft.com/en-us/pricing/details/bandwidth/)
- Ingress Free
- [Egress $](https://azure.microsoft.com/en-us/pricing/details/bandwidth/)

# [Encryption](https://learn.microsoft.com/en-us/azure/virtual-network/virtual-network-encryption-overview)
- Azure VNET encryption is not enabled by default
- Traffic to unsupported VMs is unencrypted. Use VNET Flow Logs to confirm flow encryption between VMs
- [Enable encryption](https://learn.microsoft.com/en-us/azure/virtual-network/how-to-create-encryption-portal#enable-encryption)

# Secure network access to PaaS services
## Service Endpoint
- Adding service endpoints doesn't remove the public endpoint. It simply provides a redirection of traffic.
- To enable a service endpoint, you must:
  - Turn off public access to the service.
  - Add the service endpoint to a virtual network.
[![service endpoint](https://learn.microsoft.com/en-us/training/modules/secure-and-isolate-with-nsg-and-service-endpoints/media/4-service-endpoint.svg)](https://learn.microsoft.com/en-us/training/modules/secure-and-isolate-with-nsg-and-service-endpoints/media/4-service-endpoint.svg)
- Service resources that you've secured by using virtual network service endpoints are not, by default, accessible from on-premises networks. To access resources from an on-premises network, use NAT IPs. If you use ExpressRoute for connectivity from on-premises to Azure, you have to identify the NAT IP addresses ExpressRoute uses. By default, each circuit uses two NAT IP addresses to connect to the Azure backbone network. You then need to add these IP addresses into the Azure service resource's IP firewall configuration (for example, Azure Storage).
- The following diagram shows how you can use a service endpoint and firewall configuration to enable on-premises devices to access Azure Storage resources:
[![servce endpoints and hybrid](https://learn.microsoft.com/en-us/training/modules/secure-and-isolate-with-nsg-and-service-endpoints/media/4-service-endpoint-flow.svg)](https://learn.microsoft.com/en-us/training/modules/secure-and-isolate-with-nsg-and-service-endpoints/media/4-service-endpoint-flow.svg)
