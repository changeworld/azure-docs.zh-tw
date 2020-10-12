---
title: '在 Azure Stack Hub (preview 上使用 Blob 儲存體作為檢查點存放區) '
description: 本文說明如何在 Azure Stack Hub (preview) 的事件中樞中，使用 Blob 儲存體作為檢查點存放區。
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 1c876ed36be6aec9395a1acba3a1deb25a47de2c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87039220"
---
# <a name="use-blob-storage-as-checkpoint-store---event-hubs-on-azure-stack-hub-preview"></a>使用 Blob 儲存體作為檢查點存放區-Azure Stack Hub (預覽) 的事件中樞
如果您使用 Azure Blob 儲存體作為環境中的檢查點存放區，其支援的儲存體 Blob SDK 版本與通常在 Azure 上提供的不同，您將需要使用程式碼將儲存體服務 API 版本變更為該環境所支援的特定版本。 例如，如果您是 [在 Azure Stack Hub 2002 版上執行事件中樞](/azure-stack/user/event-hubs-overview)，則儲存體服務的最高可用版本是2017-11-09 版。 在此情況下，您必須使用程式碼將儲存體服務 API 版本的目標設為2017-11-09。 如需如何以特定儲存體 API 版本為目標的範例，請參閱 GitHub 上的下列範例： 

- [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample10_RunningWithDifferentStorageVersion.cs)
- [JAVA](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithCustomStorageVersion.java)。 
- [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript/receiveEventsWithApiSpecificStorage.js) 或  [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript/src/receiveEventsWithApiSpecificStorage.ts) 
- Python- [同步](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob/samples/receive_events_using_checkpoint_store_storage_api_version.py)、 [非同步](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/receive_events_using_checkpoint_store_storage_api_version_async.py)

> [!IMPORTANT]
> Azure Stack Hub 上的事件中樞目前為 [預覽](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 狀態，而且是免費的。 

如果您執行的事件中樞接收器使用 Blob 儲存體作為檢查點存放區，而不是以 Azure Stack Hub 支援的版本為目標，您將會收到下列錯誤訊息：

```
The value for one of the HTTP headers is not in the correct format
```


## <a name="sample-error-message-in-python"></a>Python 中的範例錯誤訊息
針對 Python，會將的錯誤傳遞給的 `azure.core.exceptions.HttpResponseError` 錯誤處理 `on_error(partition_context, error)` 程式 `EventHubConsumerClient.receive()` 。 但是，方法 `receive()` 不會引發例外狀況。 `print(error)` 將會列印下列例外狀況資訊：

```bash
The value for one of the HTTP headers is not in the correct format.

RequestId:f048aee8-a90c-08ba-4ce1-e69dba759297
Time:2020-03-17T22:04:13.3559296Z
ErrorCode:InvalidHeaderValue
Error:None
HeaderName:x-ms-version
HeaderValue:2019-07-07
```

記錄器會記錄兩個警告，如下所示：

```bash
WARNING:azure.eventhub.extensions.checkpointstoreblobaio._blobstoragecsaio: 
An exception occurred during list_ownership for namespace '<namespace-name>.eventhub.<region>.azurestack.corp.microsoft.com' eventhub 'python-eh-test' consumer group '$Default'. 

Exception is HttpResponseError('The value for one of the HTTP headers is not in the correct format.\nRequestId:f048aee8-a90c-08ba-4ce1-e69dba759297\nTime:2020-03-17T22:04:13.3559296Z\nErrorCode:InvalidHeaderValue\nError:None\nHeaderName:x-ms-version\nHeaderValue:2019-07-07')

WARNING:azure.eventhub.aio._eventprocessor.event_processor:EventProcessor instance '26d84102-45b2-48a9-b7f4-da8916f68214' of eventhub 'python-eh-test' consumer group '$Default'. An error occurred while load-balancing and claiming ownership. 

The exception is HttpResponseError('The value for one of the HTTP headers is not in the correct format.\nRequestId:f048aee8-a90c-08ba-4ce1-e69dba759297\nTime:2020-03-17T22:04:13.3559296Z\nErrorCode:InvalidHeaderValue\nError:None\nHeaderName:x-ms-version\nHeaderValue:2019-07-07'). Retrying after 71.45254944090853 seconds
```



## <a name="next-steps"></a>接下來的步驟

請參閱下列文章瞭解分割和檢查點：在 [應用程式的多個實例之間平衡資料分割負載](event-processor-balance-partition-load.md)
