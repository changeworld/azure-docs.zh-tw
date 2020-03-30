---
title: 包含檔案
description: 包含檔案
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/14/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 6bb59db4c1b31033b1e116742dedc94621b1c60d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80117071"
---
[Durable Functions](../articles/azure-functions/durable-functions-overview.md) 的組態設定。

### <a name="durable-functions-1x"></a>持久功能 1.x

```json
{
  "durableTask": {
    "hubName": "MyTaskHub",
    "controlQueueBatchSize": 32,
    "partitionCount": 4,
    "controlQueueVisibilityTimeout": "00:05:00",
    "workItemQueueVisibilityTimeout": "00:05:00",
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10,
    "maxQueuePollingInterval": "00:00:30",
    "azureStorageConnectionStringName": "AzureWebJobsStorage",
    "trackingStoreConnectionStringName": "TrackingStorage",
    "trackingStoreNamePrefix": "DurableTask",
    "traceInputsAndOutputs": false,
    "logReplayEvents": false,
    "eventGridTopicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
    "eventGridKeySettingName":  "EventGridKey",
    "eventGridPublishRetryCount": 3,
    "eventGridPublishRetryInterval": "00:00:30",
    "eventGridPublishEventTypes": ["Started", "Completed", "Failed", "Terminated"]
  }
}
```

### <a name="durable-functions-2x"></a><a name="durable-functions-2-0-host-json"></a>持久功能 2.x

```json
{
 "extensions": {
  "durableTask": {
    "hubName": "MyTaskHub",
    "storageProvider": {
      "connectionStringName": "AzureWebJobsStorage",
      "controlQueueBatchSize": 32,
      "controlQueueBufferThreshold": 256,
      "controlQueueVisibilityTimeout": "00:05:00",
      "maxQueuePollingInterval": "00:00:30",
      "partitionCount": 4,
      "trackingStoreConnectionStringName": "TrackingStorage",
      "trackingStoreNamePrefix": "DurableTask",
      "workItemQueueVisibilityTimeout": "00:05:00",
    },
    "tracing": {
      "traceInputsAndOutputs": false,
      "traceReplayEvents": false,
    },
    "notifications": {
      "eventGrid": {
        "topicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
        "keySettingName": "EventGridKey",
        "publishRetryCount": 3,
        "publishRetryInterval": "00:00:30",
        "publishEventTypes": [
          "Started",
          "Pending",
          "Failed",
          "Terminated"
        ]
      }
    },
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10,
    "extendedSessionsEnabled": false,
    "extendedSessionIdleTimeoutInSeconds": 30,
    "useGracefulShutdown": false
  }
  }
}

```

工作中樞名稱必須以字母開頭，且只包含字母和數字。 如果未指定函式應用程式的工作中樞名稱，則預設為 **DurableFunctionsHub**。 如需詳細資訊，請參閱[工作中樞](../articles/azure-functions/durable-functions-task-hubs.md)。

|屬性  |預設 | 描述 |
|---------|---------|---------|
|hubName|DurableFunctionsHub|替代[工作中樞](../articles/azure-functions/durable-functions-task-hubs.md)名稱可用來彼此隔離多個 Durable Functions 應用程式，即使它們使用相同的儲存體後端。|
|控制項佇列批量|32|要從控制佇列中一次提取的訊息數。|
|控制佇列緩衝區閾值|256|一次可以在記憶體中緩衝的控制項佇列消息數，此時調度程式將等待取消任何其他消息的排隊。|
|partitionCount |4|控制佇列的資料分割計數。 必須是介於 1 到 16 之間的正整數。|
|控制佇列可見度超時 |5 分鐘|已從控制佇列中清除之訊息的可見度逾時。|
|工作專案佇列可見度超時 |5 分鐘|已從工作項目佇列中清除之訊息的可見度逾時。|
|最大併發活動函數 |目前電腦上的 10 倍處理器數目|可以在單一主機執行個體上同時處理的活動函式數目上限。|
|最大併發協調器功能 |目前電腦上的 10 倍處理器數目|可以在單一主機執行個體上同時處理的協調器函式數目上限。|
|最大佇列輪詢間隔|30 秒|*hh：mm：ss*格式的最大控制項和工作項佇列輪詢間隔。 值越高，消息處理延遲越高。 由於存儲事務增加，值越低可能會導致存儲成本增加。|
|azure 存儲連接字串名稱 |AzureWebJobsStorage|具有 Azure 儲存體連接字串的應用程式設定名稱，而該連接字串用來管理基礎的 Azure 儲存體資源。|
|跟蹤存儲連接字串名稱||用於"歷史記錄"和"實例"表的連接字串的名稱。 如果未指定，`azureStorageConnectionStringName`則使用連接。|
|跟蹤商店名稱首碼||指定時`trackingStoreConnectionStringName`用於歷史記錄和實例表的首碼。 如果未設置，則預設首碼值將為`DurableTask`。 如果未`trackingStoreConnectionStringName`指定，則"歷史記錄"和"實例"表將使用`hubName`該值作為首碼，並將忽略 其`trackingStoreNamePrefix`的任何設置。|
|跟蹤輸入和輸出 |false|此值指出是否要追蹤函式呼叫的輸入和輸出。 追蹤函式執行事件時的預設行為就是在函式呼叫的序列化輸入和輸出中包含位元組數目。 此行為提供有關輸入和輸出外觀的最小資訊，而不會使日誌膨脹或無意中公開敏感資訊。 將此屬性設定為 true，會導致預設函式記錄功能記錄函式輸入和輸出的整個內容。|
|日誌重播事件|false|此值可指出是否要將協調流程重新執行事件寫入 Application Insights。|
|事件網格主題終結點 ||Azure 事件方格自訂主題端點的 URL。 設置此屬性後，業務流程生命週期通知事件將發佈到此終結點。 這個屬性支援應用程式設定解析。|
|事件網格鍵設置名稱 ||應用程式設定的名稱，其中包含在 `EventGridTopicEndpoint` 用來向 Azure 事件方格自訂主題進行驗證的金鑰。|
|事件網格發佈重試計數|0|如果發佈到 Event Grid 主題失敗，重試的次數。|
|事件網格發佈重試間隔|5 分鐘|「事件方格」會以 *hh:mm:ss* 格式發佈重試間隔。|
|事件網格發佈事件種類||要發佈到事件網格的事件種類的清單。 如果未指定，將發佈所有事件種類。 允許的值包括`Started` `Completed`、 `Failed` `Terminated`、 、 、 、 、 、 、 、 、|
|使用Graceful 關閉|false|（預覽版）正常關閉，以減少主機關閉失敗進程內功能執行的可能性。|

其中許多設置用於優化性能。 如需詳細資訊，請參閱[效能和級別](../articles/azure-functions/durable-functions-perf-and-scale.md)。
