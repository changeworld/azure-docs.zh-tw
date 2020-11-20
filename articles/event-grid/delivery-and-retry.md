---
title: Azure Event Grid 傳遞與重試
description: 說明 Azure Event Grid 如何傳遞事件，以及如何處理未傳遞的訊息。
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: 7bf8fd3a647e28d18a7ca1e658761f9226d1153a
ms.sourcegitcommit: f311f112c9ca711d88a096bed43040fcdad24433
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94981097"
---
# <a name="event-grid-message-delivery-and-retry"></a>Event Grid 訊息傳遞與重試

本文章說明未確認傳遞時，Azure Event Grid 如何處理事件。

Event Grid 提供持久的傳遞。 它會針對每個訂用帳戶 **至少傳遞一次** 訊息。 事件會立即傳送到每個訂用帳戶的已註冊端點。 如果端點未確認接收事件，事件方格會重新嘗試傳遞事件。

> [!NOTE]
> 事件方格不保證事件傳遞的順序，所以訂閱者可能會依序接收它們。 

## <a name="batched-event-delivery"></a>批次事件傳遞

事件方格預設會將每個事件個別傳送給訂閱者。 訂閱者會收到包含單一事件的陣列。 您可以將事件方格設定為批次事件以進行傳遞，以改善高輸送量案例中的 HTTP 效能。

批次傳遞有兩個設定：

* **每批次的事件** 數上限-每個批次事件方格可傳遞的事件數目上限。 永遠不會超過此數目，但如果在發行時沒有其他可用的事件，則可能傳遞較少的事件。 事件方格不會延遲事件，以在有較少事件可用時建立批次。 必須介於1到5000之間。
* **建議** 的批次大小（以 kb 為單位），以 kb 為單位的批次大小上限。 類似于「最大事件」，如果在發佈時有更多事件無法使用，批次大小可能較小。 *如果* 單一事件大於慣用的大小，則批次可能會大於慣用的批次大小。 例如，如果慣用的大小為 4 KB，且將 10 KB 的事件推送至事件方格，則 10 KB 的事件仍會以自己的批次傳遞，而不會被捨棄。

透過入口網站、CLI、PowerShell 或 Sdk，以每個事件訂用帳戶為基礎設定批次傳遞。

### <a name="azure-portal"></a>Azure 入口網站： 
![批次傳遞設定](./media/delivery-and-retry/batch-settings.png)

### <a name="azure-cli"></a>Azure CLI
建立事件訂用帳戶時，請使用下列參數： 

- **每批次事件** 數上限-批次中的事件數目上限。 必須是介於1到5000之間的數位。
- **慣用-批次大小-以** kb 為單位的慣用批次大小（以 kb 為單位）。 必須是介於1到1024之間的數位。

```azurecli
storageid=$(az storage account show --name <storage_account_name> --resource-group <resource_group_name> --query id --output tsv)
endpoint=https://$sitename.azurewebsites.net/api/updates

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name <event_subscription_name> \
  --endpoint $endpoint \
  --max-events-per-batch 1000 \
  --preferred-batch-size-in-kilobytes 512
```

如需有關搭配事件方格使用 Azure CLI 的詳細資訊，請參閱使用 [Azure CLI 將儲存體事件路由至 web 端點](../storage/blobs/storage-blob-event-quickstart.md)。

## <a name="retry-schedule-and-duration"></a>重試排程和持續時間

事件方格會在傳遞訊息之後等候30秒的回應。 30秒之後，如果端點未回應，則會將訊息排入佇列等候重試。 Event Grid 針對事件傳遞使用指數輪詢重試原則。 事件方格會依下列排程重試傳遞：

- 10 秒
- 30 秒
- 1 分鐘
- 5 分鐘
- 10 分鐘
- 30 分鐘
- 1 小時
- 每小時最多24小時

如果端點在3分鐘內回應，事件方格會嘗試以最佳方式從重試佇列中移除事件，但仍會收到重複的專案。

事件方格會將小型隨機載入新增至所有重試步驟，而且可能會伺機略過特定的重試（如果端點持續狀況不良、很長一段時間），或看似被淹沒。

針對決定性的行為，請在訂用帳戶 [重試原則](manage-event-delivery.md)中設定事件存留時間和最大傳遞嘗試次數。

根據預設，事件格線會讓所有未在 24 小時內傳遞的事件變成過期事件。 您可以在建立事件訂用帳戶時[自訂重試原則](manage-event-delivery.md)。 您可以提供傳遞嘗試次數上限 (預設為 30 次) 和事件存留時間 (預設為 1440 分鐘)。

## <a name="delayed-delivery"></a>延遲傳遞

當端點遇到傳遞失敗時，事件方格將會開始延遲傳遞，並將事件重試到該端點。 例如，如果發佈到端點的前10個事件失敗，事件方格會假設端點發生問題，並會延遲所有後續的重試 *和新* 的傳遞，但在某些情況下，最多可達數小時。

延遲傳遞的功能性用途是保護狀況不良的端點以及事件方格系統。 如果沒有回復和傳遞到狀況不良的端點，事件方格的重試原則和磁片區功能可能會讓系統的負荷很慢。

## <a name="dead-letter-events"></a>無效信件事件
當事件方格無法在某段時間內或在嘗試傳遞事件一段時間之後，將事件傳送至儲存體帳戶時，它就可以將未傳遞的事件傳送至儲存體帳戶。 此處理程式稱為無效 **信件。** 當符合 **下列其中一個** 條件時，事件方格會失效。 

- 事件不會在生存 **時間** 期間傳遞。 
- 嘗試傳遞事件的 **次數** 已超過限制。

如果符合其中一個條件，就會捨棄事件或寄不出的信件。  根據預設，事件方格不會開啟無效信件處理。 若要啟用它，您必須指定儲存體帳戶在建立事件訂用帳戶時保留未傳遞事件。 您可從這個儲存體帳戶提取事件，以解析傳遞項目。

事件方格在試過所有重試嘗試後，會將事件傳送至無效信件位置。 如果事件方格收到 400 (錯誤的要求) 或 413 (要求實體太大) 回應碼，則會立即將事件傳送至無效信件端點。 這些回應碼表示事件傳遞會永遠不會成功。

只有在下次排程的傳遞嘗試時，才會檢查存留時間到期日。 因此，即使在下一次排程的傳遞嘗試之前，存留時間會過期，但只有在下一次傳遞時，才會檢查事件到期時間。 

在系統最後一次嘗試傳遞事件，以及將它傳遞至無效信件位置之間，具有五分鐘的延遲。 此延遲的目的是為了減少 Blob 儲存體的作業次數。 如果無效信件位置無法使用的時間達到四個小時，系統便會捨棄該事件。

在設定無效信件位置之前，您必須先有具備容器的儲存體帳戶。 建立事件訂用帳戶時，您必須提供這個容器的端點。 端點的格式如下：`/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/blobServices/default/containers/<container-name>`

您可以選擇在事件已傳送至無效信件位置時接收通知。 若要使用事件方格來回應未傳遞事件，請為無效信件 Blob 儲存體[建立事件訂用帳戶](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)。 每當您的無效信件 Blob 儲存體收到未傳遞事件時，事件方格就會通知您的處理常式。 處理常式會以您希望採取的動作來回應，以便協調未傳遞事件。 如需設定死信位置和重試原則的範例，請參閱 [死信和重試原則](manage-event-delivery.md)。

## <a name="delivery-event-formats"></a>傳遞事件格式
本節提供不同傳遞架構格式的事件和死信事件範例 (事件方格架構、CloudEvents 1.0 架構和自訂架構) 。 如需這些格式的詳細資訊，請參閱 [事件方格架構](event-schema.md) 和 [雲端事件1.0 架構](cloud-event-schema.md) 文章。 

### <a name="event-grid-schema"></a>事件格線結構描述

#### <a name="event"></a>事件 
```json
{
    "id": "93902694-901e-008f-6f95-7153a806873c",
    "eventTime": "2020-08-13T17:18:13.1647262Z",
    "eventType": "Microsoft.Storage.BlobCreated",
    "dataVersion": "",
    "metadataVersion": "1",
    "topic": "/subscriptions/000000000-0000-0000-0000-00000000000000/resourceGroups/rgwithoutpolicy/providers/Microsoft.Storage/storageAccounts/myegteststgfoo",
    "subject": "/blobServices/default/containers/deadletter/blobs/myBlobFile.txt",    
    "data": {
        "api": "PutBlob",
        "clientRequestId": "c0d879ad-88c8-4bbe-8774-d65888dc2038",
        "requestId": "93902694-901e-008f-6f95-7153a8000000",
        "eTag": "0x8D83FACDC0C3402",
        "contentType": "text/plain",
        "contentLength": 0,
        "blobType": "BlockBlob",
        "url": "https://myegteststgfoo.blob.core.windows.net/deadletter/myBlobFile.txt",
        "sequencer": "00000000000000000000000000015508000000000005101c",
        "storageDiagnostics": { "batchId": "cfb32f79-3006-0010-0095-711faa000000" }
    }
}
```

#### <a name="dead-letter-event"></a>死信事件

```json
{
    "id": "93902694-901e-008f-6f95-7153a806873c",
    "eventTime": "2020-08-13T17:18:13.1647262Z",
    "eventType": "Microsoft.Storage.BlobCreated",
    "dataVersion": "",
    "metadataVersion": "1",
    "topic": "/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/rgwithoutpolicy/providers/Microsoft.Storage/storageAccounts/myegteststgfoo",
    "subject": "/blobServices/default/containers/deadletter/blobs/myBlobFile.txt",    
    "data": {
        "api": "PutBlob",
        "clientRequestId": "c0d879ad-88c8-4bbe-8774-d65888dc2038",
        "requestId": "93902694-901e-008f-6f95-7153a8000000",
        "eTag": "0x8D83FACDC0C3402",
        "contentType": "text/plain",
        "contentLength": 0,
        "blobType": "BlockBlob",
        "url": "https://myegteststgfoo.blob.core.windows.net/deadletter/myBlobFile.txt",
        "sequencer": "00000000000000000000000000015508000000000005101c",
        "storageDiagnostics": { "batchId": "cfb32f79-3006-0010-0095-711faa000000" }
    },

    "deadLetterReason": "MaxDeliveryAttemptsExceeded",
    "deliveryAttempts": 1,
    "lastDeliveryOutcome": "NotFound",
    "publishTime": "2020-08-13T17:18:14.0265758Z",
    "lastDeliveryAttemptTime": "2020-08-13T17:18:14.0465788Z" 
}
```

### <a name="cloudevents-10-schema"></a>CloudEvents 1.0 架構

#### <a name="event"></a>事件

```json
{
    "id": "caee971c-3ca0-4254-8f99-1395b394588e",
    "source": "mysource",
    "dataversion": "1.0",
    "subject": "mySubject",
    "type": "fooEventType",
    "datacontenttype": "application/json",
    "data": {
        "prop1": "value1",
        "prop2": 5
    }
}
```

#### <a name="dead-letter-event"></a>死信事件

```json
{
    "id": "caee971c-3ca0-4254-8f99-1395b394588e",
    "source": "mysource",
    "dataversion": "1.0",
    "subject": "mySubject",
    "type": "fooEventType",
    "datacontenttype": "application/json",
    "data": {
        "prop1": "value1",
        "prop2": 5
    },

    "deadletterreason": "MaxDeliveryAttemptsExceeded",
    "deliveryattempts": 1,
    "lastdeliveryoutcome": "NotFound",
    "publishtime": "2020-08-13T21:21:36.4018726Z",
}
```

### <a name="custom-schema"></a>自訂架構

#### <a name="event"></a>事件

```json
{
    "prop1": "my property",
    "prop2": 5,
    "myEventType": "fooEventType"
}

```

#### <a name="dead-letter-event"></a>死信事件
```json
{
    "id": "8bc07e6f-0885-4729-90e4-7c3f052bd754",
    "eventTime": "2020-08-13T18:11:29.4121391Z",
    "eventType": "myEventType",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/00000000000-0000-0000-0000-000000000000000/resourceGroups/rgwithoutpolicy/providers/Microsoft.EventGrid/topics/myCustomSchemaTopic",
    "subject": "subjectDefault",
  
    "deadLetterReason": "MaxDeliveryAttemptsExceeded",
    "deliveryAttempts": 1,
    "lastDeliveryOutcome": "NotFound",
    "publishTime": "2020-08-13T18:11:29.4121391Z",
    "lastDeliveryAttemptTime": "2020-08-13T18:11:29.4277644Z",
  
    "data": {
        "prop1": "my property",
        "prop2": 5,
        "myEventType": "fooEventType"
    }
}
```


## <a name="message-delivery-status"></a>訊息傳遞狀態

Event Grid 使用 HTTP 回應碼以確認接收事件。 

### <a name="success-codes"></a>成功碼

事件方格會 **將下列** HTTP 回應碼視為成功傳遞。 所有其他狀態碼會視為失敗的傳遞，並會視需要重試或寄不出。 收到成功的狀態碼時，事件方格會將傳遞視為完成。

- 200 確定
- 201 Created
- 202 已接受
- 203非授權資訊
- 204 沒有內容

### <a name="failure-codes"></a>失敗碼

不在上述集合中的所有其他代碼 (200-204) 會視為失敗，並會重試。 有些特定的重試原則會系結至下面所述的原則，而其他則會遵循標準指數的復原模式。 請務必記住，由於事件方格架構的高度平行化本質，因此重試行為不具決定性。 

| 狀態碼 | 重試行為 |
| ------------|----------------|
| 400 不正確的要求 | 在5分鐘後重試一次或更多 (在 Deadletter 安裝) 時立即 Deadletter |
| 401 未經授權 | 在5分鐘後重試 |
| 403 禁止 | 在5分鐘後重試 |
| 404 找不到 | 在5分鐘後重試 |
| 408 要求逾時 | 2分鐘後重試 |
| 413 要求實體太大 | 如果 Deadletter 安裝) ，請在10秒後重試 (Deadletter |
| 503 服務無法使用 | 30秒後再重試 |
| All others | 10秒後重試 |


## <a name="next-steps"></a>後續步驟

* 若要檢視事件傳遞的狀態，請參閱[監視 Event Grid 訊息傳遞](monitor-event-delivery.md)。
* 若要自訂事件傳遞選項，請參閱[無效信件和重試原則](manage-event-delivery.md)。
