---
title: Azure 服務總線診斷日誌 |微軟文件
description: 本文概述了可用於 Azure 服務總線的所有操作和診斷日誌。
keywords: ''
documentationcenter: .net
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: a80fb97810fee04a4eb50c43178c168e66f29173
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618736"
---
# <a name="enable-diagnostics-logs-for-service-bus"></a>為服務匯流啟用診斷紀錄

開始使用 Azure 服務總線命名空間時,可能需要監視創建、刪除或訪問命名空間的方式和時間。 本文概述了所有可用的操作和診斷日誌。

Azure 服務匯流目前支援活動紀錄與操作紀錄,這些紀錄捕捉在 Azure 服務總線命名空間上執行*的管理操作*。 具體而言，這些記錄會擷取作業類型，包括建立佇列、使用的資源，以及作業狀態。

## <a name="operational-logs-schema"></a>作業記錄結構描述

所有紀錄都以 JavaScript 物件符號 (JSON) 格式儲存在以下兩個位置:

- **Azure 活動**:顯示針對 Azure 門戶中的命名空間或透過 Azure 資源管理器樣本部署執行的操作和操作的日誌。
- **Azure診斷**:使用 API 或透過語言 SDK 上的管理用戶端顯示針對命名空間執行的操作和操作的日誌。

操作紀錄 JSON 字串包括下表中列出的元素:

| 名稱 | 描述 |
| ------- | ------- |
| ActivityId | 內部識別碼,用於識別指定的活動 |
| EventName | 作業名稱 |
| ResourceId | Azure Resource Manager 資源識別碼 |
| SubscriptionId | 訂用帳戶識別碼 |
| EventTimeString | 作業時間 |
| EventProperties | 作業屬性 |
| 狀態 | 作業狀態 |
| 呼叫者 | 操作呼叫者(Azure 門戶或管理用戶端) |
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

## <a name="events-and-operations-captured-in-operational-logs"></a>在操作紀錄中擷取的事件與操作

操作日誌捕獲在 Azure 服務總線命名空間上執行的所有管理操作。 由於在 Azure 服務總線上執行的數據操作量大,因此不會捕獲數據操作。

> [!NOTE]
> 為了説明您更好地跟蹤數據操作,我們建議使用用戶端跟蹤。

以下管理操作在操作紀錄中捕捉: 

| 影響範圍 | 作業|
|-------| -------- |
| 命名空間 | <ul> <li> 建立命名空間</li> <li> 更新命名空間 </li> <li> 刪除命名空間 </li> <li> 更新命名空間分享存取原則 </li> </ul> | 
| 佇列 | <ul> <li> 建立佇列</li> <li> 更新佇列</li> <li> 刪除佇列 </li> <li> 自動刪除刪除佇列 </li> </ul> | 
| 主題 | <ul> <li> 建立主題 </li> <li> 更新主題 </li> <li> 刪除主題 </li> <li> 自動移除移除佈景主題 </li> </ul> |
| 訂用帳戶 | <ul> <li> 建立訂閱 </li> <li> 更新訂閱 </li> <li> 刪除訂閱 </li> <li> 自動刪除訂閱訂閱 </li> </ul> |

> [!NOTE]
> 目前,*未*在操作日誌中跟蹤讀取操作。

## <a name="enable-operational-logs"></a>開啟操作紀錄

默認情況下禁用操作日誌。 要啟用診斷紀錄,可以執行以下操作:

1. 在[Azure 門戶](https://portal.azure.com)中,轉到 Azure 服務總線命名空間,然後在 **「監視**」下選擇 **「診斷設置**」。

   !["診斷設定"連結](./media/service-bus-diagnostic-logs/image1.png)

1. 在 **「診斷設定」** 窗格中,選擇 **「添加診斷設定**」。  

   !['新增診斷設定"連結](./media/service-bus-diagnostic-logs/image2.png)

1. 通過執行以下操作設定診斷設定:

   a. 在 **「名稱」** 框中,輸入診斷設置的名稱。  

   b. 為診斷紀錄選擇以下三個目標之一:  
   - 如果選擇 **「存檔到存儲帳戶**」,則需要配置存儲診斷日誌的存儲帳戶。  
   - 如果選擇 **「流到事件中心**」,則需要配置要將診斷日誌流式傳輸到的事件中心。
   - 如果選擇 **「發送到日誌分析**」,則需要指定診斷程式將發送到日誌分析的實例。  

   c. 選擇 **「操作日誌」** 複選方塊。

    !["診斷設定"窗格](./media/service-bus-diagnostic-logs/image3.png)

1. 選取 [儲存]  。

新設置在大約 10 分鐘內生效。 日誌顯示在配置的存檔目標中,即**診斷日誌**窗格中。

有關設定診斷設定的詳細資訊,請參閱[Azure 診斷紀錄的概述](../azure-monitor/platform/diagnostic-logs-overview.md)。

## <a name="next-steps"></a>後續步驟

要瞭解有關服務總線的更多詳細資訊,請參閱:

* [服務匯流排簡介](service-bus-messaging-overview.md)
* [開始使用服務匯流排](service-bus-dotnet-get-started-with-queues.md)
