---
title: 回應 Azure SignalR Service 事件
description: 使用 Azure 事件方格來訂閱 Azure SignalR Service 事件。 這些事件可觸發其他下游服務。
services: azure-signalr,event-grid
author: chenyl
ms.author: chenyl
ms.reviewer: zhshang
ms.date: 11/13/2019
ms.topic: conceptual
ms.service: signalr
ms.openlocfilehash: 77c8887ac19c6ce4c7d83734bdd2b44d9213914d
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2020
ms.locfileid: "92151106"
---
# <a name="reacting-to-azure-signalr-service-events"></a>回應 Azure SignalR Service 事件

Azure SignalR Service 事件可讓應用程式使用新式無伺服器架構來回應連線或中斷連線的用戶端連接。 它不需要複雜的程式碼或昂貴且無效率的輪詢服務來執行此動作。  相反地，事件會透過 [Azure 事件方格](https://azure.microsoft.com/services/event-grid/) 推送至「訂閱者」，例如 [Azure Functions](https://azure.microsoft.com/services/functions/)、 [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)，甚至是您自己的自訂 HTTP 接聽程式。 使用 Azure SignalR 時，您只需支付所取用的費用。

Azure SignalR Service 事件會可靠地傳送至事件方格服務，以透過 rich 重試原則和寄不出的信件傳遞，提供可靠的傳遞服務給您的應用程式。 若要深入瞭解，請參閱 [事件方格訊息傳遞和重試](../event-grid/delivery-and-retry.md)。

![Event Grid 模型](/azure/event-grid/media/overview/functional-model.png)

## <a name="serverless-state"></a>無伺服器狀態
只有當用戶端連線處於無伺服器狀態時，才會使用 Azure SignalR Service 事件。 如果用戶端未路由傳送至中樞伺服器，則會進入無伺服器狀態。 只有當用戶端連線所連接的中樞沒有中樞伺服器時，傳統模式才適用。 建議您最好採用無伺服器模式。 若要深入瞭解服務模式的詳細資訊，請參閱 [如何選擇服務模式](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose)。

## <a name="available-azure-signalr-service-events"></a>可用的 Azure SignalR Service 事件
Event Grid 使用[事件訂閱](../event-grid/concepts.md#event-subscriptions)將事件訊息路由至訂閱者。 Azure SignalR Service 事件訂閱支援兩種類型的事件：  

|活動名稱|描述|
|----------|-----------|
|`Microsoft.SignalRService.ClientConnectionConnected`|連接用戶端連接時引發。|
|`Microsoft.SignalRService.ClientConnectionDisconnected`|當用戶端連接中斷連接時引發。|

## <a name="event-schema"></a>結構描述
Azure SignalR Service 事件包含回應資料變更所需的所有資訊。 您可以使用 [事件識別碼] 屬性開頭為 "Microsoft.signalrservice" 來識別 Azure SignalR Service 事件。 事件方格事件 [架構](../event-grid/event-schema.md)中記載了事件方格事件屬性使用的其他相關資訊。  

以下是用戶端連接連接事件的範例：
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

如需詳細資訊，請參閱 [SignalR Service events 架構](../event-grid/event-schema-azure-signalr.md)。

## <a name="next-steps"></a>後續步驟

深入瞭解事件方格，並嘗試 Azure SignalR Service 的事件：

> [!div class="nextstepaction"]
> [嘗試 Azure SignalR Service](./signalr-howto-event-grid-integration.md) 
>  的事件方格整合範例[關於事件方格](../event-grid/overview.md)