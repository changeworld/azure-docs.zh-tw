---
title: Azure 事件方格事件處理常式
description: 描述支援的 Azure 事件方格事件處理常式。 Azure 自動化、Functions、事件中樞、混合式連線、Logic Apps、服務匯流排、佇列儲存體、Webhook。
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 76f7fff3e0a3e1c0c554e452987bf944503358b8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86117010"
---
# <a name="event-handlers-in-azure-event-grid"></a>Azure 事件方格中的事件處理常式
事件處理常式是傳送事件的位置。 處理常式會採取一些進一步的動作來處理事件。 許多 Azure 服務都已自動設定為可處理事件。 您也可以使用任何 Webhook 來處理事件。 Webhook 不需要裝載於 Azure 也能處理處理事件。 Event Grid 僅支援 HTTPS Webhook 端點。

## <a name="supported-event-handlers"></a>支援的事件處理常式
以下是支援的事件處理常式： 

- [Webhook](handler-webhooks.md)。 Azure 自動化 Runbook 和 Logic Apps 均可透過 Webhook 支援。 
- [Azure Functions](handler-functions.md)
- [事件中樞](handler-event-hubs.md)
- [轉送混合式連線](handler-relay-hybrid-connections.md)
- [服務匯流排佇列和主題](handler-service-bus.md)
- [儲存體佇列](handler-storage-queues.md)

## <a name="next-steps"></a>後續步驟
- 如需 Event Grid 的簡介，請參閱[關於 Event Grid](overview.md)。
