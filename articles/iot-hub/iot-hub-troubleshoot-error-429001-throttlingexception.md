---
title: 故障排除 Azure IoT 中心錯誤 429001 限制異常
description: 瞭解如何修復錯誤 429001 限制異常
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 3095e398d7e5cfe59085144d5bb4e8dc33618064
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960694"
---
# <a name="429001-throttlingexception"></a>429001 ThrottlingException

本文介紹了**429001 限制異常**錯誤的原因和解決方案。

## <a name="symptoms"></a>徵狀

您對 IoT 中心的請求失敗，錯誤**429001 限制異常**。

## <a name="cause"></a>原因

已超出請求操作的 IoT 中心[限制限制](./iot-hub-devguide-quotas-throttling.md)。

## <a name="solution"></a>解決方法

通過將*遙測消息發送嘗試*指標與上述限制進行比較，檢查您是否達到限制限制。 您還可以檢查*限制錯誤數*指標。 有關這些指標和其他可用於 IoT 中心指標的詳細資訊，請參閱[IoT 中心指標以及如何使用它們](./iot-hub-metrics.md#iot-hub-metrics-and-how-to-use-them)。

IoT 中心僅在限制被違反時間過長後返回 429 限制異常。 這樣做是為了在 IoT 中心獲得突發流量時不會丟棄消息。 在此同時，IoT 中樞會以作業節流速率處理訊息，如果待處理項目中有太多流量，該速率可能會很慢。 若要深入了解，請參閱 [IoT 中樞流量成形](./iot-hub-devguide-quotas-throttling.md#traffic-shaping)。

## <a name="next-steps"></a>後續步驟

如果遇到配額限制或限制限制，請考慮[向上擴展 IoT 中心](./iot-hub-scaling.md)。