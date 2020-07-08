---
title: 作為事件方格來源的 Azure SignalR
description: 說明使用 Azure 事件方格為 Azure SignalR 事件提供的屬性
services: event-grid
author: femila
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/23/2020
ms.author: femila
ms.openlocfilehash: 8fbae3fad4aeb85022c804e1ac648060360c6531
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84560621"
---
# <a name="azure-event-grid-event-schema-for-signalr-service"></a>適用于 SignalR Service 的 Azure 事件方格事件架構

本文提供 SignalR Service 事件的屬性和架構。如需事件結構描述的簡介，請參閱 [Azure Event Grid 事件結構描述](event-schema.md)。 它也會提供快速入門和教學課程的清單，以使用 Azure SignalR 作為事件來源。

## <a name="event-grid-event-schema"></a>Event Grid 事件結構描述

### <a name="available-event-types"></a>可用的事件類型

SignalR Service 會發出下列事件種類：

| 事件類型 | 描述 |
| ---------- | ----------- |
| Microsoft.signalrservice. ClientConnectionConnected | 當用戶端連接連接時引發。 |
| Microsoft.signalrservice. ClientConnectionDisconnected | 在用戶端連接中斷連線時引發。 |

### <a name="example-event"></a>事件範例

下列範例顯示用戶端連接連接事件的架構： 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/signalr-rg/providers/Microsoft.SignalRService/SignalR/signalr-resource",
  "subject": "/hub/chat",
  "eventType": "Microsoft.SignalRService.ClientConnectionConnected",
  "eventTime": "2019-06-10T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "timestamp": "2019-06-10T18:41:00.9584103Z",
    "hubName": "chat",
    "connectionId": "crH0uxVSvP61p5wkFY1x1A",
    "userId": "user-eymwyo23"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

用戶端連接中斷線上活動的架構類似： 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/signalr-rg/providers/Microsoft.SignalRService/SignalR/signalr-resource",
  "subject": "/hub/chat",
  "eventType": "Microsoft.SignalRService.ClientConnectionDisconnected",
  "eventTime": "2019-06-10T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "timestamp": "2019-06-10T18:41:00.9584103Z",
    "hubName": "chat",
    "connectionId": "crH0uxVSvP61p5wkFY1x1A",
    "userId": "user-eymwyo23",
    "errorMessage": "Internal server error."
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="event-properties"></a>事件屬性

事件具有下列的最高層級資料：

| 屬性 | 類型 | Description |
| -------- | ---- | ----------- |
| 主題 | 字串 | 事件來源的完整資源路徑。 此欄位不可寫入。 Event Grid 提供此值。 |
| subject | 字串 | 發行者定義事件主體的路徑。 |
| eventType | 字串 | 此事件來源已註冊的事件類型之一。 |
| eventTime | 字串 | 事件產生的時間，以提供者之 UTC 時間為準。 |
| id | 字串 | 事件的唯一識別碼。 |
| data | 物件 (object) | SignalR Service 事件資料。 |
| dataVersion | 字串 | 資料物件的結構描述版本。 發行者會定義結構描述版本。 |
| metadataVersion | 字串 | 事件中繼資料的結構描述版本。 Event Grid 會定義最上層屬性的結構描述。 Event Grid 提供此值。 |

資料物件具有下列屬性：

| 屬性 | 類型 | Description |
| -------- | ---- | ----------- |
| timestamp | 字串 | 事件產生的時間，以提供者之 UTC 時間為準。 |
| hubName | 字串 | 用戶端連接所屬的中樞。 |
| connectionId | 字串 | 用戶端連接的唯一識別碼。 |
| userId | 字串 | 宣告中定義的使用者識別碼。 |
| errorMessage | 字串 | 導致連線中斷連接的錯誤。 |

## <a name="tutorials-and-how-tos"></a>教學課程和操作說明
|Title | Description |
|---------|---------|
| [使用事件方格來回應 Azure SignalR Service 事件](../azure-signalr/signalr-concept-event-grid-integration.md) | 整合 Azure SignalR Service 與事件方格的總覽。 |
| [如何將 Azure SignalR Service 事件傳送至事件方格](../azure-signalr/signalr-howto-event-grid-integration.md) | 說明如何透過事件方格將 Azure SignalR Service 事件傳送至應用程式。 |

## <a name="next-steps"></a>後續步驟

* 如需 Azure Event Grid 的簡介，請參閱[什麼是 Event Grid？](overview.md)
* 若要了解 Event Grid 訂用帳戶的建立，請參閱 [Event Grid 訂用帳戶結構描述](subscription-creation-schema.md)。
