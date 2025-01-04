## :star: Key Vault KQL Query

* The below query will help you to get the list of Key Vault on which IP ranges/Public IP is allow to access from the internet or your on-premises networks to a key vault.

    **Note:** Mainly this option is available on key vault that have the public network access setting **Enabled from selected virtual networks and IP addresses**.

* You need to run the below query in Azure *Resource Graph Explorer* by selecting the appropriate scope of directory or subscription. 

```kql
resources
| where type == "microsoft.keyvault/vaults"
| extend publicNetworkAccess = (properties.publicNetworkAccess)
| extend KVPricingTier = (properties.sku.name)
| extend iprules = (properties.networkAcls.ipRules)
| extend networkAcls = tostring(properties.networkAcls)
| where publicNetworkAccess == 'Enabled'
| mv-expand test = iprules
| extend IPDeatils = tostring(test["value"])
| project  name, type, location , resourceGroup, subscriptionId,publicNetworkAccess,KVPricingTier,iprules,IPDeatils = tostring(test["value"])
```
* If you have multiple subscriptions and you want to pull details with the subscription name and specific tag details that are present on the subscription. 

    _**Example:** in the below scenario, I have pulled the details with the subscription name with some tag details like ( Owner, SupportLevel, etc)_

```kql
resources
| where type == "microsoft.keyvault/vaults"
| extend publicNetworkAccess = (properties.publicNetworkAccess)
| extend KVPricingTier = (properties.sku.name)
| extend iprules = (properties.networkAcls.ipRules)
| extend networkAcls = tostring(properties.networkAcls)
| where publicNetworkAccess == 'Enabled'
| mv-expand test = iprules
| extend IPDeatils = tostring(test["value"])
| project  name, type, location , resourceGroup, subscriptionId,publicNetworkAccess,KVPricingTier,iprules,IPDeatils = tostring(test["value"])
| join kind= inner (
resourcecontainers
| where type == "microsoft.resources/subscriptions"
| extend CustomerName = tostring(tags.CustomerName)
| extend Owner = tostring(tags.Owner)
| extend SupportLevel = tostring(tags.SupportLevel)
| project SubscriptionName=name, CustomerName,subscriptionId
) on $left.subscriptionId == $right.subscriptionId
```