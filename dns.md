# DNS Scenarios in Azure

## Microsoft built-in Azure DNS Service
- Vnet -> 168.63.129.129 -> Azure DNS

## Customer may also create a private DNS zone
- e.g. x.net
- Link to 1 or more Vnets
- Azure will automatically publish records in private zone as we create services in the linked Vnet e.g. private link

## Customer may also have custom DNS e.g. AD-Integrated DNS in Azure on a VM
- We then typically configure one or more Custom DNS IPs on the Vnet and that can then forward to 168.63.129.16

## Hybrid: Customer may also have DNS on-premises
- Problem 1: This on-premises DNS cannot forward to 168.63.129.16 as it only exists off Vnets in Azure
- Solution 1: Customer has to deply a DNS forwarder in Azure, which in turn can then forward to 168.63.129.16. Historically this was a couple of DNS servers on Azure VMs.
- Problem 2: What if we need Azure to forward DNS queries for on-premises zones?
- Underlying problem: DNS zones in Azure cannot be used as forwarders, and Azure DNS itself is only reachable from within an Azure Vnet.

### DNS Private Resolver addresses historical challenges
![DNS Private Resolver](https://learn.microsoft.com/en-us/azure/architecture/networking/architecture/_images/azure-dns-private-resolver-architecture.svg "DNS Private Resolver")
- Inbound Endpoints: On-premises to Azure Name Resolution Receive domain name resolution requests. Binds to a subnet. /28 min, /24 recommended.
  - On-premises DNS can now be configured to conditional forward to this inbound endpoint IP address in the inbound endpoint subnet e.g. privatelink.blob.core.windows.net. The private resolver can in turn forward to 168.63.129.16
- Outbound Endpoints: Azure to On-premises Name Resolution. Forward domain name resolution requests. Also binds to a subnet, and it has to be a **different subnet** from the inbound subnet. /28 min, /24 recommended.
  - Azure can use the IP address that this creates to forward to on-premises DNS servers
- Rulesets: Define conditional forwarding rules for DNS traffic using outbound endpoints
  - Links the ruleset to a Vnet e.g. A couple of DNS Servers that should be used to resolve contoso.com via the outbound endpoint
  - Hub-Spoke Topology:
    - Resolver may be in Hub, simply link spokes
    - Do we need peering? Not needed, a linked Vnet does not have to be peered for this to work as the path goes from Azure DNS, which knows about the private resolver and the ruleset.

  ### Note
  - Private Resolver is a regional resource. Vnets that are linked must be in the same region.
  - To forward ALL requests from Azure DNS to custom DNS where there is no matching private DNS zone linked add a **default rule** i.e. domain "." (no quotes)
  - The only private DNS zones resolved are those linked to the vnet where the DNS resolver is deployed

## Resources
[VWAN with Azure DNS Private Resolver](https://learn.microsoft.com/en-us/azure/architecture/networking/guide/private-link-virtual-wan-dns-single-region-workload "vWAn with Azure DNS Private Resolver")
[Guide to Private Link and DNS in Azure Virtual WAN](https://learn.microsoft.com/en-us/azure/architecture/networking/guide/private-link-virtual-wan-dns-guide)
