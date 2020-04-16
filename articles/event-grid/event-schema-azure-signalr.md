---
title: Azure 唱歌器作為事件網格源
description: 描述為 Azure 事件格格為 Azure SignalR 事件提供的屬性
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: babanisa
ms.openlocfilehash: 730d1a7a053ab636c45313dd0c35a537434eb782
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393401"
---
# <a name="azure-event-grid-event-schema-for-signalr-service"></a>SignalR 服務的 Azure 事件網格事件架構

本文提供了 SignalR 服務事件的屬性和架構。如需事件結構描述的簡介，請參閱 [Azure Event Grid 事件結構描述](event-schema.md)。 它還為您提供了使用 Azure SignalR 作為事件來源的快速啟動和教學的清單。

## <a name="event-grid-event-schema"></a>Event Grid 事件結構描述

### <a name="available-event-types"></a>可用的事件類型

SignalR 服務發出以下事件類型:

| 事件類型 | 描述 |
| ---------- | ----------- |
| 微軟.SignalR服務.用戶端連接連接 | 連接用戶端連接時引發。 |
| 微軟.SignalR服務.用戶端連接斷線連線 | 用戶端連接斷開連接時引發。 |

### <a name="example-event"></a>事件範例

下面的範例顯示客戶端連線事件的架構: 

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

用戶端連線斷線連線事件的架構類似: 

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

| 屬性 | 類型 | 描述 |
| -------- | ---- | ----------- |
| 主題 | 字串 | 事件來源的完整資源路徑。 此欄位不可寫入。 Event Grid 提供此值。 |
| subject | 字串 | 發行者定義事件主體的路徑。 |
| eventType | 字串 | 此事件來源已註冊的事件類型之一。 |
| eventTime | 字串 | 事件產生的時間，以提供者之 UTC 時間為準。 |
| id | 字串 | 事件的唯一識別碼。 |
| data | 物件 (object) | 信號R服務事件數據。 |
| dataVersion | 字串 | 資料物件的結構描述版本。 發行者會定義結構描述版本。 |
| metadataVersion | 字串 | 事件中繼資料的結構描述版本。 Event Grid 會定義最上層屬性的結構描述。 Event Grid 提供此值。 |

資料物件具有下列屬性：

| 屬性 | 類型 | 描述 |
| -------- | ---- | ----------- |
| timestamp | 字串 | 事件產生的時間，以提供者之 UTC 時間為準。 |
| hubName | 字串 | 用戶端連接所屬的集線器。 |
| connectionId | 字串 | 用戶端連接的唯一標識碼。 |
| userId | 字串 | 聲明中定義的用戶識別碼。 |
| errorMessage | 字串 | 導致連接斷開連接的錯誤。 |

## <a name="tutorials-and-how-tos"></a>教學和如何
|Title | 描述 |
|---------|---------|
| [使用事件網格回應 Azure SignalR 服務事件](../azure-signalr/signalr-concept-event-grid-integration.md) | 將 Azure SignalR 服務與事件網格整合的概述。 |
| [如何向事件網格傳送 Azure SignalR 服務事件](../azure-signalr/signalr-howto-event-grid-integration.md) | 演示如何通過事件網格向應用程式發送 Azure SignalR 服務事件。 |

## <a name="next-steps"></a>後續步驟

* 如需 Azure Event Grid 的簡介，請參閱[什麼是 Event Grid？](overview.md)
* 若要了解 Event Grid 訂用帳戶的建立，請參閱 [Event Grid 訂用帳戶結構描述](subscription-creation-schema.md)。
