---
title: 事件路由
titleSuffix: Azure Digital Twins
description: 瞭解如何將 Azure 數位 Twins 和其他 Azure 服務內的事件路由傳送。
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 7c1f0c18fc8b867e16ce539a24839637e1d14379
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85390783"
---
# <a name="route-events-within-and-outside-of-azure-digital-twins"></a>在 Azure 數位 Twins 內外路由事件

Azure 數位 twins 會使用**事件路由**，將資料傳送給服務以外的取用者。 

在預覽期間，傳送 Azure 數位 Twins 資料有兩個主要案例：
* 將資料從 Azure 數位 Twins 圖形中的一個對應項傳送至另一個。 例如，當某個數位對應項上的屬性變更時，您可能會想要據此通知並更新另一個數位對應項。
* 將資料傳送到下游資料服務，以進行額外的儲存或處理（也稱為*資料*輸出）。 例如，
  - 醫院可能會想要將 Azure 數位 Twins 事件資料傳送至[時間序列深入解析（TSI）](../time-series-insights/time-series-insights-update-overview.md)，以記錄大量分析之 handwashing 相關事件的時間序列資料。
  - 已在使用[Azure 地圖服務](../azure-maps/about-azure-maps.md)的企業可能會想要使用 Azure 數位 Twins 來增強其解決方案。 在設定 Azure 數位 Twins 之後，他們可以快速啟用 Azure 地圖、將 Azure 地圖實體帶入 Azure 數位 Twins 作為對應項圖形中的[數位 Twins](concepts-twins-graph.md) ，或利用其 Azure 地圖服務和 Azure 數位 Twins 資料一起執行強大的查詢。

這兩種案例都使用事件路由。

## <a name="about-event-routes"></a>關於事件路由

事件路由可讓您從 Azure 數位 Twins 中的數位 twins，將事件資料傳送至您訂用帳戶中的自訂定義端點。 端點目前支援三項 Azure 服務：[事件中樞](../event-hubs/event-hubs-about.md)、[事件方格](../event-grid/overview.md)和[服務匯流排](../service-bus-messaging/service-bus-messaging-overview.md)。 每個 Azure 服務都可以連接到其他服務，並作為中間人，將資料傳送到最終目的地（例如 TSI 或 Azure 地圖服務），以進行您需要的任何處理。

下圖說明透過具有 Azure 數位 Twins 層面的較大型 IoT 解決方案的事件資料流程程：

:::image type="content" source="media/concepts-route-events/routing-workflow.png" alt-text="透過端點將資料路由至數個下游服務的 Azure 數位 Twins" border="false":::

事件路由的一般下游目標是 TSI、Azure 地圖服務、儲存體和分析解決方案等資源。

### <a name="event-routes-for-internal-digital-twin-events"></a>內部數位對應項事件的事件路由

在目前的預覽版本中，事件路由也會用來處理對應項圖形內的事件，並將數位對應項的資料傳送至數位對應項。 這是藉由將事件路由透過事件方格連接到計算資源（例如[Azure Functions](../azure-functions/functions-overview.md)）來完成。 這些函數接著會定義 twins 應如何接收和回應事件。 

當計算資源想要根據透過事件路由收到的事件來修改對應項圖形時，它會很有説明，知道它想要事先修改的對應項。 

或者，事件訊息也包含傳送訊息之來源對應項的識別碼，因此計算資源可以使用查詢或遍歷關聯性來尋找所需作業的目標對應項。 

計算資源也需要獨立建立安全性和存取權限。

若要逐步完成設定 Azure 函式以處理數位對應項事件的程式，請參閱[如何：設定用來處理資料的 azure 函數](how-to-create-azure-function.md)。

## <a name="create-an-endpoint"></a>建立端點

若要定義事件路由，開發人員必須先定義端點。 「**端點**」（endpoint）是 Azure 數位 Twins 外部的目的地，可支援路由連接。 目前預覽版本中支援的目的地為：
* 事件方格自訂主題
* 事件中樞
* 服務匯流排

端點會使用控制平面 Api （ [Azure 數位 TWINS CLI](how-to-use-cli.md)所支援，或透過 Azure 入口網站來設定）。 端點定義提供：
* 端點的名稱
* 端點類型（事件方格、事件中樞或服務匯流排）
* 要驗證的主要連接字串和次要連接字串 
* 端點的主題路徑，例如*your-topic.westus2.eventgrid.azure.net*

控制平面中可用的端點 Api 包括：
* 建立端點
* 取得端點清單
* 依名稱取得端點
* 依名稱刪除端點

## <a name="create-an-event-route"></a>建立事件路由
 
事件路由是在用戶端應用程式中使用下列[.net （c #） SDK](how-to-use-apis-sdks.md)呼叫所建立： 

```csharp
await client.EventRoutes.AddAsync("<name-for-the-new-route>", new EventRoute("<endpoint-name>"));
```

* 會 `endpoint-name` 識別端點，例如事件中樞、事件方格或服務匯流排。 在進行此註冊呼叫之前，必須先在您的訂用帳戶中建立這些端點，並使用控制平面 Api 附加至 Azure 數位 Twins。

傳遞給的事件路由物件 `EventRoutes.Add` 也會接受[**篩選**參數](./how-to-manage-routes.md#filter-events)，這可以用來限制遵循此路由的事件種類。

您也可以使用[Azure 數位 TWINS CLI](how-to-use-cli.md)來建立路由。

### <a name="types-of-event-messages"></a>事件訊息的類型

IoT 中樞和 Azure 數位 Twins 中不同類型的事件會產生不同類型的通知訊息，如下所述。

[!INCLUDE [digital-twins-notifications.md](../../includes/digital-twins-notifications.md)]

## <a name="next-steps"></a>後續步驟

請參閱如何設定和管理事件路由：
* [如何：管理端點和路由](how-to-manage-routes.md)

或者，請參閱如何使用 Azure Functions 來路由傳送 Azure 數位 Twins 內的事件：
* [如何：設定用來處理資料的 Azure 函數](how-to-create-azure-function.md)