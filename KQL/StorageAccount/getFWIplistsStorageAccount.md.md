##  Storage Account KQL Query

* The below query will help you to get the list of storage accounts on which IP ranges/Public IP is allow to access from the internet or your on-premises networks to a storage account.

    **Note:** Mainly this option is available on storage accounts that have the public network access setting **Enabled from selected virtual networks and IP addresses**.

* You need to run the below query in Azure *Resource Graph Explorer* by selecting the appropriate scope of directory or subscription. 

```kql
resources
| where type == "microsoft.storage/storageaccounts"
| extend creationTime = tostring(properties.creationTime)
| extend StorageAccount_creationTime_YYY_MM_DD = tostring(split(creationTime,'T')[0])
| extend iprules = properties.networkAcls.ipRules
| mv-expand test = iprules
| project  name, type, location , resourceGroup, subscriptionId,StorageAccount_creationTime_YYY_MM_DD,valuedata = tostring(test["value"]), actiondata = tostring(test["action"])
```

* If you have multiple subscriptions and you want to pull details with the subscription name and specific tag details that are present on the subscription. 

    _**Example:** in the below scenario, I have pulled the details with the subscription name with some tag details like ( Owner, SupportLevel, etc)_

 ```kql
resources
| where type == "microsoft.storage/storageaccounts"
| extend creationTime = tostring(properties.creationTime)
| extend StorageAccount_creationTime_YYY_MM_DD = tostring(split(creationTime,'T')[0])
| extend iprules = properties.networkAcls.ipRules
| mv-expand test = iprules
| project  name, type, location , resourceGroup, subscriptionId,StorageAccount_creationTime_YYY_MM_DD,IPDeatils = tostring(test["value"]), Action = tostring(test["action"])
| join kind= inner (
resourcecontainers
| where type == "microsoft.resources/subscriptions"
| extend CustomerName = tostring(tags.CustomerName)
| extend Owner = tostring(tags.Owner)
| extend SupportLevel = tostring(tags.SupportLevel)
| project SubscriptionName=name, CustomerName,subscriptionId 
) on $left.subscriptionId == $right.subscriptionId
 ```
