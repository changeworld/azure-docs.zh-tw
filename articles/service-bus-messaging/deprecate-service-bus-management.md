---
title: Azure 訊息服務-Service Manager 至 Resource Manager
description: 本文提供已淘汰的 Azure Service Manager REST API & PowerShell Cmdlet 到 Resource Manager REST API & PowerShell Cmdlet 的對應。
ms.topic: article
ms.date: 09/25/2020
ms.openlocfilehash: 84335d534c229f40381b913dfca54f42e2da250b
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91761170"
---
# <a name="deprecation-of-azure-service-manager-support-for-azure-service-bus-relay-and-event-hubs"></a>淘汰 Azure Service Manager Azure 服務匯流排、轉送和事件中樞的支援

Resource Manager，我們新一代的雲端基礎結構堆疊，將會完全取代 (傳統部署模型) 的「傳統」 Azure 服務管理模型。 因此，傳統部署模型 REST Api 和服務匯流排、轉送和事件中樞的支援將于2021年11月1日淘汰。 這項淘汰最初是在 [Microsoft Tech 共同體公告](https://techcommunity.microsoft.com/t5/Service-Bus-blog/Deprecating-Service-Management-support-for-Azure-Service-Bus/ba-p/370909)上宣佈，但我們最近決定將淘汰期間從原始公告的時間延長為兩年。 為了方便識別，這些 Api 具有 `management.core.windows.net` 其 URI。 請參閱下表，以取得您現在應該使用的已淘汰 Api 及其 Azure Resource Manager API 版本的清單。

若要繼續使用服務匯流排、轉送和事件中樞，請于2021年10月31日前移至 Resource Manager。 我們鼓勵仍在使用舊 Api 的所有客戶立即進行切換，以利用 Resource Manager 的額外優點，其中包括資源群組、標籤、簡化的部署和管理程式，以及使用角色型存取控制的更精細存取控制 (RBAC) 。

如需有關 Azure Resource Manager 與 Azure Service Manager 的詳細資訊，請參閱 [TechNet Blog](https://blogs.technet.microsoft.com/meamcs/2016/12/22/difference-between-azure-service-manager-and-azure-resource-manager/)。

如需 Azure 服務匯流排、轉送和事件中樞 Service Manager 和 Resource Manager Api 的詳細資訊，請參閱我們的 REST API 檔：

- [Azure 服務匯流排](/rest/api/servicebus/)
- [Azure 事件中樞](/rest/api/eventhub/)
- [Azure 轉送](/rest/api/relay/)

## <a name="service-manager-rest-api---resource-manager-rest-api"></a>Service Manager REST API-Resource Manager REST API

| Service Manager Api (已淘汰)  | Resource Manager-服務匯流排 API | Resource Manager-事件中樞 API | Resource Manager 轉送 API |
| --------------- | ----------------- | ----------------- | ----------------- | 
| **命名空間-GetNamespaceAsync** <br/>[服務匯流排取得命名空間](/rest/api/servicebus/get-namespace)<br/>[事件中樞取得命名空間](/rest/api/eventhub/get-event-hub)<br/>[轉送取得命名空間](/rest/api/servicebus/get-relays)<br/> ```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [get](/rest/api/servicebus/stable/namespaces/get) | [get](/rest/api/eventhub/2017-04-01/namespaces/get) | [get](/rest/api/relay/namespaces/get) |
| **ConnectionDetails-GetConnectionDetails**<br/>服務匯流排/事件中樞/轉送 GetConnectionDetals<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/ConnectionDetails``` | [listkeys](/rest/api/servicebus/stable/namespaces%20-%20authorization%20rules/listkeys) | [listkeys](/rest/api/eventhub/2017-04-01/authorization%20rules%20-%20event%20hubs/listkeys) | [listkeys](/rest/api/relay/namespaces/listkeys) |
| **主題-GetTopicsAsync**<br/>服務匯流排<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/topics? $skip={skip}&$top={top}``` | list | &nbsp; | &nbsp; | 
| **佇列-GetQueueAsync** <br/>服務匯流排<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/queues/{queueName}``` | [get](/rest/api/servicebus/stable/queues/get) | &nbsp; | &nbsp; | 
| **轉送-GetRelaysAsync**<br/>[取得轉送](/rest/api/servicebus/get-relays)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/relays? $skip={skip}&$top={top}```| &nbsp; | &nbsp; | list | 
| **NamespaceAuthorizationRules-GetNamespaceAuthorizationRuleAsync**<br/>服務匯流排/事件中樞/轉送 GetNamespaceAuthRule<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/authorizationrules?``` | [getauthorizationrule](/rest/api/servicebus/stable/namespaces%20-%20authorization%20rules/getauthorizationrule) | [getauthorizationrule](/rest/api/eventhub/2017-04-01/authorization%20rules%20-%20namespaces/getauthorizationrule) | [getauthorizationrule](/rest/api/relay/namespaces/getauthorizationrule) |
| **命名空間-DeleteNamespaceAsync**<br/>[服務匯流排刪除命名空間](/rest/api/servicebus/delete-namespace)<br/>[事件中樞刪除命名空間](/rest/api/eventhub/delete-event-hub)<br/>[轉送刪除命名空間](/rest/api/servicebus/delete-namespace)<br/> ```DELETE  https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [delete](/rest/api/servicebus/stable/namespaces/delete) | [delete](/rest/api/eventhub/2017-04-01/namespaces/delete) | [delete](/rest/api/relay/namespaces/delete) | 
| **MessagingSKUPlan-GetPlanAsync**<br/>服務匯流排/事件中樞/轉送取得命名空間<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/MessagingPlan``` | [get](/rest/api/servicebus/stable/namespaces/get) | [get](/rest/api/eventhub/2017-04-01/namespaces/get) | [get](/rest/api/relay/namespaces/get) |
| **MessagingSKUPlan-UpdatePlanAsync**<br/>服務匯流排/事件中樞/轉送取得命名空間<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/MessagingPlan``` | [createorupdate](/rest/api/servicebus/stable/namespaces/createorupdate) | [createorupdate](/rest/api/eventhub/2017-04-01/namespaces/createorupdate) | [createorupdate](/rest/api/relay/namespaces/createorupdate) |
| **NamespaceAuthorizationRules-UpdateNamespaceAuthorizationRuleAsync**<br/>服務匯流排/事件中樞/轉送取得命名空間<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules/{rule name}``` | [createorupdate](/rest/api/servicebus/stable/namespaces/createorupdate) | [createorupdateauthorizationrule](/rest/api/eventhub/2017-04-01/authorization%20rules%20-%20event%20hubs/createorupdateauthorizationrule) | [createorupdateauthorizationrule](/rest/api/relay/namespaces/createorupdateauthorizationrule) | 
| **NamespaceAuthorizationRules-CreateNamespaceAuthorizationRuleAsync**<br/> 
服務匯流排/事件中樞/轉送<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules/{rule name}``` |[createorupdate](/rest/api/servicebus/stable/namespaces/createorupdate) | [createorupdateauthorizationrule](/rest/api/eventhub/2017-04-01/authorization%20rules%20-%20event%20hubs/createorupdateauthorizationrule) | [createorupdateauthorizationrule](/rest/api/relay/namespaces/createorupdateauthorizationrule) |
| **NamespaceProperties-GetNamespacePropertiesAsync**<br/>[服務匯流排取得命名空間](/rest/api/servicebus/get-namespace)<br/>[事件中樞取得命名空間](/rest/api/eventhub/get-event-hub)<br/>[轉送取得命名空間](/rest/api/servicebus/get-relays)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [get](/rest/api/servicebus/stable/namespaces/get) | [get](/rest/api/eventhub/2017-04-01/namespaces/get) | [get](/rest/api/relay/namespaces/get) |
| **RegionCodes-GetRegionCodesAsync**<br/>服務匯流排/EventHub/轉送取得命名空間<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [listbysku](/rest/api/servicebus/stable/regions/listbysku) | [listbysku](/rest/api/eventhub/2017-04-01/regions/listbysku) | &nbsp; | 
| **NamespaceProperties-UpdateNamespacePropertyAsync**<br/>服務匯流排/EventHub/轉送<br/>```GET    https://management.core.windows.net/{subscription ID}/services/ServiceBus/Regions/``` | [createorupdate](/rest/api/servicebus/stable/namespaces/createorupdate) | [createorupdate](/rest/api/eventhub/2017-04-01/namespaces/createorupdate) | [createorupdate](/rest/api/relay/namespaces/createorupdate) |
| **EventHubsCrud-ListEventHubsAsync**<br/>[列出事件中樞](/rest/api/eventhub/list-event-hubs)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/eventhubs?$skip={skip}&$top={top}``` | &nbsp; | list | &nbsp; | 
| **EventHubsCrud-GetEventHubAsync**<br/>[取得事件中樞](/rest/api/eventhub/get-event-hub)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/eventhubs/{eventHubPath}``` | &nbsp; | [get](/rest/api/eventhub/get-event-hub) | &nbsp; | 
| **NamespaceAuthorizationRules-DeleteNamespaceAuthorizationRuleAsync**<br/>服務匯流排/事件中樞/轉送<br/>```DELETE https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules/{rule name}``` | [deleteauthorizationrule](/rest/api/servicebus/stable/namespaces%20-%20authorization%20rules/deleteauthorizationrule) | [deleteauthorizationrule](/rest/api/eventhub/2017-04-01/authorization%20rules%20-%20namespaces/deleteauthorizationrule) | [deleteauthorizationrule](/rest/api/relay/namespaces/deleteauthorizationrule) |
| **NamespaceAuthorizationRules-GetNamespaceAuthorizationRulesAsync**<br/>服務匯流排/EventHub/轉送<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules``` | [listauthorizationrules](/rest/api/relay/namespaces/listauthorizationrules) | [listauthorizationrules](/rest/api/eventhub/2017-04-01/authorization%20rules%20-%20namespaces/listauthorizationrules) | [listauthorizationrules](/rest/api/relay/namespaces/listauthorizationrules) |
| **NamespaceAvailability-IsNamespaceAvailable**<br/>[服務匯流排命名空間可用性](/rest/api/servicebus/check-namespace-availability)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/CheckNamespaceAvailability/?namespace=<namespaceValue>``` | [checknameavailability](/rest/api/servicebus/stable/namespaces%20-%20checkname%20availability/checknameavailability) | [checknameavailability](/rest/api/eventhub/2017-04-01/check%20name%20availability%20-%20namespaces/checknameavailability) | [checknameavailability](/rest/api/relay/namespaces/checknameavailability) |
| **命名空間-CreateOrUpdateNamespaceAsync**<br/>服務匯流排/事件中樞/轉送<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [createorupdate](/rest/api/servicebus/stable/namespaces/createorupdate) | [createorupdate](/rest/api/eventhub/2017-04-01/namespaces/createorupdate) | [createorupdate](/rest/api/relay/namespaces/createorupdate) | 
| **主題-GetTopicAsync**<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/topics/{topicPath}``` | [get](/rest/api/servicebus/stable/topics/get) | &nbsp; | &nbsp; |

## <a name="service-manager-powershell---resource-manager-powershell"></a>Service Manager PowerShell-Resource Manager PowerShell
| Service Manager PowerShell 命令 (已淘汰)  | 新的 Resource Manager 命令 | 較新的 Resource Manager 命令 |
| ----- | ----- | ----- | 
| [Get-AzureSBAuthorizationRule](/powershell/module/servicemanagement/azure.service/get-azuresbauthorizationrule) | [Get-AzureRmServiceBusAuthorizationRule](/powershell/module/azurerm.servicebus/get-azurermservicebusauthorizationrule) | [AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/get-azservicebusauthorizationrule) |
| [Get-AzureSBLocation](/powershell/module/servicemanagement/azure.service/get-azuresblocation) | [AzureRmServiceBusGeoDRConfiguration](/powershell/module/azurerm.servicebus/get-azurermservicebusgeodrconfiguration) | [AzServiceBusGeoDRConfiguration](/powershell/module/az.servicebus/get-azservicebusgeodrconfiguration) |
| [Get-AzureSBNamespace](/powershell/module/servicemanagement/azure.service/get-azuresbnamespace) | [Get-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/get-azurermservicebusnamespace) | [AzServiceBusNamespace](/powershell/module/az.servicebus/get-azservicebusnamespace) |
| [New-AzureSBAuthorizationRule](/powershell/module/servicemanagement/azure.service/new-azuresbauthorizationrule) | [New-AzureRmServiceBusAuthorizationRule](/powershell/module/azurerm.servicebus/new-azurermservicebusauthorizationrule) | [新 AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/new-azservicebusauthorizationrule) |
| [New-AzureSBNamespace](/powershell/module/servicemanagement/azure.service/new-azuresbnamespace) | [New-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/new-azurermservicebusnamespace) | [新 AzServiceBusNamespace](/powershell/module/az.servicebus/new-azservicebusnamespace) |
| [移除-AzureRmRelayAuthorizationRule](/powershell/module/azurerm.relay/remove-azurermrelayauthorizationrule) | [Remove-AzureRmEventHubAuthorizationRule](/powershell/module/azurerm.eventhub/remove-azurermeventhubauthorizationrule) | [移除-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/remove-azservicebusauthorizationrule) |
| [Remove-AzureSBNamespace](/powershell/module/servicemanagement/azure.service/remove-azuresbnamespace) | [Remove-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/remove-azurermservicebusnamespace) | [移除-AzServiceBusNamespace](/powershell/module/az.servicebus/remove-azservicebusnamespace) |
| [Set-AzureSBAuthorizationRule](/powershell/module/servicemanagement/azure.service/set-azuresbauthorizationrule) | [Set-AzureRmServiceBusAuthorizationRule](/powershell/module/azurerm.servicebus/set-azurermservicebusauthorizationrule) | [設定-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/set-azservicebusauthorizationrule) |

## <a name="next-steps"></a>後續步驟
請參閱下列文件： 

- 最新 REST API 檔
    - [Azure 服務匯流排](/rest/api/servicebus/)
    - [Azure 事件中樞](/rest/api/eventhub/)
    - [Azure 轉送](/rest/api/relay/)
- 最新的 PowerShell 檔
    - [Azure 服務匯流排](/powershell/module/azurerm.servicebus/#service_bus)
    - [Azure 事件中樞](/powershell/module/azurerm.eventhub/#event_hub)
    - [事件格線](/powershell/module/azurerm.eventgrid/#event_grid)
