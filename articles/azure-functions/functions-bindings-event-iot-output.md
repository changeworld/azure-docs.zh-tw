---
title: Azure IoT 中樞 Azure Functions 的輸出系結
description: 瞭解如何使用 Azure Functions 將訊息寫入 Azure IoT 中樞串流。
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: d4dbf43fb5684d829e581be29832e94ad46b2936
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91871774"
---
# <a name="azure-iot-hub-output-binding-for-azure-functions"></a>Azure IoT 中樞 Azure Functions 的輸出系結

本文說明如何使用適用于 IoT 中樞的 Azure Functions 輸出系結。 IoT 中樞支援是以 [Azure 事件中樞](functions-bindings-event-hubs.md)系結為基礎。

如需安裝和設定詳細資料的相關資訊，請參閱[概觀](functions-bindings-event-iot.md)。

> [!IMPORTANT]
> 下列程式碼範例使用事件中樞 API，而指定的語法適用于 IoT 中樞函式。

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs-output.md)]

## <a name="next-steps"></a>後續步驟

- [回應傳送至事件中樞事件資料流程的事件 (觸發程式) ](./functions-bindings-event-iot-trigger.md)
