---
title: 設定診斷記錄 - Azure 事件中樞 | Microsoft Docs
description: 了解如何為 Azure 中的事件中樞設定活動記錄和診斷記錄。
keywords: ''
documentationcenter: ''
services: event-hubs
author: ShubhaVijayasarathy
manager: ''
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.custom: seodec18
ms.date: 04/28/2020
ms.author: shvija
ms.openlocfilehash: 3010ee7b996c9d3e96082edeb9447c960da321bd
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82509755"
---
# <a name="set-up-diagnostic-logs-for-an-azure-event-hub"></a>為 Azure 事件中樞設定診斷記錄

您可以檢視 Azure 事件中樞的兩種記錄類型：

* **[活動記錄](../azure-monitor/platform/platform-logs-overview.md)**：這些記錄檔包含作業上完成之作業的相關資訊。 系統一律會啟用這些記錄。 您可以在 Azure 入口網站的事件中樞命名空間的左窗格中選取 [**活動記錄**]，以查看活動記錄專案。 例如：「建立或更新命名空間」、「建立或更新事件中樞」。

    ![事件中樞命名空間的活動記錄](./media/event-hubs-diagnostic-logs/activity-log.png)
* **[診斷記錄](../azure-monitor/platform/platform-logs-overview.md)**：您可以設定診斷記錄，以深入檢視隨著作業發生的所有事件。 診斷記錄涵蓋從建立工作到刪除工作期間的活動，包括工作執行時發生的更新與活動。

    下一節將說明如何啟用事件中樞命名空間的診斷記錄。

## <a name="enable-diagnostic-logs"></a>啟用診斷記錄
診斷記錄預設為停用。 若要啟用診斷記錄，請依照下列步驟操作：

1.  在 [ [Azure 入口網站](https://portal.azure.com)中，流覽至您的事件中樞命名空間。 
2. 在左窗格中選取 [**監視**] 底下的 [**診斷設定**]，然後選取 [ **+ 新增診斷設定**]。 

    ![診斷設定頁面-新增診斷設定](./media/event-hubs-diagnostic-logs/diagnostic-settings-page.png)
4. 在 [**類別目錄詳細資料**] 區段中，選取您想要啟用的**診斷記錄類型**。 您稍後會在本文中找到這些類別的詳細資料。 
5. 在 [**目的地詳細資料**] 區段中，設定您想要的封存目標（目的地）;例如，儲存體帳戶、事件中樞或 Log Analytics 工作區。

    ![[新增診斷設定] 頁面](./media/event-hubs-diagnostic-logs/aDD-diagnostic-settings-page.png)
6.  選取工具列上的 [**儲存**] 以儲存診斷設定。

    新的設定大約會在 10 分鐘內生效。 之後，記錄就會在 [診斷記錄]**** 窗格內的已設定封存目標中顯示。

    如需設定診斷的詳細資訊，請參閱 [Azure 診斷記錄概觀](../azure-monitor/platform/platform-logs-overview.md)。

## <a name="diagnostic-logs-categories"></a>診斷記錄類別

事件中樞會針對下列類別來捕捉診斷記錄：

- **封存記錄**：與「事件中樞」封存相關的記錄，具體而言，就是與封存錯誤相關的記錄。
- **作業記錄**：與「事件中樞」作業期間發生的事件有關的資訊，具體而言，就是作業類型 (包括事件中樞的建立)、使用的資源及作業狀態。
- **自動調整記錄**：在事件中樞命名空間上完成之自動調整作業的相關資訊。 
- **Kafka 協調器記錄**-與事件中樞相關的 Kafka 協調器作業資訊。 
- **Kafka 使用者記錄**：與事件中樞相關的 Kafka 使用者作業的資訊。 
- **事件中樞虛擬網路（VNet）線上活動**：事件中樞虛擬網路線上活動的相關資訊。 
- **客戶管理的金鑰使用者記錄**：與客戶管理的金鑰相關之作業的資訊。 


    所有記錄都會以「JavaScript 物件標記法」(JSON) 格式儲存。 每個項目都具有字串欄位，這些欄位會使用下列小節所述的格式。

## <a name="archive-logs-schema"></a>封存記錄結構描述

封存記錄檔 JSON 字串包括下表所列的元素：

Name | 描述
------- | -------
TaskName | 失敗工作的描述。
ActivityId | 用於追蹤的內部識別碼。
trackingId | 用於追蹤的內部識別碼。
resourceId | Azure Resource Manager 資源識別碼。
eventHub | 事件中樞完整名稱 (包括命名空間名稱)。
partitionId | 要寫入的事件中樞資料分割。
archiveStep | ArchiveFlushWriter
startTime | 失敗開始時間。
failures | 發生失敗的次數。
durationInSeconds | 失敗持續時間。
訊息 | 錯誤訊息。
category | ArchiveLogs

下列程式碼是封存記錄 JSON 字串的範例：

```json
{
   "TaskName": "EventHubArchiveUserError",
   "ActivityId": "21b89a0b-8095-471a-9db8-d151d74ecf26",
   "trackingId": "21b89a0b-8095-471a-9db8-d151d74ecf26_B7",
   "resourceId": "/SUBSCRIPTIONS/854D368F-1828-428F-8F3C-F2AFFA9B2F7D/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "eventHub": "fbettati-opera-eventhub:eventhub:eh123~32766",
   "partitionId": "1",
   "archiveStep": "ArchiveFlushWriter",
   "startTime": "9/22/2016 5:11:21 AM",
   "failures": 3,
   "durationInSeconds": 360,
   "message": "Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (404) Not Found. ---> System.Net.WebException: The remote server returned an error: (404) Not Found.\r\n   at Microsoft.WindowsAzure.Storage.Shared.Protocol.HttpResponseParsers.ProcessExpectedStatusCodeNoException[T](HttpStatusCode expectedStatusCode, HttpStatusCode actualStatusCode, T retVal, StorageCommandBase`1 cmd, Exception ex)\r\n   at Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob.<PutBlockImpl>b__3e(RESTCommand`1 cmd, HttpWebResponse resp, Exception ex, OperationContext ctx)\r\n   at Microsoft.WindowsAzure.Storage.Core.Executor.Executor.EndGetResponse[T](IAsyncResult getResponseResult)\r\n   --- End of inner exception stack trace ---\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.StorageAsyncResult`1.End()\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.AsyncExtensions.<>c__DisplayClass4.<CreateCallbackVoid>b__3(IAsyncResult ar)\r\n--- End of stack trace from previous location where exception was thrown ---\r\n   at System.",
   "category": "ArchiveLogs"
}
```

## <a name="operational-logs-schema"></a>作業記錄結構描述

作業記錄 JSON 字串包括下表所列的元素：

Name | 描述
------- | -------
ActivityId | 用於追蹤目的的內部識別碼。
EventName | 作業名稱。  
resourceId | Azure Resource Manager 資源識別碼。
SubscriptionId | 訂用帳戶 ID。
EventTimeString | 作業時間。
EventProperties | 作業屬性。
狀態 | 作業狀態。
呼叫者 | 作業呼叫者 (Azure 入口網站或管理用戶端)。
category | OperationalLogs

下列程式碼是作業記錄 JSON 字串的範例：

```json
Example:
{
   "ActivityId": "6aa994ac-b56e-4292-8448-0767a5657cc7",
   "EventName": "Create EventHub",
   "resourceId": "/SUBSCRIPTIONS/1A2109E3-9DA0-455B-B937-E35E36C1163C/RESOURCEGROUPS/DEFAULT-SERVICEBUS-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/SHOEBOXEHNS-CY4001",
   "SubscriptionId": "1a2109e3-9da0-455b-b937-e35e36c1163c",
   "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
   "EventProperties": "{\"SubscriptionId\":\"1a2109e3-9da0-455b-b937-e35e36c1163c\",\"Namespace\":\"shoeboxehns-cy4001\",\"Via\":\"https://shoeboxehns-cy4001.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
   "Status": "Succeeded",
   "Caller": "ServiceBus Client",
   "category": "OperationalLogs"
}
```

## <a name="autoscale-logs-schema"></a>自動調整記錄架構
自動調整記錄 JSON 包含下表所列的元素：

| Name | 描述 |
| ---- | ----------- | 
| trackingId | 用於追蹤目的的內部識別碼 |
| resourceId | 內部識別碼，其中包含 Azure 訂用帳戶識別碼和命名空間名稱 |
| 訊息 | 參考用訊息，其中提供自動擴充動作的詳細資料。 此訊息包含給定命名空間的輸送量單位先前和目前值，以及觸發了 TU 的擴大。 |

## <a name="kafka-coordinator-logs-schema"></a>Kafka 協調器記錄架構
Kafka 協調器記錄檔 JSON 包含下表所列的元素：

| Name | 描述 |
| ---- | ----------- | 
| requestId | 要求識別碼，用於追蹤用途 |
| resourceId | 內部識別碼，其中包含 Azure 訂用帳戶識別碼和命名空間名稱 |
| operationName | 在群組協調期間完成的作業名稱 |
| clientId | 用戶端識別碼 |
| namespaceName | 命名空間名稱 | 
| subscriptionId | Azure 訂用帳戶識別碼 |
| 訊息 | 參考用訊息，其中提供在取用者群組協調期間完成之動作的詳細資料。 |

## <a name="kafka-user-error-logs-schema"></a>Kafka 使用者錯誤記錄架構
Kafka 使用者錯誤記錄檔 JSON 包含下表所列的元素：

| Name | 描述 |
| ---- | ----------- |
| trackingId | 追蹤識別碼，用於追蹤用途。 |
| namespaceName | 命名空間名稱 |
| eventhub | 事件中樞名稱 |
| partitionId | 資料分割識別碼 |
| groupId | 群組識別碼 |
| ClientId | 用戶端識別碼 |
| resourceId | 內部識別碼，其中包含 Azure 訂用帳戶識別碼和命名空間名稱 |
| 訊息 | 告知性訊息，其中提供錯誤的相關詳細資料 |

## <a name="event-hubs-virtual-network-connection-event-schema"></a>事件中樞虛擬網路線上活動架構

事件中樞虛擬網路（VNet）線上活動 JSON 包含下表所列的元素：

| Name | 描述 |
| ---  | ----------- | 
| subscriptionId | Azure 訂用帳戶識別碼 |
| namespaceName | 命名空間名稱 |
| ipAddress | 連接到事件中樞服務之用戶端的 IP 位址 |
| 動作 | 評估連接要求時，事件中樞服務所執行的動作。 支援的動作為**AcceptConnection**和**RejectConnection**。 |
| reason | 提供動作完成的原因 |
| count | 指定動作的發生次數 |
| resourceId | 內部資源識別碼，其中包含訂用帳戶識別碼和命名空間名稱。 |

## <a name="customer-managed-key-user-logs"></a>客戶管理的金鑰使用者記錄
客戶管理的金鑰使用者記錄 JSON 包含下表所列的元素：

| Name | 描述 |
| ---- | ----------- | 
| category | 訊息的類別目錄類型。 這是下列其中一個值： [**錯誤**] 和 [**資訊**] |
| resourceId | 內部資源識別碼，包括 Azure 訂用帳戶識別碼和命名空間名稱 |
| keyVault | Key Vault 資源的名稱 |
| 索引鍵 | Key Vault 索引鍵的名稱。 |
| 版本 | Key Vault 金鑰的版本 |
| operation (作業) | 執行以處理要求的作業名稱 |
| code | 狀態碼 |
| 訊息 | 訊息，提供錯誤或告知性訊息的相關詳細資料 |



## <a name="next-steps"></a>後續步驟
- [事件中樞簡介](event-hubs-what-is-event-hubs.md)
- [事件中樞 API 總覽](event-hubs-api-overview.md)
- 開始使用事件中心
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [JavaScript](get-started-java-send-v2.md)
