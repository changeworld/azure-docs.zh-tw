---
title: Azure 消息傳遞服務 - 資源管理器的服務管理員
description: 本文提供已棄用的 Azure 服務管理員 REST API & PowerShell Cmdlet 映射到資源管理器 REST API & PowerShell Cmdlet。
services: service-bus-messaging, event-hubs, event-grid
documentationcenter: na
author: spelluru
editor: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/22/2020
ms.author: spelluru
ms.openlocfilehash: d263381667319b98a28ee6168e2de75c4041b58a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77589905"
---
# <a name="deprecation-of-azure-service-manager-support-for-azure-service-bus-relay-and-event-hubs"></a>棄用 Azure 服務匯流排、中繼和事件中心 Azure 服務管理員支援

資源管理器，我們的下一代雲基礎架構堆疊，正在完全取代"經典"Azure 服務管理模式（經典部署模型）。 因此，經典部署模型 REST API 和對服務匯流排、中繼和事件中心的支援將于 2021 年 11 月 1 日停用。 這種棄用首次在[微軟技術社區公告](https://techcommunity.microsoft.com/t5/Service-Bus-blog/Deprecating-Service-Management-support-for-Azure-Service-Bus/ba-p/370909)上宣佈，但我們最近決定從最初宣佈的時間再延長兩年的棄用期。 為了便於識別，這些 API`management.core.windows.net`在其 URI 中具有。 有關棄用 API 及其 Azure 資源管理器 API 版本的清單，請參閱下表，現在應使用這些 API 及其 Azure 資源管理器 API 版本。

要繼續使用服務匯流排、中繼和事件中心，請于 2021 年 10 月 31 日前轉到資源管理器。 我們鼓勵仍在使用舊 API 的所有客戶儘快進行切換，以利用資源管理器的額外優勢，包括資源分組、標記、簡化的部署和管理流程以及細細微性訪問使用基於角色的存取控制 （RBAC） 進行控制。

有關 Azure 資源管理器與 Azure 服務管理員的詳細資訊，請參閱[TechNet 博客](https://blogs.technet.microsoft.com/meamcs/2016/12/22/difference-between-azure-service-manager-and-azure-resource-manager/)。

有關 Azure 服務匯流排、中繼和事件中心的服務管理員和資源管理器 API 的詳細資訊，請參閱 REST API 文檔：

- [Azure 服務匯流排](/rest/api/servicebus/)
- [Azure 事件中心](/rest/api/eventhub/)
- [Azure 轉送](/rest/api/relay/)

## <a name="service-manager-rest-api---resource-manager-rest-api"></a>服務管理員 REST API - 資源管理器 REST API

| 服務管理員 API（已棄用） | 資源管理器 - 服務匯流排 API | 資源管理器 - 事件中心 API | 資源管理器 - 中繼 API |
| --------------- | ----------------- | ----------------- | ----------------- | 
| **命名空間-獲取命名空間同步** <br/>[服務匯流排獲取命名空間](/rest/api/servicebus/get-namespace)<br/>[事件中心獲取命名空間](/rest/api/eventhub/get-event-hub)<br/>[中繼獲取命名空間](/rest/api/servicebus/get-relays)<br/> ```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [get](/rest/api/servicebus/namespaces/get) | [get](/rest/api/eventhub/namespaces/get) | [get](/rest/api/relay/namespaces/get) |
| **連接詳細資訊 -獲取連接詳細資訊**<br/>服務匯流排/事件集線器/繼電器接線<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/ConnectionDetails``` | [listkeys](/rest/api/servicebus/namespaces/listkeys) | [listkeys](/rest/api/eventhub/namespaces/listkeys) | [listkeys](/rest/api/relay/namespaces/listkeys) |
| **主題-獲取主題同步**<br/>服務匯流排<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/topics? $skip={skip}&$top={top}``` | [清單](/rest/api/servicebus/topics/listbynamespace) | &nbsp; | &nbsp; | 
| **佇列-獲取佇列同步** <br/>服務匯流排<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/queues/{queueName}``` | [get](/rest/api/servicebus/queues/get) | &nbsp; | &nbsp; | 
| **繼電器-獲取中繼同步**<br/>[獲取繼電器](/rest/api/servicebus/get-relays)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/relays? $skip={skip}&$top={top}```| &nbsp; | &nbsp; | [清單](/rest/api/relay/wcfrelays/listbynamespace) | 
| **命名空間授權規則-獲取命名空間授權規則Async**<br/>服務匯流排/事件集線器/中繼獲取命名空間 Auth 規則<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/authorizationrules?``` | [獲取授權規則](/rest/api/servicebus/namespaces/getauthorizationrule) |[獲取授權規則](/rest/api/eventhub/namespaces/getauthorizationrule) | [獲取授權規則](/rest/api/relay/namespaces/getauthorizationrule) |
| **命名空間-刪除命名空間同步**<br/>[服務匯流排刪除命名空間](/rest/api/servicebus/delete-namespace)<br/>[事件中心刪除命名空間](/rest/api/eventhub/delete-event-hub)<br/>[中繼 刪除命名空間](/rest/api/servicebus/delete-namespace)<br/> ```DELETE  https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [刪除](/rest/api/servicebus/namespaces/delete) | [刪除](/rest/api/eventhub/namespaces/delete) | [刪除](/rest/api/relay/namespaces/delete) | 
| **消息庫平面-獲取平面同步**<br/>服務匯流排/事件集線器/中繼獲取命名空間<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/MessagingPlan``` | [get](/rest/api/servicebus/namespaces/get) | [get](/rest/api/eventhub/namespaces/get) | [get](/rest/api/relay/namespaces/get) |
| **消息庫計畫-更新計畫同步**<br/>服務匯流排/事件集線器/中繼獲取命名空間<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/MessagingPlan``` | [創建器更新](/rest/api/servicebus/namespaces/createorupdate) | [創建器更新](/rest/api/eventhub/namespaces/createorupdate) | [創建器更新](/rest/api/relay/namespaces/createorupdate) |
| **命名空間授權規則-更新命名空間授權規則Async**<br/>服務匯流排/事件集線器/中繼獲取命名空間<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules/{rule name}``` | [創建器更新](/rest/api/servicebus/namespaces/createorupdate) | [創建或更新授權規則](/rest/api/eventhub/namespaces/createorupdateauthorizationrule) | [創建或更新授權規則](/rest/api/relay/namespaces/createorupdateauthorizationrule) | 
| **命名空間授權規則-創建命名空間授權規則Async**<br/> 
服務匯流排/事件集線器/中繼<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules/{rule name}``` |[創建器更新](/rest/api/servicebus/namespaces/createorupdate) | [創建或更新授權規則](/rest/api/eventhub/namespaces/createorupdateauthorizationrule) | [創建或更新授權規則](/rest/api/relay/namespaces/createorupdateauthorizationrule) |
| **命名空間屬性-獲取命名空間屬性同步**<br/>[服務匯流排獲取命名空間](/rest/api/servicebus/get-namespace)<br/>[事件中心獲取命名空間](/rest/api/eventhub/get-event-hub)<br/>[中繼獲取命名空間](/rest/api/servicebus/get-relays)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [get](/rest/api/servicebus/namespaces/get) | [get](/rest/api/eventhub/namespaces/get) | [get](/rest/api/relay/namespaces/get) |
| **區功能變數代碼-獲取區功能變數代碼同步**<br/>服務匯流排/事件中心/中繼獲取命名空間<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [列斯特比斯庫](/rest/api/servicebus/regions/listbysku) | [列斯特比斯庫](/rest/api/eventhub/regions/listbysku) | &nbsp; | 
| **命名空間屬性-更新命名空間屬性同步**<br/>服務匯流排/事件中心/中繼<br/>```GET    https://management.core.windows.net/{subscription ID}/services/ServiceBus/Regions/``` | [創建器更新](/rest/api/servicebus/namespaces/createorupdate) | [創建器更新](/rest/api/eventhub/namespaces/createorupdate) | [創建器更新](/rest/api/relay/namespaces/createorupdate) |
| **事件中心克魯德-清單事件中心同步**<br/>[列出事件中心](/rest/api/eventhub/list-event-hubs)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/eventhubs?$skip={skip}&$top={top}``` | &nbsp; | [清單](/rest/api/eventhub/eventhubs/listbynamespace) | &nbsp; | 
| **事件中心Crud-獲取事件Hubasync**<br/>[獲取事件中心](/rest/api/eventhub/get-event-hub)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/eventhubs/{eventHubPath}``` | &nbsp; | [get](/rest/api/eventhub/eventhubs/get) | &nbsp; | 
| **命名空間授權規則-刪除命名空間授權規則Async**<br/>服務匯流排/事件集線器/中繼<br/>```DELETE https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules/{rule name}``` | [刪除授權規則](/rest/api/servicebus/namespaces/deleteauthorizationrule) | [刪除授權規則](/rest/api/eventhub/namespaces/deleteauthorizationrule) | [刪除授權規則](/rest/api/relay/namespaces/deleteauthorizationrule) |
| **命名空間授權規則-獲取命名空間授權規則Async**<br/>服務匯流排/事件中心/中繼<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules``` | [清單授權規則](/rest/api/servicebus/namespaces/listauthorizationrules) | [清單授權規則](/rest/api/eventhub/namespaces/listauthorizationrules) | [清單授權規則](/rest/api/relay/namespaces/listauthorizationrules) |
| **命名空間可用性 -正名空間可用**<br/>[服務匯流排命名空間可用性](/rest/api/servicebus/check-namespace-availability)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/CheckNamespaceAvailability/?namespace=<namespaceValue>``` | [checknameavailability](/rest/api/servicebus/namespaces/checknameavailability) | [checknameavailability](/rest/api/eventhub/namespaces/checknameavailability) | [checknameavailability](/rest/api/relay/namespaces/checknameavailability) |
| **命名空間-創建或更新命名空間同步**<br/>服務匯流排/事件集線器/中繼<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [創建器更新](/rest/api/servicebus/namespaces/createorupdate) | [創建器更新](/rest/api/eventhub/namespaces/createorupdate) | [創建器更新](/rest/api/relay/namespaces/createorupdate) | 
| **主題-獲取主題同步**<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/topics/{topicPath}``` | [get](/rest/api/servicebus/topics/get) | &nbsp; | &nbsp; |

## <a name="service-manager-powershell---resource-manager-powershell"></a>服務管理員 PowerShell - 資源管理器 PowerShell
| 服務管理員 PowerShell 命令（已棄用） | 新的資源管理器命令 | 更新資源管理器命令 |
| ----- | ----- | ----- | 
| [Get-AzureSBAuthorizationRule](/powershell/module/servicemanagement/azure/get-azuresbauthorizationrule?view=azuresmps-4.0.0) | [Get-AzureRmServiceBusAuthorizationRule](/powershell/module/azurerm.servicebus/get-azurermservicebusauthorizationrule?view=azurermps-6.13.0) | [獲取服務匯流排授權規則](/powershell/module/az.servicebus/get-azservicebusauthorizationrule?view=azps-1.6.0) |
| [Get-AzureSBLocation](/powershell/module/servicemanagement/azure/get-azuresblocation?view=azuresmps-4.0.0) | [獲取 AzureRm 服務匯流排地理配置](/powershell/module/azurerm.servicebus/get-azurermservicebusgeodrconfiguration?view=azurermps-6.13.0) | [獲取服務匯流排地理配置](/powershell/module/az.servicebus/get-azservicebusgeodrconfiguration?view=azps-1.6.0) |
| [Get-AzureSBNamespace](/powershell/module/servicemanagement/azure/get-azuresbnamespace?view=azuresmps-4.0.0) | [Get-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/get-azurermservicebusnamespace?view=azurermps-6.13.0) | [獲取服務匯流排命名空間](/powershell/module/az.servicebus/get-azservicebusnamespace?view=azps-1.6.0) |
| [New-AzureSBAuthorizationRule](/powershell/module/servicemanagement/azure/new-azuresbauthorizationrule?view=azuresmps-4.0.0) | [New-AzureRmServiceBusAuthorizationRule](/powershell/module/azurerm.servicebus/new-azurermservicebusauthorizationrule?view=azurermps-6.13.0) | [新服務匯流排授權規則](/powershell/module/az.servicebus/new-azservicebusauthorizationrule?view=azps-1.6.0) |
| [New-AzureSBNamespace](/powershell/module/servicemanagement/azure/new-azuresbnamespace?view=azuresmps-4.0.0) | [New-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/new-azurermservicebusnamespace?view=azurermps-6.13.0) | [新服務匯流排命名空間](/powershell/module/az.servicebus/new-azservicebusnamespace?view=azps-1.6.0) |
| [刪除-AzureRm 中繼授權規則](/powershell/module/azurerm.relay/remove-azurermrelayauthorizationrule?view=azurermps-6.13.0) | [Remove-AzureRmEventHubAuthorizationRule](/powershell/module/azurerm.eventhub/remove-azurermeventhubauthorizationrule?view=azurermps-6.13.0) | [刪除-服務匯流排授權規則](/powershell/module/az.servicebus/remove-azservicebusauthorizationrule?view=azps-1.6.0) |
| [Remove-AzureSBNamespace](/powershell/module/servicemanagement/azure/remove-azuresbnamespace?view=azuresmps-4.0.0) | [Remove-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/remove-azurermservicebusnamespace?view=azurermps-6.13.0) | [刪除-服務匯流排命名空間](/powershell/module/az.servicebus/remove-azservicebusnamespace?view=azps-1.6.0) |
| [Set-AzureSBAuthorizationRule](/powershell/module/servicemanagement/azure/set-azuresbauthorizationrule?view=azuresmps-4.0.0) | [Set-AzureRmServiceBusAuthorizationRule](/powershell/module/azurerm.servicebus/set-azurermservicebusauthorizationrule?view=azurermps-6.13.0) | [設置-服務匯流排授權規則](/powershell/module/az.servicebus/set-azservicebusauthorizationrule?view=azps-1.6.0) |

## <a name="next-steps"></a>後續步驟
請參閱下列文件： 

- 最新的 REST API 文檔
    - [Azure 服務匯流排](/rest/api/servicebus/)
    - [Azure 事件中心](/rest/api/eventhub/)
    - [Azure 轉送](/rest/api/relay/)
- 最新的 PowerShell 文檔
    - [Azure 服務匯流排](/powershell/module/azurerm.servicebus/?view=azurermps-6.13.0#service_bus)
    - [Azure 事件中心](/powershell/module/azurerm.eventhub/?view=azurermps-6.13.0#event_hub)
    - [Azure 事件網格](/powershell/module/azurerm.eventgrid/?view=azurermps-6.13.0#event_grid)
