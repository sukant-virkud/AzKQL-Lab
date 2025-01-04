## :star: Route Table KQL Query

* The below query will help you to get the list of  route table rules and further you can export it.

* You need to run the below query in Azure *Resource Graph Explorer* by selecting the appropriate scope of directory or subscription and download the result in CSV format.

```kql
resources
| where type == "microsoft.network/routetables"
| where name == " " //Enter the Name of RT that you want to export the details
| mv-expand rtrules = properties.routes
| extend routeTableName = tostring(name)
| extend rtRuleName = tostring(rtrules.name)
| extend rtAddressPrefix = tostring(rtrules.properties.addressPrefix)
| extend rtNextHopType = tostring(rtrules.properties.nextHopType)
| extend rtNextHopIpAddress = tostring(rtrules.properties.nextHopIpAddress)
| project routeTableName, resourceGroup, subscriptionId, rtRuleName, rtAddressPrefix, rtNextHopType, rtNextHopIpAddress
| order by ['rtNextHopType'] asc
```