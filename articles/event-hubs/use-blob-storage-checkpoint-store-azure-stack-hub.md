---
title: 使用 Blob 儲存為 Azure 堆疊中心上的檢查點儲存(預覽)
description: 本文介紹如何在 Azure 堆疊中心(預覽)的事件中心中使用 Blob 儲存作為檢查點存儲。
services: event-hubs
documentationcenter: na
author: spelluru
ms.service: event-hubs
ms.topic: how-to
ms.date: 03/18/2020
ms.author: spelluru
ms.openlocfilehash: 1f0e4dea44007ef82cb4b700ff0be4a5579541d8
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398918"
---
# <a name="use-blob-storage-as-checkpoint-store---event-hubs-on-azure-stack-hub-preview"></a>使用 Blob 儲存作為檢查點儲存 - Azure 堆疊中心的事件中心(預覽)
如果在支援與 Azure 上通常可用的版本的儲存 Blob SDK 不同的環境中使用 Azure Blob 儲存作為檢查點存儲,則需要使用代碼將儲存服務 API 版本更改為該環境支援的特定版本。 例如,如果您在[Azure 堆疊中心版本 2002 上運行事件中心](https://docs.microsoft.com/azure-stack/user/event-hubs-overview),則存儲服務的最高可用版本是版本 2017-11-09。 在這種情況下,您需要使用代碼將儲存服務 API 版本定位到 2017-11-09。 有關如何定位特定存儲 API 版本的範例,請參閱 GitHub 上的以下範例: 

- [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample10_RunningWithDifferentStorageVersion.cs)
- [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithOlderStorageVersion.java). 
- [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsWithDownleveledStorage.js)或[TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsWithDownleveledStorage.ts), 
- Python -[同步](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob/samples/event_processor_blob_storage_example_with_storage_api_version.py),[非同步](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/event_processor_blob_storage_example_with_storage_api_version.py)

> [!IMPORTANT]
> Azure 堆疊中心上的事件中心當前處於[預覽狀態](https://azure.microsoft.com/support/legal/preview-supplemental-terms/),並且是免費的。 

如果執行事件中心接收器,使用 Blob 儲存作為檢查點儲存,而不定位 Azure 堆疊中心支援的版本,您將收到以下錯誤訊息:

```
The value for one of the HTTP headers is not in the correct format
```


## <a name="sample-error-message-in-python"></a>Python 的範例錯誤訊息
對於 Python,`azure.core.exceptions.HttpResponseError`將錯誤傳遞給的`on_error(partition_context, error)``EventHubConsumerClient.receive()`錯誤 處理程式。 但是,該方法`receive()`不會引發異常。 `print(error)`將列印以下異常資訊:

```bash
The value for one of the HTTP headers is not in the correct format.

RequestId:f048aee8-a90c-08ba-4ce1-e69dba759297
Time:2020-03-17T22:04:13.3559296Z
ErrorCode:InvalidHeaderValue
Error:None
HeaderName:x-ms-version
HeaderValue:2019-07-07
```

記錄器將記錄兩個警告,如下所示:

```bash
WARNING:azure.eventhub.extensions.checkpointstoreblobaio._blobstoragecsaio: 
An exception occurred during list_ownership for namespace '<namespace-name>.eventhub.<region>.azurestack.corp.microsoft.com' eventhub 'python-eh-test' consumer group '$Default'. 

Exception is HttpResponseError('The value for one of the HTTP headers is not in the correct format.\nRequestId:f048aee8-a90c-08ba-4ce1-e69dba759297\nTime:2020-03-17T22:04:13.3559296Z\nErrorCode:InvalidHeaderValue\nError:None\nHeaderName:x-ms-version\nHeaderValue:2019-07-07')

WARNING:azure.eventhub.aio._eventprocessor.event_processor:EventProcessor instance '26d84102-45b2-48a9-b7f4-da8916f68214' of eventhub 'python-eh-test' consumer group '$Default'. An error occurred while load-balancing and claiming ownership. 

The exception is HttpResponseError('The value for one of the HTTP headers is not in the correct format.\nRequestId:f048aee8-a90c-08ba-4ce1-e69dba759297\nTime:2020-03-17T22:04:13.3559296Z\nErrorCode:InvalidHeaderValue\nError:None\nHeaderName:x-ms-version\nHeaderValue:2019-07-07'). Retrying after 71.45254944090853 seconds
```



## <a name="next-steps"></a>後續步驟

請參考以下文章瞭解分割區與檢查點:[平衡應用程式多個實體的分割區負載](event-processor-balance-partition-load.md)
