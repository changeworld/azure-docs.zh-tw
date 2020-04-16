---
title: Azure 應用程式設定為事件網格來源
description: 本文介紹如何使用 Azure 應用配置作為事件網格事件源。 它提供了與教程和"訪問者"文章的架構和連結。
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: babanisa
ms.openlocfilehash: adb548ef8531698a2cb075fbc742bb20a02a434b
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393421"
---
# <a name="azure-app-configuration-as-an-event-grid-source"></a>Azure 應用程式設定為事件網格來源
本文提供 Azure 應用配置事件的屬性和架構。 如需事件結構描述的簡介，請參閱 [Azure Event Grid 事件結構描述](event-schema.md)。 它還為您提供了使用 Azure 應用配置作為事件源的快速啟動和教程的清單。

## <a name="event-grid-event-schema"></a>Event Grid 事件結構描述

### <a name="available-event-types"></a>可用的事件類型

Azure 應用程式設定發出以下事件型態:

| 事件類型 | 描述 |
| ---------- | ----------- |
| 微軟.App配置.鍵值修改 | 創建或替換鍵值時引發。 |
| 微軟.應用程式設定.鍵值已刪除 | 刪除鍵值時引發。 |

### <a name="example-event"></a>事件範例

下面的範例顯示了鍵值修改事件的架構: 

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

刪除鍵值事件的架構類似: 

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
| ID | 字串 | 事件的唯一識別碼。 |
| data | 物件 (object) | 應用配置事件數據。 |
| dataVersion | 字串 | 資料物件的結構描述版本。 發行者會定義結構描述版本。 |
| metadataVersion | 字串 | 事件中繼資料的結構描述版本。 Event Grid 會定義最上層屬性的結構描述。 Event Grid 提供此值。 |

資料物件具有下列屬性：

| 屬性 | 類型 | 描述 |
| -------- | ---- | ----------- |
| 索引鍵 | 字串 | 已修改或刪除的鍵值的鍵。 |
| 標籤 | 字串 | 修改或刪除的鍵值的標籤(如果有)。 |
| etag | 字串 | 對於`KeyValueModified`新鍵值的 etag。 對於`KeyValueDeleted`已刪除的鍵值的 etag。 |

## <a name="tutorials-and-how-tos"></a>教學和如何

|Title | 描述 |
|---------|---------|
| [使用事件網格回應 Azure 應用設定事件](../azure-app-configuration/concept-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | 將 Azure 應用配置與事件網格整合的概述。 |
| [快速入門:使用 Azure CLI 將 Azure 應用設定事件路由到自訂 Web 終結點](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | 展示如何使用 Azure CLI 將 Azure 應用設定事件發送到 WebHook。 |

## <a name="next-steps"></a>後續步驟

* 如需 Azure Event Grid 的簡介，請參閱[什麼是 Event Grid？](overview.md)
* 若要了解 Event Grid 訂用帳戶的建立，請參閱 [Event Grid 訂用帳戶結構描述](subscription-creation-schema.md)。
* 有關使用 Azure 應用設定事件的簡介,請參閱[路由 Azure 應用設定事件 - Azure CLI](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json)。 