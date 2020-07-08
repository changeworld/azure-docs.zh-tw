---
title: 以 Azure 函式作為 Azure 事件方格事件的事件處理常式
description: 說明如何使用 Azure 函式作為「事件方格」事件的事件處理常式。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: 0152a9fc11562744f83ab9d20466a3dcc8e2e6e0
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.contentlocale: zh-TW
ms.lasthandoff: 05/22/2020
ms.locfileid: "83800420"
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
                "maxEventsPerBatch": 1,
                "preferredBatchSizeInKilobytes": 64
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="next-steps"></a>後續步驟
如需支援的事件處理常式清單，請參閱[事件處理常式](event-handlers.md)一文。 
