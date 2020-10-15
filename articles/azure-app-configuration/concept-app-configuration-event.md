---
title: 回應 Azure 應用程式組態索引鍵/值事件
description: 使用 Azure 事件方格來訂閱應用程式設定事件，以允許應用程式回應索引鍵值的變更，而不需要複雜的程式碼。
services: azure-app-configuration,event-grid
author: jimmyca
ms.author: jimmyca
ms.date: 02/20/2020
ms.topic: article
ms.service: azure-app-configuration
ms.openlocfilehash: 640be797b2653f9e6c969306b7e2b99393b99c39
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92078199"
---
# <a name="reacting-to-azure-app-configuration-events"></a>回應 Azure 應用程式組態事件

Azure 應用程式組態事件可讓應用程式回應索引鍵/值的變更。 這樣做不需要複雜的程式碼或昂貴且無效率的輪詢服務。 相反地，事件會透過 [Azure 事件方格](https://azure.microsoft.com/services/event-grid/) 推送至「訂閱者」，例如 [Azure Functions](https://azure.microsoft.com/services/functions/)、 [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)，甚至是您自己的自訂 HTTP 接聽程式。 嚴格來說，您只需為使用的部分付費。

Azure 應用程式組態事件會傳送至 Azure 事件方格，以透過 rich 重試原則和寄不出的信件傳遞，提供可靠的傳遞服務給您的應用程式。 若要深入瞭解，請參閱 [事件方格訊息傳遞和重試](../event-grid/delivery-and-retry.md)。

常見的應用程式設定事件案例包括重新整理應用程式設定、觸發部署或任何設定導向的工作流程。 在變更不常見但情況需要立即回應的情況下，以事件為基礎的架構可能特別有效。

查看 [路由 Azure 應用程式組態事件至自訂的 web 端點-CLI](./howto-app-configuration-event.md) ，以取得快速範例。 

![Event Grid 模型](./media/event-grid-functional-model.png)

## <a name="available-azure-app-configuration-events"></a>可用的 Azure 應用程式組態事件
Event Grid 使用[事件訂閱](../event-grid/concepts.md#event-subscriptions)將事件訊息路由至訂閱者。 Azure 應用程式組態事件訂閱可以包含兩種類型的事件：  

> |活動名稱|描述|
> |----------|-----------|
> |`Microsoft.AppConfiguration.KeyValueModified`|在建立或取代索引鍵/值時引發|
> |`Microsoft.AppConfiguration.KeyValueDeleted`|在刪除索引鍵/值時引發|

## <a name="event-schema"></a>結構描述
Azure 應用程式組態事件包含回應資料變更所需的所有資訊。 您可以識別應用程式設定事件，因為 [事件識別碼] 屬性的開頭是 "AppConfiguration"。 Event Grid 事件屬性之使用方式的其他資訊列於[Event Grid 事件結構描述](../event-grid/event-schema.md)。  

> |屬性|類型|描述|
> |-------------------|------------------------|-----------------------------------------------------------------------|
> |主題|字串|發出事件之應用程式設定的完整 Azure Resource Manager 識別碼。|
> |subject|字串|索引鍵-值的 URI，此為事件的主旨。|
> |eventTime|字串|產生事件的日期/時間，格式為 ISO 8601。|
> |eventType|字串|"AppConfiguration. KeyValueModified" 或 "AppConfiguration. KeyValueDeleted"。|
> |Id|字串|這個事件的唯一識別碼。|
> |dataVersion|字串|資料物件的結構描述版本。|
> |metadataVersion|字串|最上層屬性的結構描述版本。|
> |data|物件 (object)|Azure 應用程式組態特定事件資料的集合|
> |資料。金鑰|字串|修改或刪除之索引鍵/值的索引鍵。|
> |資料。標籤|字串|修改或刪除之索引鍵/值的標籤（如果有的話）。|
> |資料 etag|字串|適用于 `KeyValueModified` 新索引鍵/值的 etag。 針對已 `KeyValueDeleted` 刪除之索引鍵/值的 etag。|

以下是 KeyValueModified 事件的範例：
```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueModified",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]

```

如需詳細資訊，請參閱 [Azure 應用程式組態 events 架構](../event-grid/event-schema-app-configuration.md)。

## <a name="practices-for-consuming-events"></a>消費事件做法
處理應用程式設定事件的應用程式應該遵循下列建議做法：
> [!div class="checklist"]
> * 您可以設定多個訂用帳戶，將事件路由傳送至相同的事件處理常式，因此請勿假設事件來自特定的來源。 相反地，請檢查訊息的主題，以確定傳送事件的應用程式設定實例。
> * 檢查結果，並不要假設您收到的所有事件都是您預期的類型。
> * 使用 etag 欄位來瞭解物件的相關資訊是否仍為最新狀態。  
> * 使用 sequencer 欄位來瞭解任何特定物件上的事件順序。
> * 使用 [主旨] 欄位來存取已修改的索引鍵/值。


## <a name="next-steps"></a>後續步驟

深入瞭解事件方格，並嘗試 Azure 應用程式組態的事件：

- [關於 Event Grid](../event-grid/overview.md)
- [將 Azure 應用程式組態事件路由至自訂 web 端點](./howto-app-configuration-event.md)