---
title: 以 Azure 函式作為 Azure 事件方格事件的事件處理常式
description: 說明如何使用 Azure 函式作為「事件方格」事件的事件處理常式。
ms.topic: conceptual
ms.date: 09/18/2020
ms.openlocfilehash: db06962c020eb954bf0c595e5a4019b1df774898
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/01/2020
ms.locfileid: "91629683"
---
# <a name="azure-function-as-an-event-handler-for-event-grid-events"></a>以 Azure 函式作為事件方格事件的事件處理常式

事件處理常式是傳送事件的位置。 處理常式會採取相關動作來處理事件。 有幾項 Azure 服務已自動設定為會處理事件，**Azure Functions** 是其中之一。 

請在無伺服器架構中使用 **Azure Functions**，以回應事件方格中的事件。 使用 Azure Functions 作為處理常式時，請使用事件方格觸發程序，而不是一般 HTTP 觸發程序。 事件方格會自動驗證事件方格觸發程序。 若要使用 HTTP 觸發程序，您必須自行實作[驗證回應](webhook-event-delivery.md)。

如需詳細資訊，請參閱 [Azure Functions 的事件方格觸發程序](../azure-functions/functions-bindings-event-grid.md)，以取得在函式中使用事件方格觸發程序的概觀。

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

## <a name="enable-batching"></a>啟用批次處理
針對較高的輸送量，請在訂用帳戶上啟用批次處理。 如果您使用 Azure 入口網站，可以設定每個批次的最大事件數目，以及在建立訂閱時或建立之後的慣用批次大小（以 kb 為單位）。 

您可以使用 Azure 入口網站、PowerShell、CLI 或 Resource Manager 範本來設定批次設定。 

### <a name="azure-portal"></a>Azure 入口網站
當您在 UI 中建立訂用帳戶時，請在 [**建立事件訂閱**] 頁面上切換至 [ **Advanced Features** ] 索引標籤，並設定**每個批次的最大事件數目**和**慣用批次大小（以 kb 為單位）** 
    
:::image type="content" source="./media/custom-event-to-function/enable-batching.png" alt-text="在建立訂閱時啟用批次處理":::

您可以在**事件方格主題**頁面的 [**功能**] 索引標籤上，更新現有訂用帳戶的這些值。 

:::image type="content" source="./media/custom-event-to-function/features-batch-settings.png" alt-text="在建立訂閱時啟用批次處理":::

### <a name="azure-resource-manager-template"></a>Azure Resource Manager 範本
您可以在 Azure Resource Manager 範本中設定 **maxEventsPerBatch** 和 **preferredBatchSizeInKilobytes** 。 如需詳細資訊，請參閱 [EventGrid eventSubscriptions 範本參考](https://docs.microsoft.com/azure/templates/microsoft.eventgrid/eventsubscriptions)。

### <a name="azure-cli"></a>Azure CLI
您可以使用 [az eventgrid event-訂](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_create&preserve-view=true) 用帳戶建立或 [az eventgrid 事件訂](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_update&preserve-view=true) 用帳戶更新命令，以使用下列參數來設定批次相關設定： `--max-events-per-batch` 或 `--preferred-batch-size-in-kilobytes` 。

### <a name="azure-powershell"></a>Azure PowerShell
您可以使用 [AzEventGridSubscription](https://docs.microsoft.com/powershell/module/az.eventgrid/new-azeventgridsubscription) 或 [AzEventGridSubscription](https://docs.microsoft.com/powershell/module/az.eventgrid/update-azeventgridsubscription) Cmdlet，利用下列參數來設定批次相關設定： `-MaxEventsPerBatch` 或 `-PreferredBatchSizeInKiloBytes` 。

## <a name="next-steps"></a>後續步驟
如需支援的事件處理常式清單，請參閱[事件處理常式](event-handlers.md)一文。 
