---
title: 以 Webhook 作為「Azure 事件方格」事件的事件處理常式
description: 說明如何使用 Webhook 作為「Azure 事件方格」事件的事件處理常式。 Azure 自動化 Runbook 和邏輯應用程式均可透過 Webhook 作為事件處理常式支援。
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 3dc75b15c55c82b6164e77f0ac93a87bced94a64
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86105603"
---
# <a name="webhooks-automation-runbooks-logic-apps-as-event-handlers-for-azure-event-grid-events"></a>以 Webhook、自動化 Runbook、Logic Apps 作為 Azure 事件方格事件的事件處理常式
事件處理常式是傳送事件的位置。 處理常式會採取一些進一步的動作來處理事件。 許多 Azure 服務都已自動設定為可處理事件。 您也可以使用任何 WebHook 來處理事件。 WebHook 不需要裝載於 Azure 也能處理處理事件。 事件方格僅支援 HTTPS WebHook 端點。

> [!NOTE]
> Azure 自動化 Runbook 和邏輯應用程式均可透過 Webhook 作為事件處理常式支援。 

## <a name="webhooks"></a>Webhook
如需使用 webhook 作為事件處理常式的概觀和範例，請參閱下列文章。 

|Title  |描述  |
|---------|---------|
| 快速入門：使用 [Azure CLI](custom-event-quickstart.md)、[PowerShell](custom-event-quickstart-powershell.md) 及[入口網站](custom-event-quickstart-portal.md)建立和路由傳送自訂事件。 | 示範如何將自訂事件傳送至 WebHook。 |
| 快速入門：使用 [Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)、[PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) 及[入口網站](blob-event-quickstart-portal.md)將 Blob 儲存體事件路由至自訂的 Web 端點。 | 示範如何將 Blob 儲存體事件傳送至 WebHook。 |
| [快速入門：傳送容器登錄事件](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | 示範如何使用 Azure CLI 傳送 Container Registry 事件。 |
| [概觀：接收 HTTP 端點的事件](receive-events.md) | 說明如何驗證 HTTP 端點以從事件訂閱接收事件，然後接收和還原序列化事件。 |


## <a name="azure-automation"></a>Azure 自動化
您可以使用 Azure 自動化 Runbook 來處理事件。 Webhook 可支援使用自動化 Runbook 來處理事件。 您會為 Runbook 建立 Webhook，然後使用 Webhook 處理常式。 如需範例，請參閱下列教學課程： 

|Title  |描述  |
|---------|---------|
|[教學課程：Azure 自動化與事件方格和 Microsoft Teams](ensure-tags-exists-on-new-virtual-machines.md) |建立會傳送事件的虛擬機器。 事件會觸發標記虛擬機器的自動化 Runbook，以及觸發傳送給 Microsoft Teams 通道的訊息。 |


## <a name="logic-apps"></a>Logic Apps
使用 **Logic Apps** 來實作商務流程，以處理事件方格事件。 在此案例中，您不會明確建立 Webhook。 當您設定邏輯應用程式來處理事件方格中的事件時，系統會自動為您建立 Webhook。 請參閱下列教學課程以取得範例： 

|Title  |描述  |
|---------|---------|
| [教學課程：使用 Azure Event Grid 和 Logic Apps 監視虛擬機器變更](monitor-virtual-machine-changes-event-grid-logic-app.md) | 邏輯應用程式會監視虛擬機器的變更，並傳送有關這些變更的電子郵件。 |
| [教學課程：使用 Logic Apps 來傳送 Azure IoT 中樞事件的相關電子郵件通知](publish-iot-hub-events-to-logic-apps.md) | 每當有裝置新增至您的 IoT 中樞時，邏輯應用程式就會傳送電子郵件通知。 |
| [教學課程：使用 Azure Functions 和 Azure Logic Apps 來回應透過 Azure 事件方格所收到的 Azure 服務匯流排事件](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | 事件方格會從服務匯流排主題傳送訊息至函式應用程式和邏輯應用程式。 |

## <a name="rest-example-for-put"></a>REST 範例 (用於 PUT)

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "WebHook",
            "properties": 
            {
                "endpointUrl": "<WEB HOOK URL>",
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
