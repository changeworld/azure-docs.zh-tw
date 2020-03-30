---
title: 路由事件和消息 - Azure 數位孿生 |微軟文檔
description: 使用 Azure Digital Twins 將事件和訊息路由至服務端點的概觀
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/03/2020
ms.openlocfilehash: 65b760eaf28d907fab3654ed92f960be7556b0d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75862351"
---
# <a name="routing-iot-events-and-messages"></a>路由 IoT 事件和消息

物聯網解決方案通常將多個功能強大的服務（包括存儲、分析等）結合起來。 本文說明如何將 Azure Digital Twins 應用程式連線到 Azure 分析、AI 和儲存體服務，讓這些應用程式取得更深入的見解和功能。

## <a name="route-types"></a>路由類型  

Azure 數位孿生提供了兩種將 IoT 事件與其他 Azure 服務或商務應用程式連接的方法：

* **路由傳送 Azure Digital Twins 事件**：當空間圖表中的物件發生變更、收到遙測資料，或使用者定義的函式根據預先定義的條件建立通知時，都可能觸發 Azure Digital Twins 事件。 使用者可以將這些事件傳送至 [Azure 事件中樞](https://azure.microsoft.com/services/event-hubs/)、[Azure 服務匯流排主題](https://azure.microsoft.com/services/service-bus/)或 [Azure 事件方格](https://azure.microsoft.com/services/event-grid/)，以執行進一步的處理。

* **路由裝置遙測**：除了路由事件之外，Azure Digital Twins 也可以將未經處理的裝置遙測訊息路由至事件中樞，以取得深入的見解和分析。 Digital Twins 並未處理這些類型的訊息。 而這些訊息只會轉送至事件中樞。

使用者可以指定一個或多個輸出端點來傳送事件或轉送訊息。 事件和訊息會根據這些預先定義的路由喜好設定來傳送至端點。 換句話說，使用者可以指定特定端點來接收圖表作業事件，並指定另一個端點來接收裝置遙測事件，依此類推。

[![Azure Digital Twins 事件路由](media/concepts/digital-twins-events-routing.png)](media/concepts/digital-twins-events-routing.png#lightbox)

路由傳送至事件中樞可維持遙測訊息的傳送順序。 所以它們會以原先接收的相同順序抵達端點。 

事件方格和服務匯流排則不保證端點收到事件的順序會與事件發生順序相同。 不過，事件結構描述包含可用來識別事件抵達端點後之順序的時間戳記。

## <a name="route-implementation"></a>路由實作

Azure Digital Twins 服務目前支援下列 **EndpointTypes**：

* **EventHub** 是事件中樞連接字串端點。
* **服務匯流排**是服務匯流排連接字串終結點。
* **事件網格**是事件網格連接字串終結點。

Azure Digital Twins 目前支援將下列 **EventTypes** 傳送至所選的端點：

* **設備消息**是從使用者設備發送並由系統轉發的遙測消息。
* **TopologyOperation** 是變更圖表或圖表中繼資料的作業。 例如，新增或刪除實體 (例如空間)。
* **SpaceChange** 是裝置遙測訊息所造成的空間計算值變更。
* **SensorChange** 是裝置遙測訊息所造成的感應器計算值變更。
* **UdfCustom** 是來自使用者定義函式的自訂通知。

> [!IMPORTANT]  
> 並非所有 **EndpointTypes** 都支援所有 **EventTypes**。
> 查看下表中允許每個**終結點類型**的事件**類型**。

|             | DeviceMessages | TopologyOperation | SpaceChange | SensorChange | UdfCustom |
| ----------- | -------------- | ----------------- | ----------- | ------------ | --------- |
| EventHub|     X          |         X         |     X       |      X       |   X       |
| ServiceBus|              |         X         |     X       |      X       |   X       |
| EventGrid|               |         X         |     X       |      X       |   X       |

>[!NOTE]  
>有關如何創建終結點和事件架構示例的詳細資訊，請閱讀[Egress 和終結點](how-to-egress-endpoints.md)。

## <a name="next-steps"></a>後續步驟

- 要瞭解 Azure 數位孿生預覽限制，請閱讀[公共預覽服務限制](concepts-service-limits.md)。

- 要嘗試 Azure 數位孿生示例，請閱讀[快速入門以查找可用房間](quickstart-view-occupancy-dotnet.md)。