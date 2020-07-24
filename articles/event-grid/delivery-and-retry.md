---
title: Azure Event Grid 傳遞與重試
description: 說明 Azure Event Grid 如何傳遞事件，以及如何處理未傳遞的訊息。
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: fe7574d7e17b1763afb2292c15007dd87b056ef1
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87087606"
---
# <a name="event-grid-message-delivery-and-retry"></a>Event Grid 訊息傳遞與重試

本文章說明未確認傳遞時，Azure Event Grid 如何處理事件。

Event Grid 提供持久的傳遞。 它針對每個訂用帳戶傳遞每則訊息至少一次。 事件會立即傳送到每個訂用帳戶的已註冊端點。 如果端點未確認接收事件，事件方格會重新嘗試傳遞事件。

## <a name="batched-event-delivery"></a>批次事件傳遞

「事件方格」預設會將每個事件個別傳送給「訂閱者」。 訂閱者會收到包含單一事件的陣列。 您可以設定事件方格來批次事件以進行傳遞，以改善高輸送量案例中的 HTTP 效能。

批次傳遞有兩個設定：

* **每個批次的事件**上限-事件方格每個批次的最大事件數目。 永遠不會超過此數目，但如果在發行時沒有其他可用的事件，可能會傳遞較少的事件。 事件方格不會延遲事件，以便在有較少的事件可用時建立批次。 必須介於1到5000之間。
* **慣用的批次大小（kb** ）-批次大小的目標上限（以 kb 為單位）。 與 max 事件類似，如果發佈時有更多事件無法使用，批次大小可能會較小。 *如果*單一事件大於慣用的大小，則批次可能會大於慣用的批次大小。 例如，如果慣用的大小是 4 KB，而 10 KB 的事件會推送至事件方格，則 10 KB 的事件仍會以自己的批次傳遞，而不會被捨棄。

中的批次傳遞會透過入口網站、CLI、PowerShell 或 Sdk，以每個事件訂用帳戶為基礎進行設定。

### <a name="azure-portal"></a>Azure 入口網站： 
![批次傳遞設定](./media/delivery-and-retry/batch-settings.png)

### <a name="azure-cli"></a>Azure CLI
建立事件訂用帳戶時，請使用下列參數： 

- **最大-每個批次事件**數-批次中的事件數目上限。 必須是介於1到5000之間的數位。
- **慣用-批次大小-以 kb**為單位的慣用批次大小（以 kb 為單位）。 必須是介於1到1024之間的數位。

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

如需有關使用 Azure CLI 搭配事件方格的詳細資訊，請參閱使用[Azure CLI 將儲存體事件路由至 web 端點](../storage/blobs/storage-blob-event-quickstart.md)。

## <a name="retry-schedule-and-duration"></a>重試排程和持續時間

事件方格在傳遞訊息之後，會等待30秒的回應。 30秒後，如果端點尚未回應，則會將訊息排入佇列以進行重試。 Event Grid 針對事件傳遞使用指數輪詢重試原則。 事件方格會以最大的方式來重試傳遞下列排程：

- 10 秒
- 30 秒
- 1 分鐘
- 5 分鐘
- 10 分鐘
- 30 分鐘
- 1 小時
- 最多24小時的每小時

如果端點在3分鐘內回應，事件方格將會嘗試盡最大努力從重試佇列中移除事件，但仍可能會收到重複的專案。

事件方格會在所有重試步驟中新增小型隨機載入，而且如果端點持續狀況不良、長時間下降，或似乎有過多的情況，可能會都伺機略過某些重試。

針對具決定性的行為，請將「事件存留時間」設定為「[訂用帳戶重試原則](manage-event-delivery.md)」中的最大傳遞嘗試

根據預設，事件格線會讓所有未在 24 小時內傳遞的事件變成過期事件。 您可以在建立事件訂用帳戶時[自訂重試原則](manage-event-delivery.md)。 您可以提供傳遞嘗試次數上限 (預設為 30 次) 和事件存留時間 (預設為 1440 分鐘)。

## <a name="delayed-delivery"></a>延遲傳遞

當端點發生傳遞失敗時，事件方格會開始延遲傳遞，然後再重試該端點的事件。 例如，如果發佈至端點的前10個事件失敗，事件方格會假設端點發生問題，並會延遲一段時間的所有後續重試*和新*傳遞-在某些情況下，最多可達數小時。

延遲傳遞的功能目的是要保護狀況不良的端點，以及事件方格系統。 如果沒有輪詢和延遲傳遞至狀況不良的端點，事件方格的重試原則和磁片區功能就很容易就會造成系統的負荷。

## <a name="dead-letter-events"></a>無效信件事件
當事件方格無法在特定期間內或在嘗試傳遞事件一段特定的時間之後傳遞事件時，它可以將無法傳遞的事件傳送至儲存體帳戶。 此**程式**稱為無效信件處理。 當符合**下列其中一**項條件時，事件方格會將事件失效。 

- 事件不會在存留時間期間內傳遞
- 嘗試傳遞事件的次數已超過限制

如果符合其中一個條件，就會捨棄或不正確事件。  根據預設，事件方格不會開啟無效信件處理。 若要啟用它，您必須指定儲存體帳戶在建立事件訂用帳戶時保留未傳遞事件。 您可從這個儲存體帳戶提取事件，以解析傳遞項目。

事件方格在試過所有重試嘗試後，會將事件傳送至無效信件位置。 如果事件方格收到 400 (錯誤的要求) 或 413 (要求實體太大) 回應碼，則會立即將事件傳送至無效信件端點。 這些回應碼表示事件傳遞會永遠不會成功。

在系統最後一次嘗試傳遞事件，以及將它傳遞至無效信件位置之間，具有五分鐘的延遲。 此延遲的目的是為了減少 Blob 儲存體的作業次數。 如果無效信件位置無法使用的時間達到四個小時，系統便會捨棄該事件。

在設定無效信件位置之前，您必須先有具備容器的儲存體帳戶。 建立事件訂用帳戶時，您必須提供這個容器的端點。 端點的格式如下：`/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/blobServices/default/containers/<container-name>`

您可以選擇在事件已傳送至無效信件位置時接收通知。 若要使用事件方格來回應未傳遞事件，請為無效信件 Blob 儲存體[建立事件訂用帳戶](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)。 每當您的無效信件 Blob 儲存體收到未傳遞事件時，事件方格就會通知您的處理常式。 處理常式會以您希望採取的動作來回應，以便協調未傳遞事件。

如需無效信件位置的設定範例，請參閱[無效信件和重試原則](manage-event-delivery.md)。

## <a name="message-delivery-status"></a>訊息傳遞狀態

Event Grid 使用 HTTP 回應碼以確認接收事件。 

### <a name="success-codes"></a>成功碼

事件方格**只**會將下列 HTTP 回應碼視為成功傳遞。 所有其他狀態碼都會被視為傳遞失敗，並會適當地重試或寄不出。 當收到成功的狀態碼時，事件方格會將傳遞視為完成。

- 200 確定
- 201 Created
- 202 已接受
- 203非授權資訊
- 204 沒有內容

### <a name="failure-codes"></a>失敗碼

不在上述集合中的所有其他代碼（200-204）都會被視為失敗，並會重試。 有些特定的重試原則系結到下面所述，其他則遵循標準的指數退避模式。 請務必記住，由於事件方格架構的高度平行化本質，重試行為不具決定性。 

| 狀態碼 | 重試行為 |
| ------------|----------------|
| 400 不正確的要求 | 在5分鐘或更久之後重試（如果 Deadletter 設定，請立即 Deadletter） |
| 401 未經授權 | 在5分鐘或更久之後重試 |
| 403 禁止 | 在5分鐘或更久之後重試 |
| 404 找不到 | 在5分鐘或更久之後重試 |
| 408 要求逾時 | 2分鐘後重試 |
| 413 要求實體太大 | 10秒以上後重試（如果 Deadletter 設定，請立即 Deadletter） |
| 503 服務無法使用 | 30秒後重試 |
| All others | 10秒後重試 |


## <a name="next-steps"></a>後續步驟

* 若要檢視事件傳遞的狀態，請參閱[監視 Event Grid 訊息傳遞](monitor-event-delivery.md)。
* 若要自訂事件傳遞選項，請參閱[無效信件和重試原則](manage-event-delivery.md)。
