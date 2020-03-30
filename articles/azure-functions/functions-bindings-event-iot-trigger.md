---
title: Azure IoT Azure 函數中心綁定
description: 瞭解如何回應發送到 Azure 函數中的 IoT 中心事件流的事件。
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: f63fe965b3f37add8ddf9d262f1ef1dae9fff966
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77589723"
---
# <a name="azure-iot-hub-trigger-for-azure-functions"></a>Azure IoT 中心觸發器用於 Azure 函數

本文介紹如何使用 IoT 中心的 Azure 函數綁定。 IoT 中心支援基於 Azure[事件中心綁定](functions-bindings-event-hubs.md)。

有關設置和配置詳細資訊的資訊，請參閱[概述](functions-bindings-event-iot.md)。

> [!IMPORTANT]
> 當以下代碼示例使用事件中心 API 時，給定的語法適用于 IoT 中心函數。

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs-trigger.md)]

## <a name="next-steps"></a>後續步驟

- [將事件寫入事件流（輸出綁定）](./functions-bindings-event-iot-output.md)
