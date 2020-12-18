---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: f5101233f7995fb58fc530e613ba3235a55c783c
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97628664"
---
### <a name="functions-2x-and-higher"></a>Functions 2.x 和更新版本

```json
{
    "version": "2.0",
    "extensions": {
        "eventHubs": {
            "batchCheckpointFrequency": 5,
            "eventProcessorOptions": {
                "maxBatchSize": 256,
                "prefetchCount": 512
            },
            "initialOffsetOptions": {
                "type": "fromStart",
                "enqueuedTime": ""
            }
        }
    }
}  
```

|屬性  |預設 | 描述 |
|---------|---------|---------|
|batchCheckpointFrequency|1|要在建立 EventHub 資料指標檢查點之前處理的事件批次數目。|
|eventProcessorOptions/maxBatchSize|10|每個接收迴圈接收到的事件計數上限。|
|eventProcessorOptions/prefetchCount|300|基礎 `EventProcessorHost` 所使用的預設預先擷取計數。 允許的最小值為 10。|
|initialOffsetOptions/type|fromStart|當儲存體中沒有檢查點時，在事件資料流中要開始處理的位置。 選項為 `fromStart`、`fromEnd` 或 `fromEnqueuedTime`。 `fromEnd` 會處理在函數應用程式開始執行之後加入佇列的新事件。 適用於所有分割區。  如需詳細資訊，請參閱 [EventProcessorOptions 文件](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.initialoffsetprovider?view=azure-dotnet)。|
|initialOffsetOptions/enqueuedTime|N/A| 在要開始處理的資料流中，指定事件的加入佇列時間。 當 `initialOffsetOptions/type` 設定為 `fromEnqueuedTime` 時，這是必要設定。 支援 [DateTime.Parse()](/dotnet/standard/base-types/parsing-datetime) 所支援任何格式的時間，例如 `2020-10-26T20:31Z`。 為了清楚起見，您也應該指定時區。 若未指定時區，函式會假設執行函數應用程式電腦的當地時區，這在 Azure 上執行時是 UTC。 如需詳細資訊，請參閱 [EventProcessorOptions 文件](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.initialoffsetprovider?view=azure-dotnet)。|
> [!NOTE]
> 有關 Azure Functions 2.x 和更高版本中 host.json 的參考，請參閱[適用於 Azure Functions 的 host.json 參考](../articles/azure-functions/functions-host-json.md)。

### <a name="functions-1x"></a>Functions 1.x

```json
{
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    }
}
```

|屬性  |預設 | 描述 |
|---------|---------|---------| 
|maxBatchSize|64|每個接收迴圈接收到的事件計數上限。|
|prefetchCount|n/a|基礎 `EventProcessorHost` 將要使用的預設預先擷取。| 
|batchCheckpointFrequency|1|要在建立 EventHub 資料指標檢查點之前處理的事件批次數目。| 

> [!NOTE]
> 有關 Azure Functions 1.x 中 host.json 的參考，請參閱[適用於 Azure Functions 1.x 的 host.json 參考](../articles/azure-functions/functions-host-json-v1.md)。
