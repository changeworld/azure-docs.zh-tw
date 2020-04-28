---
title: 了解 Azure IoT 中樞自訂端點 | Microsoft Docs
description: 開發人員指南：使用路由查詢，將裝置到雲端訊息路由傳送到自訂端點。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/09/2018
ms.openlocfilehash: e5e92c40cef15e99431dc9652820c71e87935f67
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "61244339"
---
# <a name="use-message-routes-and-custom-endpoints-for-device-to-cloud-messages"></a>針對裝置對雲端訊息使用訊息路由和自訂端點

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

IoT 中樞[訊息路由](iot-hub-devguide-routing-query-syntax.md)讓使用者能夠將裝置到雲端的訊息路由傳送到服務對應端點。 路由也會提供查詢功能，在將資料路由傳送到端點前先篩選資料。 您設定的每個路由查詢都具有下列屬性：

| 屬性      | 描述 |
| ------------- | ----------- |
| **名稱**      | 可識別查詢的唯一名稱。 |
| **來源**    | 要據以處理的資料串流來源。 例如裝置遙測。 |
| **狀況** | 適用於路由查詢的查詢運算式，會針對訊息應用程式屬性、系統屬性、訊息內文、裝置對應項標記和裝置對應項屬性來執行，以判斷它是否符合端點。 如需如何建構查詢的詳細資訊，請參閱[訊息路由查詢語法](iot-hub-devguide-routing-query-syntax.md) |
| **端點**  | IoT 中樞傳送符合查詢之訊息的目的地端點名稱。 我們建議選擇與您 IoT 中樞位於相同區域的端點。 |

單一訊息可能符合多個路由查詢的條件，在這種情況下，IoT 中樞會將訊息傳遞至與每個相符查詢相關聯的端點。 IoT 中樞也會自動刪除重複的訊息傳遞，因此，如果訊息符合多個具有相同目的地的查詢，該訊息就只會寫入到該目的地一次。

## <a name="endpoints-and-routing"></a>端點和路由

IoT 中樞具有預設[內建端點](iot-hub-devguide-messages-read-builtin.md)。 您可以將訂用帳戶中的其他服務連結到中樞，來建立要路由傳送訊息的目標自訂端點。 IoT 中樞目前支援 Azure 儲存體容器、事件中樞、服務匯流排佇列，及服務匯流排主題作為自訂端點。

當您使用路由與自訂端點時，不符合任何查詢的訊息就只會傳遞到內建端點。 若要將訊息傳遞至內建端點以及自訂端點，請新增會將訊息傳送至內建**事件**端點的路由。

> [!NOTE]
> * IoT 中樞僅支援將資料寫入 Azure 儲存體容器作為 blob。
> * 不支援將啟用 [工作階段]**** 或 [重複偵測]**** 的服務匯流排佇列和主題作為自訂端點。

如需在 IoT 中樞建立自訂端點的詳細資訊，請參閱 [IoT 中樞端點](iot-hub-devguide-endpoints.md)。

如需從自訂端點讀取的詳細資訊，請參閱：

* 從 [Azure 儲存體容器](../storage/blobs/storage-blobs-introduction.md)讀取。

* 從[事件中樞](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)讀取。

* 從[服務匯流排佇列](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)讀取。

* 從[服務匯流排主題](../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md)讀取。

## <a name="next-steps"></a>後續步驟

* 如需 IoT 中樞端點的詳細資訊，請參閱 [IoT 中樞端點](iot-hub-devguide-endpoints.md)。

* 如需您用來定義路由查詢之查詢語言的詳細資訊，請參閱[訊息路由查詢語法](iot-hub-devguide-routing-query-syntax.md)。

* [使用路由處理 IoT 中樞的裝置到雲端訊息](tutorial-routing.md)教學課程示範如何使用路由查詢與自訂端點。