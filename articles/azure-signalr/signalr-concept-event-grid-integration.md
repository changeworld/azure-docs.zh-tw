---
title: 回應 Azure 信號R 服務事件
description: 使用 Azure 事件網格訂閱 Azure SignalR 服務事件。 這些事件可以觸發其他下游服務。
services: azure-signalr,event-grid
author: chenyl
ms.author: chenyl
ms.reviewer: zhshang
ms.date: 11/13/2019
ms.topic: conceptual
ms.service: signalr
ms.openlocfilehash: a8e25907b40b910f2b91884d355b6ac85eeaa250
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74158202"
---
# <a name="reacting-to-azure-signalr-service-events"></a>回應 Azure SignalR Service 事件

Azure SignalR 服務事件允許應用程式使用現代無伺服器體系結構對連接或斷開連接的用戶端連接做出反應。 它不需要複雜的程式碼或昂貴且無效率的輪詢服務來執行此動作。  而是改為透過 [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) 將事件推送給訂閱者，例如 [Azure Functions](https://azure.microsoft.com/services/functions/)、[Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)，甚至推送到您自己的自訂 http 接聽程式，且只需要支付有使用的項目的費用。

Azure SignalR 服務事件可靠地發送到事件網格服務，該服務通過豐富的重試策略和死信傳遞向應用程式提供可靠的傳遞服務。 要瞭解更多資訊，請參閱[事件網格消息傳遞和重試](https://docs.microsoft.com/azure/event-grid/delivery-and-retry)。

![Event Grid 模型](https://docs.microsoft.com/azure/event-grid/media/overview/functional-model.png)

## <a name="serverless-state"></a>無伺服器狀態
Azure SignalR 服務事件僅在用戶端連接處於無伺服器狀態時處於活動狀態。 通常，如果用戶端不路由到中心伺服器，它將進入無伺服器狀態。 傳統模式僅在用戶端連接的集線器沒有集線器伺服器時才起作用。 但是，建議使用無伺服器模式以避免某些問題。 要瞭解有關服務模式的更多詳細資訊，請參閱[如何選擇服務模式](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose)。

## <a name="available-azure-signalr-service-events"></a>可用的 Azure 信號R 服務事件
Event Grid 使用[事件訂閱](../event-grid/concepts.md#event-subscriptions)將事件訊息路由至訂閱者。 Azure SignalR 服務事件訂閱支援兩種類型的事件：  

|活動名稱|描述|
|----------|-----------|
|`Microsoft.SignalRService.ClientConnectionConnected`|連接用戶端連接時引發。|
|`Microsoft.SignalRService.ClientConnectionDisconnected`|當用戶端連接斷開連接時引發。|

## <a name="event-schema"></a>結構描述
Azure SignalR 服務事件包含回應資料更改所需的所有資訊。 您可以使用事件種類屬性以"Microsoft.SignalR 服務"開頭標識 Azure SignalR 服務事件。 有關事件網格事件屬性使用方式的其他資訊記錄在[事件網格事件架構](../event-grid/event-schema.md)中。  

下面是用戶端連接事件的示例：
```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/signalr-rg/providers/Microsoft.SignalRService/SignalR/signalr-resource",
  "subject": "/hub/chat",
  "eventType": "Microsoft.SignalRService.ClientConnectionConnected",
  "eventTime": "2019-06-10T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "timestamp": "2019-06-10T18:41:00.9584103Z",
    "hubName": "chat",
    "connectionId": "crH0uxVSvP61p5wkFY1x1A",
    "userId": "user-eymwyo23"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

有關詳細資訊，請參閱[SignalR 服務事件架構](../event-grid/event-schema-azure-signalr.md)。

## <a name="next-steps"></a>後續步驟

瞭解有關事件網格的更多資訊，並嘗試 Azure SignalR 服務事件：

> [!div class="nextstepaction"]
> [嘗試與 Azure SignalR 服務](./signalr-howto-event-grid-integration.md)
> 的示例事件網格集成[有關事件網格](../event-grid/overview.md)
