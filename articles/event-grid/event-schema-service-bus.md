---
title: 做為事件方格來源 Azure 服務匯流排
description: 描述 Azure Event Grid 中服務匯流排事件的屬性
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: babanisa
ms.openlocfilehash: 141a0e96071014dc3705d30f72b1a9257737298a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81393248"
---
# <a name="azure-service-bus-as-an-event-grid-source"></a>做為事件方格來源 Azure 服務匯流排

本文提供服務匯流排事件的屬性與結構描述。如需事件結構描述的簡介，請參閱 [Azure Event Grid 事件結構描述](event-schema.md)。

## <a name="event-grid-event-schema"></a>Event Grid 事件結構描述

### <a name="available-event-types"></a>可用的事件類型

服務匯流排會發出下列事件類型：

| 事件類型 | 描述 |
| ---------- | ----------- |
| Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners | 當佇列或訂用帳戶有作用中的訊息但沒有接聽的接收者時，就會引發。 |
| Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener | 當無效信件佇列中有作用中訊息但沒有作用中的接聽程式時，就會引發。 |

### <a name="example-event"></a>事件範例

下列範例會顯示不包含接聽程式事件的作用中訊息結構描述：

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourcegroups/{your-rg}/providers/Microsoft.ServiceBus/namespaces/{your-service-bus-namespace}",
  "subject": "topics/{your-service-bus-topic}/subscriptions/{your-service-bus-subscription}",
  "eventType": "Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://{your-service-bus-namespace}.servicebus.windows.net/{your-topic}/subscriptions/{your-service-bus-subscription}/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

無效信件佇列事件的結構描述如下：

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourcegroups/{your-rg}/providers/Microsoft.ServiceBus/namespaces/{your-service-bus-namespace}",
  "subject": "topics/{your-service-bus-topic}/subscriptions/{your-service-bus-subscription}",
  "eventType": "Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://{your-service-bus-namespace}.servicebus.windows.net/{your-topic}/subscriptions/{your-service-bus-subscription}/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="event-properties"></a>事件屬性

事件具有下列的最高層級資料：

| 屬性 | 類型 | 說明 |
| -------- | ---- | ----------- |
| 主題 | 字串 | 事件來源的完整資源路徑。 此欄位不可寫入。 Event Grid 提供此值。 |
| subject | 字串 | 發行者定義事件主體的路徑。 |
| eventType | 字串 | 此事件來源已註冊的事件類型之一。 |
| eventTime | 字串 | 事件產生的時間，以提供者之 UTC 時間為準。 |
| id | 字串 | 事件的唯一識別碼。 |
| data | 物件 | blob 儲存體帳戶。 |
| dataVersion | 字串 | 資料物件的結構描述版本。 發行者會定義結構描述版本。 |
| metadataVersion | 字串 | 事件中繼資料的結構描述版本。 Event Grid 會定義最上層屬性的結構描述。 Event Grid 提供此值。 |

資料物件具有下列屬性：

| 屬性 | 類型 | 說明 |
| -------- | ---- | ----------- |
| namespaceName | 字串 | 資源所在的服務匯流排命名空間。 |
| requestUri | 字串 | 要發出此事件的特定佇列或訂用帳戶 URI。 |
| entityType | 字串 | 發出事件 (佇列或訂閱) 的服務匯流排實體類型。 |
| queueName | 字串 | 如果訂閱佇列則為作用中訊息佇列。 如果使用主題 / 訂用帳戶則為 null 值。 |
| topicName | 字串 | 包含作用中訊息所屬之服務匯流排訂用帳戶的主題。 如果使用佇列則為 null 值。 |
| subscriptionName | 字串 | 具有作用中訊息的服務匯流排訂用帳戶。 如果使用佇列則為 null 值。 |

## <a name="tutorials-and-how-tos"></a>教學課程和操作說明
|Title  |描述  |
|---------|---------|
| [教學課程：Azure 服務匯流排與 Azure 事件方格的整合範例](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | 事件方格會從服務匯流排主題傳送訊息至函式應用程式和邏輯應用程式。 |
| [Azure 服務匯流排至事件格線整合](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md) | 整合服務匯流排與事件方格的概觀。 |

## <a name="next-steps"></a>後續步驟

* 如需 Azure Event Grid 的簡介，請參閱[什麼是 Event Grid？](overview.md)
* 若要了解 Event Grid 訂用帳戶的建立，請參閱 [Event Grid 訂用帳戶結構描述](subscription-creation-schema.md)。
* 如需使用 Azure Event Grid 搭配服務匯流排的詳細資訊，請參閱[服務匯流排與 Event Grid 的整合概觀](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md)。
* 嘗試[接收 Functions 或 Logic Apps 的服務匯流排事件](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json).
