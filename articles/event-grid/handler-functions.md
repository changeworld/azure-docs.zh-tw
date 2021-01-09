---
title: 在 Azure 中使用函式作為 Azure 事件方格事件的事件處理常式
description: 描述如何使用 Azure Functions 中建立的函式，做為事件方格事件的事件處理常式。
ms.topic: conceptual
ms.date: 09/18/2020
ms.openlocfilehash: 5a1ec575b58829a422e4d263ae0324e0343d5ad3
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98034963"
---
# <a name="use-a-function-as-an-event-handler-for-event-grid-events"></a>使用函式做為事件方格事件的事件處理常式

事件處理常式是傳送事件的位置。 處理常式會採取相關動作來處理事件。 有幾項 Azure 服務已自動設定為會處理事件，**Azure Functions** 是其中之一。 


若要在 Azure 中使用函式作為事件的處理常式，請遵循下列其中一種方法： 

-   使用 [事件方格觸發](../azure-functions/functions-bindings-event-grid-trigger.md)程式。  將 **Azure 函數** 指定為 **端點類型**。 然後，指定函式應用程式和將處理事件的函式。 
-   使用 [HTTP 觸發](../azure-functions/functions-bindings-http-webhook.md)程式。  將 **webhook 指定為****端點類型**。 然後，指定將處理事件的函式 URL。 

建議您使用第一種方法 (事件方格觸發程式) ，因為它在第二個方法中具有下列優點：
-   事件方格會自動驗證事件方格觸發程序。 若要使用 HTTP 觸發程序，您必須自行實作[驗證回應](webhook-event-delivery.md)。
-   事件方格會根據函數可處理事件的觀察速率，自動調整事件傳送到事件方格事件所觸發之函式的速率。 此速率相符功能 averts 傳遞錯誤，這些錯誤源自于函式無法處理事件，因為函式的事件處理速度會隨著時間而改變。 若要以高輸送量提高效率，請在事件訂用帳戶上啟用批次處理。 如需詳細資訊，請參閱 [啟用批次處理](#enable-batching)。

    > [!NOTE]
    > 目前，當事件在 **CloudEvents** 架構中傳遞時，您無法針對函數應用程式使用事件方格觸發程式。 相反地，請使用 HTTP 觸發程序。

## <a name="tutorials"></a>教學課程

|Title  |描述  |
|---------|---------|
| [快速入門：使用函式處理事件](custom-event-to-function.md) | 將自訂事件傳送至函式進行處理。 |
| [教學課程：使用事件方格自動調整已上傳映像的大小](resize-images-on-storage-blob-upload-event.md) | 使用者透過 Web 應用程式將映像上傳至儲存體帳戶。 建立儲存體 Blob 時，事件方格會傳送一個事件給函式應用程式，讓其調整上傳映像的大小。 |
| [教學課程：將巨量資料串流處理至資料倉儲](event-grid-event-hubs-integration.md) | 當事件中樞建立「擷取」檔案時，事件方格會傳送一個事件至函式應用程式。 應用程式會對「擷取」檔案進行擷取，並將資料遷移到資料倉儲。 |
| [教學課程：Azure 服務匯流排與 Azure 事件方格的整合範例](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | 事件方格會將訊息從服務匯流排主題傳送至函式應用程式和邏輯應用程式。 |

## <a name="rest-example-for-put"></a>REST 範例 (用於 PUT)

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "AzureFunction",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Web/sites/<FUNCTION APP NAME>/functions/<FUNCTION NAME>",
                "maxEventsPerBatch": 10,
                "preferredBatchSizeInKilobytes": 6400
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="enable-batching"></a>啟用批次
針對較高的輸送量，請在訂用帳戶上啟用批次處理。 如果您使用 Azure 入口網站，可以設定每個批次的最大事件數目，以及在建立訂閱時或建立之後的慣用批次大小（以 kb 為單位）。 

您可以使用 Azure 入口網站、PowerShell、CLI 或 Resource Manager 範本來設定批次設定。 

### <a name="azure-portal"></a>Azure 入口網站
當您在 UI 中建立訂用帳戶時，請在 [**建立事件訂閱**] 頁面上切換至 [ **Advanced Features** ] 索引標籤，並設定 **每個批次的最大事件數目** 和 **慣用批次大小（以 kb 為單位）** 
    
:::image type="content" source="./media/custom-event-to-function/enable-batching.png" alt-text="在建立訂閱時啟用批次處理":::

您可以在 **事件方格主題** 頁面的 [**功能**] 索引標籤上，更新現有訂用帳戶的這些值。 

:::image type="content" source="./media/custom-event-to-function/features-batch-settings.png" alt-text="在建立之後啟用批次處理":::

### <a name="azure-resource-manager-template"></a>Azure Resource Manager 範本
您可以在 Azure Resource Manager 範本中設定 **maxEventsPerBatch** 和 **preferredBatchSizeInKilobytes** 。 如需詳細資訊，請參閱 [EventGrid eventSubscriptions 範本參考](/azure/templates/microsoft.eventgrid/eventsubscriptions)。

### <a name="azure-cli"></a>Azure CLI
您可以使用 [az eventgrid event-訂](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_create&preserve-view=true) 用帳戶建立或 [az eventgrid 事件訂](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_update&preserve-view=true) 用帳戶更新命令，以使用下列參數來設定批次相關設定： `--max-events-per-batch` 或 `--preferred-batch-size-in-kilobytes` 。

### <a name="azure-powershell"></a>Azure PowerShell
您可以使用 [AzEventGridSubscription](/powershell/module/az.eventgrid/new-azeventgridsubscription) 或 [AzEventGridSubscription](/powershell/module/az.eventgrid/update-azeventgridsubscription) Cmdlet，利用下列參數來設定批次相關設定： `-MaxEventsPerBatch` 或 `-PreferredBatchSizeInKiloBytes` 。

## <a name="next-steps"></a>後續步驟
如需支援的事件處理常式清單，請參閱[事件處理常式](event-handlers.md)一文。
