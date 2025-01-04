## :star: Network Security Groups KQL Query

* The below query will help you to get the list of  networksecuritygroup rules and further you can export it.

* You need to run the below query in Azure *Resource Graph Explorer* by selecting the appropriate scope of directory or subscription and download the result in CSV format.

```kql
resources
| where type == "microsoft.network/networksecuritygroups"
| where name == ' ' // Enter the Name of NSG rule that you want to export the details
| mv-expand nsgrules = properties.securityRules
| extend nsgRuleNaame = tostring(nsgrules.name)
| extend nsgRuleDirection = tostring(nsgrules.properties.direction)
| extend nsgRulePriority = tostring(nsgrules.properties.priority)
| extend nsgRuleSourceAddressPrefix = tostring(nsgrules.properties.sourceAddressPrefix)
| extend nsgRuleSourcePortRange = tostring(nsgrules.properties.sourcePortRange)
| extend nsgRuleDestinationAddressPrefix = tostring(nsgrules.properties.destinationAddressPrefix)
| extend nsgRuleDestinationPortRange = tostring(nsgrules.properties.destinationPortRange)
| extend nsgRuleProtocol = tostring(nsgrules.properties.protocol)
| extend nsgRuleAccess = tostring(nsgrules.properties.access)
| project nsgName=name,resourceGroup,nsgRulePriority, nsgRuleDirection,nsgRuleNaame, nsgRuleSourceAddressPrefix, nsgRuleSourcePortRange, nsgRuleDestinationAddressPrefix, nsgRuleDestinationPortRange, nsgRuleProtocol, nsgRuleAccess
| order by ['nsgRuleDirection'] asc
```