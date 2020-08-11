---
title: Azure 服務匯流排診斷記錄 |Microsoft Docs
description: 本文概述可用於 Azure 服務匯流排的所有操作和診斷記錄。
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: f227f5a988ccd51425b4f43e87b4ed4d9af74e1f
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/11/2020
ms.locfileid: "88064429"
---
# <a name="enable-diagnostics-logs-for-service-bus"></a>啟用服務匯流排的診斷記錄

當您開始使用 Azure 服務匯流排命名空間時，您可能會想要監視命名空間的建立、刪除或存取的方式和時間。 本文提供所有可用的操作和診斷記錄的總覽。

Azure 服務匯流排目前支援「活動」和「作業記錄」，其會捕捉在 Azure 服務匯流排命名空間上執行的*管理作業*。 具體而言，這些記錄會擷取作業類型，包括建立佇列、使用的資源，以及作業狀態。

## <a name="operational-logs-schema"></a>作業記錄結構描述

所有記錄都會以 JavaScript 物件標記法 (JSON) 格式儲存在下列兩個位置：

- **AzureActivity**：顯示作業的記錄，以及針對 Azure 入口網站中的命名空間或透過 Azure Resource Manager 範本部署進行的動作。
- **AzureDiagnostics**：使用 API 或透過語言 SDK 上的管理用戶端，顯示針對您的命名空間進行的作業和動作的記錄。

作業記錄 JSON 字串包括下表所列的元素：

| 名稱 | 描述 |
| ------- | ------- |
| ActivityId | 內部識別碼，用來識別指定的活動 |
| EventName | 作業名稱 |
| ResourceId | Azure Resource Manager 資源識別碼 |
| SubscriptionId | 訂用帳戶識別碼 |
| EventTimeString | 作業時間 |
| EventProperties | 作業屬性 |
| 狀態 | 作業狀態 |
| 呼叫者 | Azure 入口網站或管理用戶端 (作業的呼叫者)  |
| 類別 | OperationalLogs |

以下是作業記錄 JSON 字串的範例：

```json
{
  "ActivityId": "6aa994ac-b56e-4292-8448-0767a5657cc7",
  "EventName": "Create Queue",
  "resourceId": "/SUBSCRIPTIONS/1A2109E3-9DA0-455B-B937-E35E36C1163C/RESOURCEGROUPS/DEFAULT-SERVICEBUS-CENTRALUS/PROVIDERS/MICROSOFT.SERVICEBUS/NAMESPACES/SHOEBOXEHNS-CY4001",
  "SubscriptionId": "1a2109e3-9da0-455b-b937-e35e36c1163c",
  "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
  "EventProperties": "{\"SubscriptionId\":\"1a2109e3-9da0-455b-b937-e35e36c1163c\",\"Namespace\":\"shoeboxehns-cy4001\",\"Via\":\"https://shoeboxehns-cy4001.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
  "Status": "Succeeded",
  "Caller": "ServiceBus Client",
  "category": "OperationalLogs"
}
```

## <a name="events-and-operations-captured-in-operational-logs"></a>在操作記錄中所捕獲的事件和作業

作業記錄會捕捉在 Azure 服務匯流排命名空間上執行的所有管理作業。 資料作業不會被捕捉，因為 Azure 服務匯流排上執行的資料作業量很高。

> [!NOTE]
> 為了協助您更有效地追蹤資料作業，我們建議使用用戶端追蹤。

下列管理作業會在操作記錄中加以捕捉： 

| 影響範圍 | 作業|
|-------| -------- |
| 命名空間 | <ul> <li> 建立命名空間</li> <li> 更新命名空間 </li> <li> 刪除命名空間 </li> <li> 更新命名空間 Win2k3 sharedaccess 原則 </li> </ul> | 
| 佇列 | <ul> <li> 建立佇列</li> <li> 更新佇列</li> <li> 刪除佇列 </li> <li> AutoDelete 刪除佇列 </li> </ul> | 
| 主題 | <ul> <li> 建立主題 </li> <li> 更新主題 </li> <li> 刪除主題 </li> <li> AutoDelete 刪除主題 </li> </ul> |
| 訂用帳戶 | <ul> <li> 建立訂閱 </li> <li> 更新訂閱 </li> <li> 刪除訂閱 </li> <li> AutoDelete 刪除訂用帳戶 </li> </ul> |

> [!NOTE]
> 目前，在操作記錄中不會追蹤*讀取*作業。

## <a name="enable-operational-logs"></a>啟用操作記錄

預設會停用操作記錄。 若要啟用診斷記錄，請執行下列動作：

1. 在[Azure 入口網站](https://portal.azure.com)中，移至您的 Azure 服務匯流排命名空間，然後在 [**監視**] 底下，選取 [**診斷設定**]。

   ![[診斷設定] 連結](./media/service-bus-diagnostic-logs/image1.png)

1. 在 [**診斷設定**] 窗格中，選取 [**新增診斷設定**]。  

   ![[新增診斷設定] 連結](./media/service-bus-diagnostic-logs/image2.png)

1. 執行下列動作來設定診斷設定：

   a. 在 [**名稱**] 方塊中，輸入診斷設定的名稱。  

   b. 為診斷記錄選取下列三個目的地的其中一個：  
   - 如果您選取 [封存**至儲存體帳戶**]，則需要設定將儲存診斷記錄的儲存體帳戶。  
   - 如果您選取 [**串流至事件中樞**]，則需要設定您想要將診斷記錄串流至其中的事件中樞。
   - 如果您選取 [**傳送至 Log analytics**]，則必須指定要將診斷傳送至哪個 Log analytics 實例。  

   c. 選取 [ **OperationalLogs** ] 核取方塊。

    ![[診斷設定] 窗格](./media/service-bus-diagnostic-logs/image3.png)

1. 選取 [儲存]。

新的設定會在大約10分鐘內生效。 記錄會顯示在 [**診斷記錄**] 窗格中設定的封存目標中。

如需有關設定診斷設定的詳細資訊，請參閱[Azure 診斷記錄的總覽](../azure-monitor/platform/platform-logs-overview.md)。

## <a name="next-steps"></a>後續步驟

若要深入瞭解服務匯流排，請參閱：

* [服務匯流排簡介](service-bus-messaging-overview.md)
* [開始使用服務匯流排](service-bus-dotnet-get-started-with-queues.md)