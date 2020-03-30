---
title: Azure 事件方格事件處理常式
description: 描述 Azure 事件網格受支援的事件處理常式。 Azure 自動化、功能、事件中心、混合連接、邏輯應用、服務匯流排、佇列存儲、Webhook。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 7ea00d663264e902c1818f7a4684e90eccd97b28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265047"
---
# <a name="event-handlers-in-azure-event-grid"></a>Azure 事件方格中的事件處理常式

事件處理常式是傳送事件的位置。 處理常式會採取一些進一步的動作來處理事件。 許多 Azure 服務都已自動設定為可處理事件。 您也可以使用任何 WebHook 來處理事件。 WebHook 不需要裝載於 Azure 也能處理處理事件。 事件方格僅支援 HTTPS WebHook 端點。

本文會提供每個事件處理常式的內容連結。

## <a name="azure-automation"></a>Azure 自動化

使用 Azure 自動化，透過自動化的 Runbook 來處理事件。

|Title  |描述  |
|---------|---------|
|[教學課程：Azure 自動化與事件方格和 Microsoft Teams](ensure-tags-exists-on-new-virtual-machines.md) |建立會傳送事件的虛擬機器。 事件會觸發標記虛擬機器的自動化 Runbook，以及觸發傳送給 Microsoft Teams 通道的訊息。 |

## <a name="azure-functions"></a>Azure Functions

使用 Azure Functions 對事件進行無伺服器回應。

使用 Azure Functions 作為處理常式時，請使用 Event Grid 觸發程序而不是泛型 HTTP 觸發程序。 Event Grid 會自動驗證 Event Grid 函式的觸發程序。 若要使用 HTTP 觸發程序，您必須實作[驗證回應](security-authentication.md#webhook-event-delivery)。

|Title  |描述  |
|---------|---------|
| [快速入門：使用函數處理事件](custom-event-to-function.md) | 將自訂事件發送到要處理的函數。 |
| [Azure Functions 的事件方格觸發程序](../azure-functions/functions-bindings-event-grid.md) | 在 Functions 中使用事件方格觸發程序的概觀。 |
| [教學課程：使用事件方格自動調整已上傳映像的大小](resize-images-on-storage-blob-upload-event.md) | 使用者透過 Web 應用程式將映像上傳至儲存體帳戶。 建立儲存體 Blob 時，事件方格會傳送一個事件給函式應用程式，讓其調整上傳映像的大小。 |
| [教學課程：將巨量資料串流處理至資料倉儲](event-grid-event-hubs-integration.md) | 當事件中樞建立「擷取」檔案時，事件方格會傳送一個事件至函式應用程式。 應用程式會對「擷取」檔案進行擷取，並將資料遷移到資料倉儲。 |
| [教學課程：Azure 服務匯流排與 Azure 事件方格的整合範例](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | 事件方格會從服務匯流排主題傳送訊息至函式應用程式和邏輯應用程式。 |

## <a name="event-hubs"></a>事件中樞

解決方案收到事件的速度比處理事件的速度更快時，請使用事件中樞。 應用程式會按照自己的排程，透過事件中樞處理事件。 您可以調整事件流程來處理傳入事件。

事件中樞可以做為事件來源或事件處理常式。 下列文章會示範如何使用事件中樞做為處理常式。

|Title  |描述  |
|---------|---------|
| [快速入門：使用 Azure CLI 和事件方格將自訂事件路由至 Azure 事件中樞](custom-event-to-eventhub.md) | 將自訂事件傳送至事件中樞交給應用程式處理。 |
| [Resource Manager 範本：自訂主題和事件中樞端點](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| 建立自訂主題訂用帳戶的 Resource Manager 範本。 它會將事件傳送到 Azure 事件中樞。 |

如需事件中樞做為來源的範例，請參閱[事件中樞來源](event-sources.md#event-hubs)。

## <a name="hybrid-connections"></a>混合式連線

使用 Azure 轉送的混合式連線將事件傳送至應用程式，這些應用程式都位於企業網路中，而且沒有可公開存取的端點。

|Title  |描述  |
|---------|---------|
| [教學課程：將事件傳送至混合式連線](custom-event-to-hybrid-connection.md) | 將自訂事件傳送至現有的混合式連線，以透過接聽應用程式進行處理。 |

## <a name="logic-apps"></a>Logic Apps

使用 Logic Apps 來自動化回應事件的商務程序。

|Title  |描述  |
|---------|---------|
| [教程：使用 Azure 事件網格和邏輯應用監視虛擬機器更改](monitor-virtual-machine-changes-event-grid-logic-app.md) | 邏輯應用程式會監視虛擬機器的變更，並傳送有關這些變更的電子郵件。 |
| [教學課程：使用 Logic Apps 來傳送 Azure IoT 中樞事件的相關電子郵件通知](publish-iot-hub-events-to-logic-apps.md) | 每當有裝置新增至您的 IoT 中樞時，邏輯應用程式就會傳送電子郵件通知。 |
| [教學課程：Azure 服務匯流排與 Azure 事件方格的整合範例](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | 事件方格會從服務匯流排主題傳送訊息至函式應用程式和邏輯應用程式。 |

## <a name="service-bus"></a>服務匯流排

### <a name="service-bus-queues"></a>服務匯流排佇列

可以將事件網格中的事件直接路由到服務匯流排佇列，以用於企業應用程式中的緩衝或命令&控制方案。

在 Azure 門戶中，在創建事件訂閱時，選擇"服務匯流排佇列"作為終結點類型，然後按一下"選擇終結點"以選擇服務匯流排佇列。

#### <a name="using-cli-to-add-a-service-bus-queue-handler"></a>使用 CLI 添加服務匯流排佇列處理常式

對於 Azure CLI，以下示例訂閱事件網格主題並將其連接到服務匯流排佇列：

```azurecli-interactive
# If you haven't already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebusqueue \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/queues/queue1
```

### <a name="service-bus-topics"></a>服務匯流排主題

可以將事件網格中的事件直接路由到服務匯流排主題，以便使用服務匯流排主題處理 Azure 系統事件，或者用於命令&控制訊息傳遞方案。

在 Azure 門戶中，在創建事件訂閱時，選擇"服務匯流排主題"作為終結點類型，然後按一下"選擇和終結點"以選擇服務匯流排主題。

#### <a name="using-cli-to-add-a-service-bus-topic-handler"></a>使用 CLI 添加服務匯流排主題處理常式

對於 Azure CLI，以下示例訂閱事件網格主題並將其連接到服務匯流排佇列：

```azurecli-interactive
# If you haven't already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebustopic \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/topics/topic1
```

## <a name="queue-storage"></a>佇列儲存體

使用佇列儲存體來接收需要提取的事件。 若執行中的流程太過冗長導致回應時間過久，您可以使用佇列儲存體。 藉由傳送事件至佇列儲存體，應用程式即可按照自己的排程提取並處理事件。

|Title  |描述  |
|---------|---------|
| [快速入門：使用 Azure CLI 和事件方格將自訂事件路由至 Azure 佇列儲存體](custom-event-to-queue-storage.md) | 描述如何將自訂事件傳送至佇列儲存體。 |

## <a name="webhooks"></a>Webhook

針對回應事件的自訂端點使用 Webhook。

|Title  |描述  |
|---------|---------|
| 快速入門：使用 [Azure CLI](custom-event-quickstart.md)、[PowerShell](custom-event-quickstart-powershell.md) 及[入口網站](custom-event-quickstart-portal.md)建立和路由傳送自訂事件。 | 示範如何將自訂事件傳送至 WebHook。 |
| 快速入門：使用 [Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)、[PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) 及[入口網站](blob-event-quickstart-portal.md)將 Blob 儲存體事件路由至自訂的 Web 端點。 | 示範如何將 Blob 儲存體事件傳送至 WebHook。 |
| [快速入門：傳送容器登錄事件](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | 示範如何使用 Azure CLI 傳送 Container Registry 事件。 |
| [概觀：接收 HTTP 端點的事件](receive-events.md) | 說明如何驗證 HTTP 端點以從事件訂閱接收事件，然後接收和還原序列化事件。 |

## <a name="next-steps"></a>後續步驟

* 有關事件網格的簡介，請參閱[有關事件網格](overview.md)。
* 若要快速地開始使用 Event Grid，請參閱[使用 Azure Event Grid 建立和路由傳送自訂事件](custom-event-quickstart.md)。
