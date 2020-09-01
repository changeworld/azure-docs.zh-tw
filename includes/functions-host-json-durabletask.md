---
title: 包含檔案
description: 包含檔案
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/14/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 6e253604c57d73c2a89ccfa5cff7efe9e572d11d
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89094223"
---
[Durable Functions](../articles/azure-functions/durable-functions-overview.md) 的組態設定。

### <a name="durable-functions-1x"></a>Durable Functions 1.x

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

### <a name="durable-functions-2x"></a><a name="durable-functions-2-0-host-json"></a>Durable Functions 2.x

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
      "useLegacyPartitionManagement": true,
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
    "useAppLease": true,
    "useGracefulShutdown": false
  }
 }
}

```

工作中樞名稱必須以字母開頭，且只包含字母和數字。 如果未指定函式應用程式的工作中樞名稱，則預設為 **DurableFunctionsHub**。 如需詳細資訊，請參閱[工作中樞](../articles/azure-functions/durable-functions-task-hubs.md)。

|屬性  |預設 | 描述 |
|---------|---------|---------|
|hubName|DurableFunctionsHub|替代[工作中樞](../articles/azure-functions/durable-functions-task-hubs.md)名稱可用來彼此隔離多個 Durable Functions 應用程式，即使它們使用相同的儲存體後端。|
|controlQueueBatchSize|32|要從控制佇列中一次提取的訊息數。|
|controlQueueBufferThreshold|256|可以在記憶體中一次緩衝處理的控制佇列訊息數目，到達這個數目之後發送器會在針對任何其他訊息清除佇列之前等待。|
|partitionCount |4|控制佇列的資料分割計數。 必須是介於 1 到 16 之間的正整數。|
|controlQueueVisibilityTimeout |5 分鐘|已從控制佇列中清除之訊息的可見度逾時。|
|workItemQueueVisibilityTimeout |5 分鐘|已從工作項目佇列中清除之訊息的可見度逾時。|
|maxConcurrentActivityFunctions |目前電腦上的 10 倍處理器數目|可以在單一主機執行個體上同時處理的活動函式數目上限。|
|maxConcurrentOrchestratorFunctions |目前電腦上的 10 倍處理器數目|可以在單一主機執行個體上同時處理的協調器函式數目上限。|
|maxQueuePollingInterval|30 秒|最大控制和工作項目佇列輪詢間隔的格式為「hh:mm:ss」。 較高的值可能會導致訊息處理延遲較高。 較低的值會因為儲存體交易增加而導致儲存成本較高。|
|azureStorageConnectionStringName |AzureWebJobsStorage|具有 Azure 儲存體連接字串的應用程式設定名稱，而該連接字串用來管理基礎的 Azure 儲存體資源。|
|trackingStoreConnectionStringName||用於歷程記錄和執行個體資料表的連接字串名稱。 如果未指定，則會使用 `connectionStringName` (Durable 2.x) 或 `azureStorageConnectionStringName` (Durable 1.x) 連線。|
|trackingStoreNamePrefix||指定 `trackingStoreConnectionStringName` 時，用於歷程記錄和執行個體資料表的前置詞。 如果未設定，預設的前置詞值將會是 `DurableTask`。 如果未指定 `trackingStoreConnectionStringName`，則歷程記錄和執行個體資料表會使用 `hubName` 值作為其前置詞，而且會忽略 `trackingStoreNamePrefix` 的任何設定。|
|traceInputsAndOutputs |false|此值指出是否要追蹤函式呼叫的輸入和輸出。 追蹤函式執行事件時的預設行為就是在函式呼叫的序列化輸入和輸出中包含位元組數目。 此行為可提供輸入和輸出的最基本資訊，而不會讓記錄變大，或者不小心公開敏感性資訊。 將此屬性設定為 true，會導致預設函式記錄功能記錄函式輸入和輸出的整個內容。|
|logReplayEvents|false|此值可指出是否要將協調流程重新執行事件寫入 Application Insights。|
|eventGridTopicEndpoint ||Azure 事件方格自訂主題端點的 URL。 若已設定這個屬性，協調流程生命週期通知事件就會發佈到此端點。 這個屬性支援應用程式設定解析。|
|eventGridKeySettingName ||應用程式設定的名稱，其中包含在 `EventGridTopicEndpoint` 用來向 Azure 事件方格自訂主題進行驗證的金鑰。|
|eventGridPublishRetryCount|0|如果發佈到 Event Grid 主題失敗，重試的次數。|
|eventGridPublishRetryInterval|5 分鐘|「事件方格」會以 *hh:mm:ss* 格式發佈重試間隔。|
|eventGridPublishEventTypes||要發佈到事件方格的事件類型清單。 如果未指定，則會發佈所有事件種類。 允許的值包括 `Started`、`Completed`、`Failed` 或 `Terminated`。|
|useAppLease|true|設定為 `true` 時，應用程式必須在處理工作中樞訊息之前，取得應用程式層級的 Blob 租用。 如需詳細資訊，請參閱[災害復原和異地分佈](../articles/azure-functions/durable/durable-functions-disaster-recovery-geo-distribution.md)文件。 從 v2.3.0 開始可供使用。
|useLegacyPartitionManagement|true|設定為 `false` 時，會使用資料分割管理演算法，以降低相應放大時重複函式執行的可能性。從 v2.3.0 開始可供使用。 預設值將在未來的版本中變更為 `false`。|
|useGracefulShutdown|false|(預覽) 啟用正常關機以減少主機關機讓內含式函式執行失敗的機會。|

上述許多設定適用於將效能最佳化。 如需詳細資訊，請參閱[效能和擴充](../articles/azure-functions/durable-functions-perf-and-scale.md)。
