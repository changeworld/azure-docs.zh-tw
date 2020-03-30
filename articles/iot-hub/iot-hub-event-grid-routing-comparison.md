---
title: 比較事件格線、IoT 中樞的路由 | Microsoft Docs
description: 「IoT 中樞」提供自己的訊息路由服務，但也與「事件格線」整合來發佈事件。 比較兩個功能。
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.openlocfilehash: 877467b65d346c871dd93f4b3f96b2c1664fa4b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73906798"
---
# <a name="compare-message-routing-and-event-grid-for-iot-hub"></a>比較 IoT 中樞的訊息路由和事件格線

Azure IoT 中心提供從連接的設備資料流資料並將該資料整合到商務應用程式中的功能。 「IoT 中樞」提供兩種方法來將 IoT 事件整合至其他 Azure 服務或商務應用程式。 本文將探討提供此能力的兩個功能，以便您選擇最適合您案例的選項。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

**[IoT 中心消息路由](iot-hub-devguide-messages-d2c.md)**：此 IoT 中心功能使使用者能夠將設備到雲的消息路由到服務終結點（如 Azure 存儲容器、事件中心、服務匯流排佇列和服務匯流排主題）。 路由也會提供查詢功能，在將資料路由傳送到端點前先篩選資料。 除了裝置遙測資料外，您也可以傳送可用來觸發動作的[非遙測事件](iot-hub-devguide-messages-d2c.md#non-telemetry-events)。 

**IoT 中樞與事件格線的整合**：「Azure 事件格線」是一個使用「發佈-訂閱」模型的完全受控事件路由服務。 「IoT 中樞」與「事件格線」合作即可以近乎即時的方式，將 [IoT 中樞事件整合至 Azure 和非 Azure 服務](iot-hub-event-grid.md)。 IoT 中心同時發佈[設備事件](iot-hub-event-grid.md#event-types)和遙測事件。

## <a name="differences"></a>差異

雖然訊息路由和「事件格線」都可啟用警示設定功能，但兩者之間有一些重要的差異。 如需詳細資訊，請參閱下表︰

| 功能 | IoT 中樞訊息路由 | IoT 中樞與事件格線的整合 |
| ------- | --------------- | ---------- |
| **設備消息和事件** | 是的，消息路由可用於遙測資料、報告設備孿生更改、設備生命週期事件和數位孿生變更事件[（IoT 隨插即用公共預覽](../iot-pnp/overview-iot-plug-and-play.md)的一部分）。 | 是，事件網格可用於遙測資料，但也可以報告何時創建、刪除、連接和斷開與 IoT 中心的連接設備 |
| **訂購** | 是，會維持事件的排序。  | 否，不保證維持事件的順序。 | 
| **濾波** | 對訊息應用程式屬性、訊息系統屬性、訊息內文、裝置對應項標籤及裝置對應項屬性所進行的豐富篩選功能。 篩選不應用於數位孿生變更事件。 如需範例，請參閱[訊息路由查詢語法](iot-hub-devguide-routing-query-syntax.md)。 | 根據每個事件中的事件種類、主題類型和屬性進行篩選。 有關示例，請參閱[瞭解事件網格訂閱中的篩選事件](../event-grid/event-filtering.md)。 訂閱遙測事件時，可以在資料上應用其他篩選器來篩選 IoT 中心中的消息屬性、郵件內文和設備孿生，然後再發佈到事件網格。 [瞭解如何篩選事件](../iot-hub/iot-hub-event-grid.md#filter-events)。 |
| **端點** | <ul><li>事件中樞</li> <li>Azure Blob 儲存體</li> <li>服務匯流排佇列</li> <li>服務匯流排主題</li></ul><br>付費的「IoT 中樞」SKU (S1、S2 及 S3) 僅限有 10 個自訂端點。 可以為每個「IoT 中樞」建立 100 個路由。 | <ul><li>Azure Functions</li> <li>Azure 自動化</li> <li>事件中樞</li> <li>Logic Apps</li> <li>儲存體 Blob</li> <li>自訂主題</li> <li>佇列儲存體</li> <li>Microsoft Flow</li> <li>透過 WebHook 提供的協力廠商服務</li></ul><br>每個 IoT 中心支援 500 個終結點。 如需最新的端點清單，請參閱[事件格線處理常式](../event-grid/overview.md#event-handlers)。 |
| **成本** | 針對訊息路由並不另外收費。 僅針對將遙測資料輸入「IoT 中樞」收費。 例如，如果您將一則訊息路由傳送至三個不同的端點，將只需支付一則訊息的費用。 | 沒有任何來自「IoT 中樞」的收費。 事件網格每月免費提供前 100，000 個操作，之後每百萬個操作 0.60 美元。 |

## <a name="similarities"></a>相似 之 處

「IoT 中樞訊息路由」與「事件格線」也有相似之處，下表詳細說明一些相似之處：

| 功能 | IoT 中樞訊息路由 | IoT 中樞與事件格線的整合 |
| ------- | --------------- | ---------- |
| **最大消息大小** | 256 KB (裝置到雲端) | 256 KB (裝置到雲端) |
| **可靠性** | 高：針對每個路由，會將每則訊息至少傳送到端點一次。 所有未在 1 小時內傳遞的訊息都會變成過期訊息。 | 高：針對每個訂用帳戶，會將每則訊息至少傳送到 Webhook 一次。 所有未在 24 小時內傳遞的事件都會變成過期事件。 | 
| **可 伸縮 性** | 高：已經過最佳化，可支援數百萬部同時連線的裝置傳送數十億則訊息。 | 高：能夠每個區域每秒路由傳送 10,000,000 個事件。 |
| **延遲** | 低：近乎即時。 | 低：近乎即時。 |
| **傳送至多個端點** | 是，將單一訊息傳送至多個端點。 | 是，將單一訊息傳送至多個端點。  
| **安全性** | 「IoT 中樞」提供個別裝置身分識別與可撤銷的存取控制。 如需詳細資訊，請參閱 [IoT 中樞存取控制](iot-hub-devguide-security.md)。 | 「事件格線」提供三個時間點的驗證：事件訂閱、事件發佈，以及 Webhook 事件傳遞。 如需詳細資訊，請參閱 [Event Grid 安全性和驗證](../event-grid/security-authentication.md)。 |

## <a name="how-to-choose"></a>如何選擇

「IoT 中樞訊息路由」和「IoT 中樞與事件格線的整合」執行不同的動作來達到類似的結果。 兩者都會從您的「IoT 中樞」解決方案取得資訊，然後將該資訊傳遞下去，以便其他服務能夠做出反應。 那麼，要如何決定要使用哪一個？ 請考慮以下問題，以説明指導您的決策： 

* **您要傳送給端點的資料是哪一種？**

   當您必須將遙測資料傳送給其他服務時，請使用「IoT 中樞訊息路由」。 訊息路由也能夠查詢訊息應用程式和系統屬性、訊息內文、裝置對應項標籤及裝置對應項屬性。

   「IoT 中樞與事件格線的整合」可處理「IoT 中樞」服務中發生的事件。 這些 IoT 中心事件包括遙測資料、設備創建、刪除、連接和斷開連接。 訂閱遙測事件時，可以在資料上應用其他篩選器來篩選 IoT 中心中的消息屬性、郵件內文和設備孿生，然後再發佈到事件網格。 [瞭解如何篩選事件](../iot-hub/iot-hub-event-grid.md#filter-events)。

* **哪些端點需要接收此資訊？**

   IoT 中心消息路由支援有限數量的唯一終結點和終結點類型，但您可以構建連接器以將資料和事件重新路由到其他終結點。 如需支援的端點完整清單，請參閱上一節中的表格。 

   IoT 中心與事件網格集成支援每個 IoT 中心 500 個終結點和多種終結點類型。 它本機與 Azure 函數、邏輯應用、存儲和服務匯流排佇列集成，還與 Webhook 一起擴展 Azure 服務生態系統之外的發送資料並擴展到協力廠商商務應用程式。

* **如果資料有抵達順序，是否有影響？**

   「IoT 中樞訊息路由」會維持訊息的傳送順序，因此這些訊息會依照相同的方式抵達。

   「事件格線」則不保證端點收到事件的順序會與事件發生順序相同。 對於消息的絕對順序顯著和/或消費者需要消息的可信唯一識別碼的情況，我們建議使用消息路由。 

## <a name="next-steps"></a>後續步驟

* 深入了解 [IoT 中樞訊息路由](iot-hub-devguide-messages-d2c.md)和 [IoT 中樞端點](iot-hub-devguide-endpoints.md)。
* 深入了解 [Azure Event Grid](../event-grid/overview.md)。
* 如需了解如何建立訊息路由，請參閱[使用路由處理 IoT 中樞的裝置到雲端訊息](../iot-hub/tutorial-routing.md)教學課程。
* 嘗試事件網格集成通過使用[邏輯應用發送有關 Azure IoT 中心事件的電子郵件通知](../event-grid/publish-iot-hub-events-to-logic-apps.md)。
