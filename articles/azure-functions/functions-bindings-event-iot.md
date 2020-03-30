---
title: Azure IoT Azure 函數中心綁定
description: 瞭解如何在 Azure 函數中使用 IoT 中心觸發器和綁定。
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 1c25543b16c3486a8f6a445427346382faaaa09a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586119"
---
# <a name="azure-iot-hub-bindings-for-azure-functions"></a>Azure IoT Azure 函數中心綁定

這組文章介紹了如何使用 IoT 中心的 Azure 函數綁定。 IoT 中心支援基於 Azure[事件中心綁定](functions-bindings-event-hubs.md)。

> [!IMPORTANT]
> 當以下代碼示例使用事件中心 API 時，給定的語法適用于 IoT 中心函數。

| 動作 | 類型 |
|--------|------|
| 回應發送到 IoT 中心事件流的事件。 | [觸發](./functions-bindings-event-iot-trigger.md) |
| 將事件寫入 IoT 事件流 | [輸出綁定](./functions-bindings-event-iot-output.md) |

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs.md)]

## <a name="next-steps"></a>後續步驟

- [回應發送到事件中心事件流的事件（觸發器）](./functions-bindings-event-iot-trigger.md)
- [將事件寫入事件流（輸出綁定）](./functions-bindings-event-iot-output.md)
