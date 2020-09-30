---
title: 事件路由
titleSuffix: Azure Digital Twins
description: 瞭解如何將 Azure 數位 Twins 內的事件路由傳送至其他 Azure 服務。
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: d41518b1fc0d8cdda3ded1e8036bd29e24e2b34a
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91541351"
---
# <a name="route-events-within-and-outside-of-azure-digital-twins"></a>在 Azure 數位 Twins 內外路由傳送事件

Azure 數位 twins 會使用 **事件路由** 將資料傳送給服務外的取用者。 

在預覽期間，傳送 Azure 數位 Twins 資料有兩種主要案例：
* 將資料從 Azure 數位 Twins 圖形中的一個對應項傳送到另一個對應項。 例如，當某個數位對應項上的屬性變更時，您可能會想要通知並據此更新另一個數位對應項。
* 將資料傳送至下游資料服務以進行額外的儲存或處理 (也稱為 *資料* 輸出) 。 例如，
  - 醫院可能會想要將 Azure 數位 Twins 事件資料傳送到 [時間序列深入解析 (TSI) ](../time-series-insights/time-series-insights-update-overview.md)，以記錄大量分析之 handwashing 相關事件的時間序列資料。
  - 已在使用 [Azure 地圖服務](../azure-maps/about-azure-maps.md) 的企業可能會想要使用 Azure 數位 Twins 來增強其解決方案。 他們可以在設定 Azure 數位 Twins 之後快速啟用 Azure 地圖、將 Azure 地圖實體納入 Azure 數位 Twins 作為對應項圖形中的 [數位 Twins](concepts-twins-graph.md) ，或是利用其 Azure 地圖服務和 Azure 數位 Twins 資料來執行功能強大的查詢。

這兩種情況都會使用事件路由。

## <a name="about-event-routes"></a>關於事件路由

事件路由可讓您從 Azure 數位 Twins 中的數位 twins，將事件資料傳送至您訂用帳戶中自訂定義的端點。 端點目前支援三項 Azure 服務： [事件中樞](../event-hubs/event-hubs-about.md)、 [事件方格](../event-grid/overview.md)和 [服務匯流排](../service-bus-messaging/service-bus-messaging-overview.md)。 每個 Azure 服務都可以連線到其他服務並作為中間人，將資料傳送到最終目的地（例如 TSI 或 Azure 地圖服務）來處理您需要的任何處理。

下圖說明透過 Azure 數位 Twins 層面的大型 IoT 解決方案來處理事件資料的流程：

:::image type="content" source="media/concepts-route-events/routing-workflow.png" alt-text="Azure 數位 Twins 透過端點將資料路由傳送到數個下游服務" border="false":::

事件路由的一般下游目標包括 TSI、Azure 地圖服務、儲存體和分析解決方案等資源。

### <a name="event-routes-for-internal-digital-twin-events"></a>內部數位對應項事件的事件路由

在目前的預覽版本中，事件路由也會用來處理對應項圖形內的事件，並將資料從數位對應項傳送至數位對應項。 這是藉由將事件路由透過事件方格連接到計算資源（例如 [Azure Functions](../azure-functions/functions-overview.md)）來完成。 然後，這些函式會定義 twins 應該如何接收和回應事件。 

當計算資源想要根據透過事件路由收到的事件來修改對應項圖表時，它會很有説明，讓它知道要事先修改的對應項。 

或者，事件訊息也包含傳送訊息之來源對應項的識別碼，因此計算資源可以使用查詢或跨越關聯性來尋找所需作業的目標對應項。 

計算資源也需要獨立建立安全性和存取權限。

若要逐步完成設定 Azure 函式以處理數位對應項事件的程式，請參閱 how [*to：設定用來處理資料的 azure 函數*](how-to-create-azure-function.md)。

## <a name="create-an-endpoint"></a>建立端點

若要定義事件路由，開發人員必須先定義端點。 **端點**是 Azure 數位 Twins 外部的目的地，可支援路由連接。 目前預覽版本中支援的目的地為：
* 事件方格自訂主題
* 事件中樞
* 服務匯流排

若要建立端點，您可以使用 Azure 數位 Twins [**控制平面 api**](how-to-manage-routes-apis-cli.md#create-an-endpoint-for-azure-digital-twins)、 [**CLI 命令**](how-to-manage-routes-apis-cli.md#manage-endpoints-and-routes-with-cli)或 [**Azure 入口網站**](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins)。 

定義端點時，您必須提供：
* 端點的名稱
* 端點類型 (事件方格、事件中樞或服務匯流排) 
* 要驗證的主要連接字串和次要連接字串 
* 端點的主題路徑，例如 *your-topic.westus2.eventgrid.azure.net*

可在控制平面中使用的端點 Api 如下：
* 建立端點
* 取得端點清單
* 依名稱取得端點
* 依名稱刪除端點

## <a name="create-an-event-route"></a>建立事件路由
 
若要建立事件路由，您可以使用 Azure 數位 Twins [**資料平面 api**](how-to-manage-routes-apis-cli.md#create-an-event-route)、 [**CLI 命令**](how-to-manage-routes-apis-cli.md#manage-endpoints-and-routes-with-cli)或 [**Azure 入口網站**](how-to-manage-routes-portal.md#create-an-event-route)。 

以下範例示範如何使用 `CreateEventRoute` [.Net (c # ) SDK](how-to-use-apis-sdks.md) 呼叫，在用戶端應用程式中建立事件路由： 

```csharp
EventRoute er = new EventRoute("endpointName");
er.Filter("true"); //Filter allows all messages
await client.CreateEventRoute("routeName", er);
```

1. 首先 `EventRoute` 會建立物件，而此函式會採用端點的名稱。 此 `endpointName` 欄位會識別端點，例如事件中樞、事件方格或服務匯流排。 您必須在訂用帳戶中建立這些端點，並使用控制平面 Api 連接至 Azure 數位 Twins，然後再進行此註冊呼叫。

2. 事件路由物件也有 [**篩選**](./how-to-manage-routes-apis-cli.md#filter-events) 欄位，可用來限制遵循此路由的事件種類。 篩選 `true` 可讓您不進行其他篩選， (篩選準則停用 `false` 路由) 。 

3. 此事件路由物件接著會傳遞至 `CreateEventRoute` ，以及路由的名稱。

> [!TIP]
> 所有 SDK 函式都有同步和非同步版本。

您也可以使用 [Azure 數位 TWINS CLI](how-to-use-cli.md)來建立路由。

### <a name="types-of-event-messages"></a>事件訊息的類型

IoT 中樞和 Azure 數位 Twins 中的不同事件種類會產生不同類型的通知訊息，如下所述。

[!INCLUDE [digital-twins-notifications.md](../../includes/digital-twins-notifications.md)]

## <a name="next-steps"></a>後續步驟

請參閱如何設定和管理事件路由：
* [*如何：管理端點和路由*](how-to-manage-routes-apis-cli.md)

或者，請參閱如何使用 Azure Functions 在 Azure 數位 Twins 中路由傳送事件：
* [*How to：設定用來處理資料的 Azure 函數*](how-to-create-azure-function.md)