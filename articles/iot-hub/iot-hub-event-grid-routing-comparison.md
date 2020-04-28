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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "73906798"
---
# <a name="compare-message-routing-and-event-grid-for-iot-hub"></a>比較 IoT 中樞的訊息路由和事件格線

Azure IoT 中樞可讓您從已連線的裝置串流資料，並將該資料整合到您的商務應用程式中。 「IoT 中樞」提供兩種方法來將 IoT 事件整合至其他 Azure 服務或商務應用程式。 本文將探討提供此能力的兩個功能，以便您選擇最適合您案例的選項。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

**[IoT 中樞訊息路由](iot-hub-devguide-messages-d2c.md)**：此 IoT 中樞功能可讓使用者將裝置到雲端訊息路由傳送至服務端點，例如 Azure 儲存體容器、事件中樞、服務匯流排佇列和服務匯流排主題。 路由也會提供查詢功能，在將資料路由傳送到端點前先篩選資料。 除了裝置遙測資料外，您也可以傳送可用來觸發動作的[非遙測事件](iot-hub-devguide-messages-d2c.md#non-telemetry-events)。 

**IoT 中樞與事件格線的整合**：「Azure 事件格線」是一個使用「發佈-訂閱」模型的完全受控事件路由服務。 「IoT 中樞」與「事件格線」合作即可以近乎即時的方式，將 [IoT 中樞事件整合至 Azure 和非 Azure 服務](iot-hub-event-grid.md)。 IoT 中樞會同時發行[裝置事件](iot-hub-event-grid.md#event-types)和遙測事件。

## <a name="differences"></a>差異

雖然訊息路由和「事件格線」都可啟用警示設定功能，但兩者之間有一些重要的差異。 如需詳細資訊，請參閱下表︰

| 功能 | IoT 中樞訊息路由 | IoT 中樞與事件格線的整合 |
| ------- | --------------- | ---------- |
| **裝置訊息和事件** | 是，訊息路由可以用於遙測資料、報告裝置對應項變更、裝置生命週期事件，以及數位對應項變更事件（ [IoT 隨插即用公開預覽](../iot-pnp/overview-iot-plug-and-play.md)的一部分）。 | 是，事件方格可以用於遙測資料，但也可以在裝置建立、刪除、連線和中斷連接時報告 IoT 中樞 |
| **訂購** | 是，會維持事件的排序。  | 否，不保證維持事件的順序。 | 
| **濾除** | 對訊息應用程式屬性、訊息系統屬性、訊息內文、裝置對應項標籤及裝置對應項屬性所進行的豐富篩選功能。 不會將篩選套用至數位對應項變更事件。 如需範例，請參閱[訊息路由查詢語法](iot-hub-devguide-routing-query-syntax.md)。 | 根據事件種類、每個事件中的主體類型和屬性進行篩選。 如需範例，請參閱[瞭解事件方格訂用帳戶中的篩選事件](../event-grid/event-filtering.md)。 訂閱遙測事件時，您可以在資料上套用其他篩選，以便在發行至事件方格之前，先篩選 IoT 中樞中的訊息屬性、訊息內文和裝置對應項。 請參閱[如何篩選事件](../iot-hub/iot-hub-event-grid.md#filter-events)。 |
| **端點** | <ul><li>事件中樞</li> <li>Azure Blob 儲存體</li> <li>服務匯流排佇列</li> <li>服務匯流排主題</li></ul><br>付費的「IoT 中樞」SKU (S1、S2 及 S3) 僅限有 10 個自訂端點。 可以為每個「IoT 中樞」建立 100 個路由。 | <ul><li>Azure Functions</li> <li>Azure 自動化</li> <li>事件中樞</li> <li>Logic Apps</li> <li>儲存體 Blob</li> <li>自訂主題</li> <li>佇列儲存體</li> <li>Microsoft Flow</li> <li>透過 WebHook 提供的協力廠商服務</li></ul><br>每個 IoT 中樞支援500個端點。 如需最新的端點清單，請參閱[事件格線處理常式](../event-grid/overview.md#event-handlers)。 |
| **成本** | 針對訊息路由並不另外收費。 僅針對將遙測資料輸入「IoT 中樞」收費。 例如，如果您將一則訊息路由傳送至三個不同的端點，將只需支付一則訊息的費用。 | 沒有任何來自「IoT 中樞」的收費。 「事件方格」可免費提供每個月的前100000個作業，之後則為每百萬個作業 $0.60。 |

## <a name="similarities"></a>相似

「IoT 中樞訊息路由」與「事件格線」也有相似之處，下表詳細說明一些相似之處：

| 功能 | IoT 中樞訊息路由 | IoT 中樞與事件格線的整合 |
| ------- | --------------- | ---------- |
| **訊息大小上限** | 256 KB (裝置到雲端) | 256 KB (裝置到雲端) |
| **可靠性** | 高：針對每個路由，會將每則訊息至少傳送到端點一次。 所有未在 1 小時內傳遞的訊息都會變成過期訊息。 | 高：針對每個訂用帳戶，會將每則訊息至少傳送到 Webhook 一次。 所有未在 24 小時內傳遞的事件都會變成過期事件。 | 
| **延展性** | 高：已經過最佳化，可支援數百萬部同時連線的裝置傳送數十億則訊息。 | 高：能夠每個區域每秒路由傳送 10,000,000 個事件。 |
| **Latency** | 低：近乎即時。 | 低：近乎即時。 |
| **傳送至多個端點** | 是，將單一訊息傳送至多個端點。 | 是，將單一訊息傳送至多個端點。  
| **安全性** | 「IoT 中樞」提供個別裝置身分識別與可撤銷的存取控制。 如需詳細資訊，請參閱 [IoT 中樞存取控制](iot-hub-devguide-security.md)。 | 「事件格線」提供三個時間點的驗證：事件訂閱、事件發佈，以及 Webhook 事件傳遞。 如需詳細資訊，請參閱 [Event Grid 安全性和驗證](../event-grid/security-authentication.md)。 |

## <a name="how-to-choose"></a>如何選擇

「IoT 中樞訊息路由」和「IoT 中樞與事件格線的整合」執行不同的動作來達到類似的結果。 兩者都會從您的「IoT 中樞」解決方案取得資訊，然後將該資訊傳遞下去，以便其他服務能夠做出反應。 那麼，要如何決定要使用哪一個？ 請考慮下列問題來協助引導您進行決策： 

* **您要傳送給端點的資料是哪一種？**

   當您必須將遙測資料傳送給其他服務時，請使用「IoT 中樞訊息路由」。 訊息路由也能夠查詢訊息應用程式和系統屬性、訊息內文、裝置對應項標籤及裝置對應項屬性。

   「IoT 中樞與事件格線的整合」可處理「IoT 中樞」服務中發生的事件。 這些 IoT 中樞事件包括遙測資料、裝置建立、刪除、連線和中斷連接。 訂閱遙測事件時，您可以在資料上套用其他篩選，以便在發行至事件方格之前，先篩選 IoT 中樞中的訊息屬性、訊息內文和裝置對應項。 請參閱[如何篩選事件](../iot-hub/iot-hub-event-grid.md#filter-events)。

* **哪些端點需要接收此資訊？**

   IoT 中樞訊息路由支援有限數目的唯一端點和端點類型，但您可以建立連接器，將資料和事件重新路由傳送到其他端點。 如需支援的端點完整清單，請參閱上一節中的表格。 

   IoT 中樞與事件方格整合，可支援每個 IoT 中樞500個端點，以及更多的端點類型。 它會與 Azure Functions、Logic Apps、儲存體和服務匯流排佇列原生整合，也可與 webhook 合作，以擴充將資料傳送到 Azure 服務生態系統和協力廠商商務應用程式以外的地方。

* **如果資料有抵達順序，是否有影響？**

   「IoT 中樞訊息路由」會維持訊息的傳送順序，因此這些訊息會依照相同的方式抵達。

   「事件格線」則不保證端點收到事件的順序會與事件發生順序相同。 對於訊息的絕對順序很重要的情況，以及（或）取用者需要可信任的訊息唯一識別碼的情況，我們建議使用訊息路由。 

## <a name="next-steps"></a>後續步驟

* 深入了解 [IoT 中樞訊息路由](iot-hub-devguide-messages-d2c.md)和 [IoT 中樞端點](iot-hub-devguide-endpoints.md)。
* 深入了解 [Azure Event Grid](../event-grid/overview.md)。
* 如需了解如何建立訊息路由，請參閱[使用路由處理 IoT 中樞的裝置到雲端訊息](../iot-hub/tutorial-routing.md)教學課程。
* [使用 Logic Apps 傳送關於 Azure IoT 中樞事件的電子郵件通知](../event-grid/publish-iot-hub-events-to-logic-apps.md)，以試用事件方格整合。
