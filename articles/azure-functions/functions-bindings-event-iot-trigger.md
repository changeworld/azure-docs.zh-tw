---
title: Azure Functions 的 Azure IoT 中樞觸發程式
description: 瞭解如何在 Azure Functions 中回應傳送至 IoT 中樞事件資料流程的事件。
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 25396da3fb9a4293633308bf2e9d3c6b3d07265d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87041644"
---
# <a name="azure-iot-hub-trigger-for-azure-functions"></a>Azure Functions 的 Azure IoT 中樞觸發程式

本文說明如何使用 IoT 中樞的 Azure Functions 系結。 IoT 中樞支援是以[Azure 事件中樞](functions-bindings-event-hubs.md)系結為基礎。

如需安裝和設定詳細資料的相關資訊，請參閱[概觀](functions-bindings-event-iot.md)。

> [!IMPORTANT]
> 雖然下列程式碼範例會使用事件中樞 API，但指定的語法適用于 IoT 中樞函式。

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs-trigger.md)]

## <a name="next-steps"></a>接下來的步驟

- [將事件寫入事件資料流程（輸出系結）](./functions-bindings-event-iot-output.md)
