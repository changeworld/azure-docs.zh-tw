---
title: Azure Functions 的 Azure IoT 中樞系結
description: 瞭解如何在 Azure Functions 中回應傳送至 IoT 中樞事件資料流程的事件。
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: f63fe965b3f37add8ddf9d262f1ef1dae9fff966
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77589723"
---
# <a name="azure-iot-hub-trigger-for-azure-functions"></a>Azure Functions 的 Azure IoT 中樞觸發程式

本文說明如何使用 IoT 中樞的 Azure Functions 系結。 IoT 中樞支援是以[Azure 事件中樞](functions-bindings-event-hubs.md)系結為基礎。

如需安裝和設定詳細資料的相關資訊，請參閱[總覽](functions-bindings-event-iot.md)。

> [!IMPORTANT]
> 雖然下列程式碼範例會使用事件中樞 API，但指定的語法適用于 IoT 中樞函式。

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs-trigger.md)]

## <a name="next-steps"></a>後續步驟

- [將事件寫入事件資料流程（輸出系結）](./functions-bindings-event-iot-output.md)
