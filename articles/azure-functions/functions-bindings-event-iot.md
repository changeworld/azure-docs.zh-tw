---
title: Azure Functions 的 Azure IoT 中樞系結
description: 瞭解如何在 Azure Functions 中使用 IoT 中樞觸發程式和系結。
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 1c25543b16c3486a8f6a445427346382faaaa09a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77586119"
---
# <a name="azure-iot-hub-bindings-for-azure-functions"></a>Azure Functions 的 Azure IoT 中樞系結

這一組文章說明如何使用 IoT 中樞的 Azure Functions 系結。 IoT 中樞支援是以[Azure 事件中樞](functions-bindings-event-hubs.md)系結為基礎。

> [!IMPORTANT]
> 雖然下列程式碼範例會使用事件中樞 API，但指定的語法適用于 IoT 中樞函式。

| 動作 | 類型 |
|--------|------|
| 回應傳送至 IoT 中樞事件資料流程的事件。 | [觸發程序](./functions-bindings-event-iot-trigger.md) |
| 將事件寫入 IoT 事件資料流程 | [輸出系結](./functions-bindings-event-iot-output.md) |

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs.md)]

## <a name="next-steps"></a>後續步驟

- [回應傳送至事件中樞事件資料流程的事件（觸發程式）](./functions-bindings-event-iot-trigger.md)
- [將事件寫入事件資料流程（輸出系結）](./functions-bindings-event-iot-output.md)
