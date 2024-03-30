# Flow Logs
## [VNET Flow Logs - Preview (March 2024)](https://learn.microsoft.com/en-us/azure/network-watcher/vnet-flow-logs-overview)
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

# [VNET Encryption](https://learn.microsoft.com/en-us/azure/virtual-network/virtual-network-encryption-overview)
- Traffic to unsupported VMs is unencrypted. Use VNET Flow Logs to confirm flow encryption between VMs
- [Enable encryption](https://learn.microsoft.com/en-us/azure/virtual-network/how-to-create-encryption-portal#enable-encryption)
