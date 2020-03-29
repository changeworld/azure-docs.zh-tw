---
title: Azure Event Grid 傳遞與重試
description: 說明 Azure Event Grid 如何傳遞事件，以及如何處理未傳遞的訊息。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 02/27/2020
ms.author: spelluru
ms.openlocfilehash: dda2fd98c4c0d330059156a5ec00baa97ffaf627
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921057"
---
# <a name="event-grid-message-delivery-and-retry"></a>Event Grid 訊息傳遞與重試

本文章說明未確認傳遞時，Azure Event Grid 如何處理事件。

Event Grid 提供持久的傳遞。 它針對每個訂用帳戶傳遞每則訊息至少一次。 事件會立即傳送到每個訂用帳戶的已註冊端點。 如果端點未確認接收事件，事件方格會重新嘗試傳遞事件。

## <a name="batched-event-delivery"></a>批次處理事件傳遞

事件網格預設將每個事件單獨發送給訂閱者。 訂閱者會收到包含單一事件的陣列。 您可以將事件網格配置為批次處理事件，以便交付，從而在高通量方案中提高 HTTP 性能。

批次處理交付有兩個設置：

* **每個批次處理的最大事件**數 - 事件網格將每個批次處理提供的最大事件數。 永遠不會超過此數位，但如果發佈時沒有其他事件可用，則傳遞的事件可能更少。 事件網格不會延遲事件，以便在可用事件較少時創建批次處理。 必須在 1 到 5，000 之間。
* **首選批次處理大小（以千位元組為單位**） - 以千位元組為單位的批次處理大小的目標上限。 與 max 事件類似，如果在發佈時沒有更多事件，批次處理大小可能會更小。 *如果*單個事件大於首選大小，則批次處理可能大於首選批次處理大小。 例如，如果首選大小為 4 KB，並且將 10 KB 事件推送到事件網格，則 10 KB 事件仍將以自己的批次處理提供，而不是被丟棄。

通過門戶、CLI、PowerShell 或 SDK 按每個事件訂閱配置的批次處理交付。

### <a name="azure-portal"></a>Azure 入口網站： 
![批次處理交付設置](./media/delivery-and-retry/batch-settings.png)

### <a name="azure-cli"></a>Azure CLI
創建事件訂閱時，請使用以下參數： 

- **每批的最大事件**數 - 批次處理中事件的最大數量。 必須是介於 1 和 5000 之間的數位。
- **首選批次大小（以千位元組**為單位 ） - 首選批次大小（以千位元組為單位）。 必須是介於 1 和 1024 之間的數位。

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

有關將 Azure CLI 與事件網格一起使用的詳細資訊，請參閱[使用 Azure CLI 將存儲事件路由到 Web 終結點](../storage/blobs/storage-blob-event-quickstart.md)。

## <a name="retry-schedule-and-duration"></a>重試排程和持續時間

事件網格在傳遞消息後等待 30 秒以等待回應。 30 秒後，如果終結點未回應，則消息將排隊重試。 Event Grid 針對事件傳遞使用指數輪詢重試原則。 事件網格以最佳方式按照以下計畫重試交付：

- 10 秒
- 30 秒
- 1 分鐘
- 5 分鐘
- 10 分鐘
- 30 分鐘
- 1 小時
- 每小時長達 24 小時

如果終結點在 3 分鐘內回應，事件網格將嘗試盡最大努力從重試佇列中刪除事件，但仍可能收到重複項。

事件網格為所有重試步驟添加一個小隨機化，如果終結點始終不正常、長時間關閉或似乎不堪重負，則可能會機會性地跳過某些重試。

對於確定性行為，將事件時間設置為[訂閱重試策略](manage-event-delivery.md)中的即時和最大傳遞嘗試。

根據預設，事件格線會讓所有未在 24 小時內傳遞的事件變成過期事件。 您可以在建立事件訂用帳戶時[自訂重試原則](manage-event-delivery.md)。 您可以提供傳遞嘗試次數上限 (預設為 30 次) 和事件存留時間 (預設為 1440 分鐘)。

## <a name="delayed-delivery"></a>延遲交貨

當終結點遇到傳遞失敗時，事件網格將開始延遲向該終結點傳遞和重試事件。 例如，如果發佈到終結點的前 10 個事件失敗，事件網格將假定終結點遇到問題，並將延遲所有後續重試*和新*交付一段時間 - 在某些情況下長達幾個小時。

延遲交付的功能目的是保護不正常的終結點以及事件網格系統。 如果沒有回退和延遲傳遞到不正常的終結點，事件網格的重試策略和卷功能很容易使系統不堪重負。

## <a name="dead-letter-events"></a>無效信件事件

當事件方格無法傳遞事件時，它可以將未傳遞事件傳送到儲存體帳戶。 這個程序稱為無效信件處理。 根據預設，事件方格不會開啟無效信件處理。 若要啟用它，您必須指定儲存體帳戶在建立事件訂用帳戶時保留未傳遞事件。 您可從這個儲存體帳戶提取事件，以解析傳遞項目。

事件方格在試過所有重試嘗試後，會將事件傳送至無效信件位置。 如果事件方格收到 400 (錯誤的要求) 或 413 (要求實體太大) 回應碼，則會立即將事件傳送至無效信件端點。 這些回應碼表示事件傳遞會永遠不會成功。

在系統最後一次嘗試傳遞事件，以及將它傳遞至無效信件位置之間，具有五分鐘的延遲。 此延遲的目的是為了減少 Blob 儲存體的作業次數。 如果無效信件位置無法使用的時間達到四個小時，系統便會捨棄該事件。

在設定無效信件位置之前，您必須先有具備容器的儲存體帳戶。 建立事件訂用帳戶時，您必須提供這個容器的端點。 端點的格式如下：`/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/blobServices/default/containers/<container-name>`

您可以選擇在事件已傳送至無效信件位置時接收通知。 若要使用事件方格來回應未傳遞事件，請為無效信件 Blob 儲存體[建立事件訂用帳戶](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)。 每當您的無效信件 Blob 儲存體收到未傳遞事件時，事件方格就會通知您的處理常式。 處理常式會以您希望採取的動作來回應，以便協調未傳遞事件。

如需無效信件位置的設定範例，請參閱[無效信件和重試原則](manage-event-delivery.md)。

## <a name="message-delivery-status"></a>訊息傳遞狀態

Event Grid 使用 HTTP 回應碼以確認接收事件。 

### <a name="success-codes"></a>成功碼

事件網格**僅**將以下 HTTP 回應代碼視為成功交付。 所有其他狀態碼被視為未送達，將酌情重試或死信。 收到成功狀態碼後，事件網格認為交付已完成。

- 200 確定
- 201 Created
- 202 已接受
- 203 非權威資訊
- 204 沒有內容

### <a name="failure-codes"></a>失敗碼

未在上述集 （200-204） 中的所有其他代碼都被視為失敗，並將重試。 有些有特定的重試策略，它們如下所示，所有其他策略都遵循標準指數回退模型。 請務必記住，由於事件網格體系結構的高度並行化特性，重試行為是非確定性行為。 

| 狀態碼 | 重試行為 |
| ------------|----------------|
| 400 不正確的要求 | 5 分鐘或更長時間後重試（如果設置死信，請立即重試） |
| 401 未經授權 | 5 分鐘或更長時間後重試 |
| 403 禁止 | 5 分鐘或更長時間後重試 |
| 404 找不到 | 5 分鐘或更長時間後重試 |
| 408 要求逾時 | 2 分鐘或更長時間後重試 |
| 413 要求實體太大 | 10 秒或更長時間後重試（如果設置死信，請立即重試） |
| 503 服務無法使用 | 30 秒或更長時間後重試 |
| All others | 10 秒或更長時間後重試 |


## <a name="next-steps"></a>後續步驟

* 若要檢視事件傳遞的狀態，請參閱[監視 Event Grid 訊息傳遞](monitor-event-delivery.md)。
* 若要自訂事件傳遞選項，請參閱[無效信件和重試原則](manage-event-delivery.md)。
