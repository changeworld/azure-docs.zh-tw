---
title: 設定診斷記錄 - Azure 事件中樞 | Microsoft Docs
description: 了解如何為 Azure 中的事件中樞設定活動記錄和診斷記錄。
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: ccd38d8924765df7bfd91b4fc26bb5304f6f180d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88927726"
---
# <a name="set-up-diagnostic-logs-for-an-azure-event-hub"></a>為 Azure 事件中樞設定診斷記錄

您可以檢視 Azure 事件中樞的兩種記錄類型：

* **[活動記錄](../azure-monitor/platform/platform-logs-overview.md)** ：這些記錄包含對作業所執行之操作的相關資訊。 系統一律會啟用這些記錄。 您可以在 Azure 入口網站中，於事件中樞命名空間的左窗格中選取 [活動記錄] 來查看活動記錄項目。 例如：「建立或更新命名空間」、「建立或更新事件中樞」。

    ![事件中樞命名空間的活動記錄](./media/event-hubs-diagnostic-logs/activity-log.png)
* **[診斷記錄](../azure-monitor/platform/platform-logs-overview.md)** ：診斷記錄能透過使用 API，或是透過語言 SDK 上的管理用戶端，為針對您命名空間所進行的作業和動作提供更加豐富的資訊。 
    
    下列小節會示範如何為事件中樞命名空間啟用診斷記錄。

## <a name="enable-diagnostic-logs"></a>啟用診斷記錄
診斷記錄預設為停用。 若要啟用診斷記錄，請依照下列步驟操作：

1.  在 [Azure 入口網站](https://portal.azure.com)中，瀏覽到您的事件中樞命名空間。 
2. 在左窗格中，選取 [監視] 底下的 [診斷設定]，然後選取 [+ 新增診斷設定]。 

    ![[診斷設定] 頁面 - 新增診斷設定](./media/event-hubs-diagnostic-logs/diagnostic-settings-page.png)
4. 在 [類別詳細資料] 區段中，選取您想要啟用的**診斷記錄類型**。 您稍後會在此文章中找到關於這些類別的詳細資料。 
5. 在 [目的地詳細資料] 區段中，設定您想要的封存目標 (目的地)，例如儲存體帳戶、事件中樞或 Log Analytics 工作區。

    ![[新增診斷設定] 頁面](./media/event-hubs-diagnostic-logs/aDD-diagnostic-settings-page.png)
6.  選取工具列上的 [儲存] 來儲存診斷設定。

    新的設定大約會在 10 分鐘內生效。 之後，記錄就會在 [診斷記錄] 窗格內的已設定封存目標中顯示。

    如需設定診斷的詳細資訊，請參閱 [Azure 診斷記錄概觀](../azure-monitor/platform/platform-logs-overview.md)。

## <a name="diagnostic-logs-categories"></a>診斷記錄類別

事件中樞會擷取下列類別的診斷記錄：

| 類別 | 描述 | 
| -------- | ----------- | 
| 封存記錄 | 擷取[事件中樞擷取](event-hubs-capture-overview.md)作業的相關資訊，具體而言就是與擷取錯誤相關的記錄。 |
| 作業記錄 | 擷取在 Azure 事件中樞命名空間上執行的所有管理作業。 不會擷取資料作業，因為在 Azure 事件中樞上執行的資料作業量很高。 |
| 自動調整記錄 | 擷取在事件中樞命名空間上完成的自動擴充作業。 |
| Kafka 協調器記錄 | 擷取與事件中樞相關的 Kafka 協調器作業。 |
| Kafka 使用者錯誤記錄 | 擷取在事件中樞上呼叫之 Kafka API 的相關資訊。 |
| 事件中樞虛擬網路 (VNet) 連線事件 | 擷取傳送流量到事件中樞的 IP 位址和虛擬網路的相關資訊。 |
| 客戶管理的金鑰使用者記錄 | 擷取與客戶管理的金鑰相關的作業。 |


所有記錄都會以「JavaScript 物件標記法」(JSON) 格式儲存。 每個項目都具有字串欄位，這些欄位會使用下列小節所述的格式。

## <a name="archive-logs-schema"></a>封存記錄結構描述

封存記錄檔 JSON 字串包括下表所列的元素：

名稱 | 描述
------- | -------
`TaskName` | 失敗工作的描述
`ActivityId` | 用於追蹤的內部識別碼
`trackingId` | 用於追蹤的內部識別碼
`resourceId` | Azure Resource Manager 資源識別碼
`eventHub` | 事件中樞完整名稱 (包括命名空間名稱)
`partitionId` | 要寫入的事件中樞資料分割
`archiveStep` | 可能值：ArchiveFlushWriter、DestinationInit
`startTime` | 失敗開始時間
`failures` | 發生失敗的次數
`durationInSeconds` | 失敗持續時間
`message` | 錯誤訊息
`category` | ArchiveLogs

下列程式碼是封存記錄 JSON 字串的範例：

```json
{
   "TaskName": "EventHubArchiveUserError",
   "ActivityId": "000000000-0000-0000-0000-0000000000000",
   "trackingId": "0000000-0000-0000-0000-00000000000000000",
   "resourceId": "/SUBSCRIPTIONS/000000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/<Event Hubs Namespace Name>",
   "eventHub": "<Event Hub full name>",
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

名稱 | 描述
------- | -------
`ActivityId` | 內部識別碼，用來進行追蹤 |
`EventName` | 作業名稱 |
`resourceId` | Azure Resource Manager 資源識別碼 |
`SubscriptionId` | 訂用帳戶識別碼 |
`EventTimeString` | 作業時間 |
`EventProperties` | 作業屬性 |
`Status` | 作業狀態 |
`Caller` | 作業呼叫者 (Azure 入口網站或管理用戶端) |
`Category` | OperationalLogs |

下列程式碼是作業記錄 JSON 字串的範例：

```json
Example:
{
   "ActivityId": "00000000-0000-0000-0000-00000000000000",
   "EventName": "Create EventHub",
   "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/<Event Hubs namespace name>",
   "SubscriptionId": "000000000-0000-0000-0000-000000000000",
   "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
   "EventProperties": "{\"SubscriptionId\":\"0000000000-0000-0000-0000-000000000000\",\"Namespace\":\"<Namespace Name>\",\"Via\":\"https://<Namespace Name>.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
   "Status": "Succeeded",
   "Caller": "ServiceBus Client",
   "category": "OperationalLogs"
}
```

## <a name="autoscale-logs-schema"></a>自動調整記錄結構描述
自動調整記錄 JSON 包括下表所列的元素：

| 名稱 | 描述 |
| ---- | ----------- | 
| `TrackingId` | 內部識別碼，用來進行追蹤 |
| `ResourceId` | Azure Resource Manager 資源識別碼。 |
| `Message` | 參考訊息，其能提供自動擴充動作的詳細資料。 訊息會包含指定命名空間之輸送量單位先前與目前的值，以及觸發 TU 擴充的項目。 |

以下是自動調整事件的範例： 

```json
{
    "TrackingId": "fb1b3676-bb2d-4b17-85b7-be1c7aa1967e",
    "Message": "Scaled-up EventHub TUs (UpdateStartTimeUTC: 5/13/2020 7:48:36 AM, PreviousValue: 1, UpdatedThroughputUnitValue: 2, AutoScaleReason: 'IncomingMessagesPerSecond reached 2170')",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name"
}
```

## <a name="kafka-coordinator-logs-schema"></a>Kafka 協調器記錄結構描述
Kafka 協調器記錄 JSON 包括下表所列的元素：

| 名稱 | 描述 |
| ---- | ----------- | 
| `RequestId` | 要求識別碼，用來進行追蹤 |
| `ResourceId` | Azure Resource Manager 資源識別碼 |
| `Operation` | 在群組協調期間完成之作業的名稱 |
| `ClientId` | 用戶端識別碼 |
| `NamespaceName` | 命名空間名稱 | 
| `SubscriptionId` | Azure 訂用帳戶識別碼 |
| `Message` | 參考或警告訊息，其能提供在群組協調期間完成之動作的詳細資料。 |

### <a name="example"></a>範例

```json
{
    "RequestId": "FE01001A89E30B020000000304620E2A_KafkaExampleConsumer#0",
    "Operation": "Join.Start",
    "ClientId": "KafkaExampleConsumer#0",
    "Message": "Start join group for new member namespace-name:c:$default:I:KafkaExampleConsumer#0-cc40856f7f3c4607915a571efe994e82, current group size: 0, API version: 2, session timeout: 10000ms, rebalance timeout: 300000ms.",
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "KafkaCoordinatorLogs"
}
```

## <a name="kafka-user-error-logs-schema"></a>Kafka 使用者錯誤記錄結構描述
Kafka 使用者錯誤記錄 JSON 包括下表所列的元素：

| 名稱 | 描述 |
| ---- | ----------- |
| `TrackingId` | 追蹤識別碼，用來進行追蹤。 |
| `NamespaceName` | 命名空間名稱 |
| `Eventhub` | 事件中樞名稱 |
| `PartitionId` | 資料分割識別碼 |
| `GroupId` | 群組識別碼 |
| `ClientId` | 用戶端識別碼 |
| `ResourceId` | Azure Resource Manager 資源識別碼。 |
| `Message` | 參考訊息，其能提供錯誤的詳細資料 |

## <a name="event-hubs-virtual-network-connection-event-schema"></a>事件中樞虛擬網路連線事件結構描述

事件中樞虛擬網路 (VNet) 連線事件 JSON 包括下表所列的元素：

| 名稱 | 描述 |
| ---  | ----------- | 
| `SubscriptionId` | Azure 訂用帳戶識別碼 |
| `NamespaceName` | 命名空間名稱 |
| `IPAddress` | 連線到事件中樞服務之用戶端的 IP 位址 |
| `Action` | 由事件中樞服務在評估連線要求時完成的動作。 支援的動作為 [接受連線] 和 [拒絕連線]。 |
| `Reason` | 提供完成動作的原因 |
| `Count` | 指定動作的出現次數 |
| `ResourceId` | Azure Resource Manager 資源識別碼。 |

### <a name="example"></a>範例

```json
{
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "IPAddress": "1.2.3.4",
    "Action": "Deny Connection",
    "Reason": "IPAddress doesn't belong to a subnet with Service Endpoint enabled.",
    "Count": "65",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "EventHubVNetConnectionEvent"
}
```

## <a name="customer-managed-key-user-logs"></a>客戶管理的金鑰使用者記錄
客戶管理的金鑰使用者記錄 JSON 包括下表所列的元素：

| 名稱 | 描述 |
| ---- | ----------- | 
| `Category` | 訊息的類別類型。 其為下列其中一個值：**error** 和 **info** |
| `ResourceId` | 內部資源識別碼，其包括 Azure 訂用帳戶識別碼和命名空間名稱 |
| `KeyVault` | 金鑰保存庫資源的名稱 |
| `Key` | 金鑰保存庫金鑰的名稱。 |
| `Version` | 金鑰保存庫金鑰的版本 |
| `Operation` | 為提供要求所完成之作業的名稱 |
| `Code` | 狀態碼 |
| `Message` | 訊息，其能提供錯誤或參考訊息的詳細資料 |



## <a name="next-steps"></a>後續步驟
- [事件中樞簡介](./event-hubs-about.md)
- [事件中樞範例](sdks.md)
- 開始使用事件中心
    - [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-java-get-started-send.md)
