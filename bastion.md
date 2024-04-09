# Overview
- Supports VNET peering
  - Virtual network peering: Connect virtual networks within the same Azure region.
  - Global virtual network peering: Connecting virtual networks across Azure regions.
- Azure Bastion deployment is per virtual network, not per subscription/account or virtual machine.
- [Azure Bastion is billed per hour and the billing starts when the resource gets created](https://azure.microsoft.com/en-us/pricing/details/azure-bastion/) Currently, there is no feature to “shutdown” or “disable” an Azure Bastion host to save costs.
- To stop the charges, you will need to delete the Azure Bastion resource. You can use the following command to delete the Azure Bastion host: az network bastion delete --name MyBastionHost --resource-group MyResourceGroup
- If you only need to use Azure Bastion at particular times of the day, you could automate creation and deletion of the Azure Bastion host. E.g. a pair of Logic Apps

# Architecture

[![Bastion](https://learn.microsoft.com/en-us/azure/bastion/media/vnet-peering/design.png)](https://learn.microsoft.com/en-us/azure/bastion/media/vnet-peering/design.png)
