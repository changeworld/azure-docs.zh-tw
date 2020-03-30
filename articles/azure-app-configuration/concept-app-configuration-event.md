---
title: 回應 Azure 應用配置鍵值事件
description: 使用 Azure 事件網格訂閱應用配置事件。
services: azure-app-configuration,event-grid
author: jimmyca
ms.author: jimmyca
ms.date: 02/20/2020
ms.topic: article
ms.service: azure-app-configuration
ms.openlocfilehash: a4f61d147ba1abf73ada6360b8d0d965d8e063a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523793"
---
# <a name="reacting-to-azure-app-configuration-events"></a>回應 Azure 應用配置事件

Azure 應用配置事件使應用程式能夠對鍵值中的更改做出反應。 這樣做不需要複雜的代碼或昂貴且低效的輪詢服務。 相反，事件通過[Azure 事件網格](https://azure.microsoft.com/services/event-grid/)推送到訂閱者，如[Azure 函數](https://azure.microsoft.com/services/functions/)[、Azure 邏輯應用](https://azure.microsoft.com/services/logic-apps/)，甚至推送到您自己的自訂 HTTP 攔截器。 關鍵是，你只為你使用的東西付費。

Azure 應用配置事件將發送到 Azure 事件網格，該網格通過豐富的重試策略和死信傳遞向應用程式提供可靠的傳遞服務。 要瞭解更多資訊，請參閱[事件網格消息傳遞和重試](https://docs.microsoft.com/azure/event-grid/delivery-and-retry)。

常見的應用配置事件方案包括刷新應用程式佈建、觸發部署或任何面向配置的工作流。 在變更不常見但情況需要立即回應的情況下，以事件為基礎的架構可能特別有效。

請查看[將 Azure 應用配置事件路由到自訂 Web 終結點 - CLI，](./howto-app-configuration-event.md)瞭解快速示例。 

![Event Grid 模型](./media/event-grid-functional-model.png)

## <a name="available-azure-app-configuration-events"></a>可用的 Azure 應用配置事件
Event Grid 使用[事件訂閱](../event-grid/concepts.md#event-subscriptions)將事件訊息路由至訂閱者。 Azure 應用配置事件訂閱可以包括兩種類型的事件：  

> |活動名稱|描述|
> |----------|-----------|
> |`Microsoft.AppConfiguration.KeyValueModified`|創建或替換鍵值時觸發|
> |`Microsoft.AppConfiguration.KeyValueDeleted`|刪除鍵值時觸發|

## <a name="event-schema"></a>結構描述
Azure 應用配置事件包含回應資料更改所需的所有資訊。 您可以標識應用配置事件，因為事件種類屬性以"Microsoft.App 配置"開頭。 Event Grid 事件屬性之使用方式的其他資訊列於[Event Grid 事件結構描述](../event-grid/event-schema.md)。  

> |屬性|類型|描述|
> |-------------------|------------------------|-----------------------------------------------------------------------|
> |主題|字串|發出事件的應用配置的完整 Azure 資源管理器 ID。|
> |subject|字串|作為事件主題的鍵值的 URI。|
> |eventTime|字串|以 ISO 8601 格式建置事件的日期/時間。|
> |eventType|字串|"微軟.App配置.鍵值修改"或"微軟.App配置.鍵值刪除"。|
> |Id|字串|此事件的唯一識別碼。|
> |dataVersion|字串|資料物件的結構描述版本。|
> |metadataVersion|字串|最上層屬性的結構描述版本。|
> |data|物件 (object)|Azure 應用配置特定事件資料的集合|
> |資料.鍵|字串|已修改或刪除的鍵值的鍵。|
> |資料.標籤|字串|修改或刪除的鍵值的標籤（如果有）。|
> |資料.etag|字串|對於`KeyValueModified`新鍵值的 etag。 對於`KeyValueDeleted`已刪除的鍵值的 etag。|

下面是 KeyValue 修改事件的示例：
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

有關詳細資訊，請參閱[Azure 應用配置事件架構](../event-grid/event-schema-app-configuration.md)。

## <a name="practices-for-consuming-events"></a>消費事件做法
處理應用配置事件的應用程式應遵循以下建議的做法：
> [!div class="checklist"]
> * 可以將多個訂閱配置為將事件路由到同一事件處理常式，因此不要假定事件來自特定源。 相反，請檢查消息的主題，以確保應用配置實例發送事件。
> * 檢查事件種類，不要假定您收到的所有事件都將是您期望的類型。
> * 使用 etag 欄位瞭解有關物件的資訊是否仍然是最新的。  
> * 使用定序器欄位瞭解任何特定物件上事件的順序。
> * 使用主題欄位訪問已修改的鍵值。


## <a name="next-steps"></a>後續步驟

瞭解有關事件網格的更多資訊，並嘗試 Azure 應用配置事件：

- [關於 Event Grid](../event-grid/overview.md)
- [將 Azure 應用配置事件路由到自訂 Web 終結點](./howto-app-configuration-event.md)