---
title: Azure Functions 的 Azure IoT 中樞觸發程式
description: 瞭解如何在 Azure Functions 中回應傳送至 IoT 中樞事件資料流程的事件。
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 25396da3fb9a4293633308bf2e9d3c6b3d07265d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87041644"
---
# <a name="azure-iot-hub-trigger-for-azure-functions"></a>Azure Functions 的 Azure IoT 中樞觸發程式

本文說明如何使用適用于 IoT 中樞的 Azure Functions 系結。 IoT 中樞支援是以 [Azure 事件中樞](functions-bindings-event-hubs.md)系結為基礎。

如需安裝和設定詳細資料的相關資訊，請參閱[概觀](functions-bindings-event-iot.md)。

> [!IMPORTANT]
> 下列程式碼範例使用事件中樞 API，而指定的語法適用于 IoT 中樞函式。

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs-trigger.md)]

## <a name="next-steps"></a>接下來的步驟

- [ (輸出系結將事件寫入事件資料流程) ](./functions-bindings-event-iot-output.md)
