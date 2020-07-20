---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 2604a1608f21d7239db755027e15b8198fb3f9f2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81791661"
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
            }
        }
    }
}  
```

|屬性  |預設 | 描述 |
|---------|---------|---------|
|maxBatchSize|10|每個接收迴圈接收到的事件計數上限。|
|prefetchCount|300|基礎所使用的預設預先提取計數 `EventProcessorHost` 。|
|batchCheckpointFrequency|1|要在建立 EventHub 資料指標檢查點之前處理的事件批次數目。|

> [!NOTE]
> 如需 Azure Functions 2.x 和更高的中 host.js的參考，請參閱[Azure Functions 上的host.js參考](../articles/azure-functions/functions-host-json.md)。

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
|prefetchCount|n/a|基礎將使用的預設預先提取 `EventProcessorHost` 。| 
|batchCheckpointFrequency|1|要在建立 EventHub 資料指標檢查點之前處理的事件批次數目。| 

> [!NOTE]
> 如需 Azure Functions 1.x 中 host.js的參考，請參閱 Azure Functions 1.x 的[參考host.js](../articles/azure-functions/functions-host-json-v1.md)。

