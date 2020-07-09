---
title: 做為事件方格來源 Azure 應用程式組態
description: 本文說明如何使用 Azure 應用程式組態做為事件方格事件來源。 其會提供教學課程與操作說明文章的結構描述和連結。
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: bdd077c291bd1e1c441217740daf39c8bcaad732
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86116993"
---
# <a name="azure-app-configuration-as-an-event-grid-source"></a>做為事件方格來源 Azure 應用程式組態
本文提供 Azure 應用程式組態事件的屬性和架構。 如需事件結構描述的簡介，請參閱 [Azure Event Grid 事件結構描述](event-schema.md)。 它也會提供快速入門和教學課程的清單，供您使用 Azure 應用程式組態作為事件來源。

## <a name="event-grid-event-schema"></a>Event Grid 事件結構描述

### <a name="available-event-types"></a>可用的事件類型

Azure 應用程式組態會發出下列事件種類：

| 事件類型 | 描述 |
| ---------- | ----------- |
| AppConfiguration. KeyValueModified | 在建立或取代索引鍵/值時引發。 |
| AppConfiguration. KeyValueDeleted | 刪除索引鍵/值時引發。 |

### <a name="example-event"></a>事件範例

下列範例會顯示索引鍵-值修改事件的架構： 

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

索引鍵/值刪除事件的架構類似： 

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
  "eventType": "Microsoft.AppConfiguration.KeyValueDeleted",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```
 
### <a name="event-properties"></a>事件屬性

事件具有下列的最高層級資料：

| 屬性 | 類型 | 描述 |
| -------- | ---- | ----------- |
| 主題 | 字串 | 事件來源的完整資源路徑。 此欄位不可寫入。 Event Grid 提供此值。 |
| subject | 字串 | 發行者定義事件主體的路徑。 |
| eventType | 字串 | 此事件來源已註冊的事件類型之一。 |
| eventTime | 字串 | 事件產生的時間，以提供者之 UTC 時間為準。 |
| 識別碼 | 字串 | 事件的唯一識別碼。 |
| data | 物件 (object) | 應用程式組態事件資料。 |
| dataVersion | 字串 | 資料物件的結構描述版本。 發行者會定義結構描述版本。 |
| metadataVersion | 字串 | 事件中繼資料的結構描述版本。 Event Grid 會定義最上層屬性的結構描述。 Event Grid 提供此值。 |

資料物件具有下列屬性：

| 屬性 | 類型 | Description |
| -------- | ---- | ----------- |
| 索引鍵 | 字串 | 已修改或刪除之索引鍵/值的索引鍵。 |
| 標籤 | 字串 | 已修改或刪除之索引鍵/值的標籤（如果有的話）。 |
| etag | 字串 | 適用于 `KeyValueModified` 新索引鍵/值的 etag。 針對已 `KeyValueDeleted` 刪除之索引鍵/值的 etag。 |

## <a name="tutorials-and-how-tos"></a>教學課程和操作說明

|Title | 描述 |
|---------|---------|
| [使用事件方格來回應 Azure 應用程式組態事件](../azure-app-configuration/concept-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | 整合 Azure 應用程式組態與事件方格的總覽。 |
| [快速入門：使用 Azure CLI 將 Azure 應用程式組態事件路由至自訂 web 端點](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | 示範如何使用 Azure CLI 將 Azure 應用程式組態事件傳送至 WebHook。 |

## <a name="next-steps"></a>後續步驟

* 如需 Azure Event Grid 的簡介，請參閱[什麼是 Event Grid？](overview.md)
* 若要了解 Event Grid 訂用帳戶的建立，請參閱 [Event Grid 訂用帳戶結構描述](subscription-creation-schema.md)。
* 如需使用 Azure 應用程式組態事件的簡介，請參閱[路由 Azure 應用程式組態事件-Azure CLI](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json)。 