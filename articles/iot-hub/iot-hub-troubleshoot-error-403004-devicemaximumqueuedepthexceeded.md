---
title: 疑難排解 Azure IoT 中樞錯誤 403004 DeviceMaximumQueueDepthExceeded
description: 瞭解如何修正錯誤 403004 DeviceMaximumQueueDepthExceeded
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: d48fd9aa9ba52c850a514d392f25b980d0219470
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960902"
---
# <a name="403004-devicemaximumqueuedepthexceeded"></a>403004 DeviceMaximumQueueDepthExceeded

本文說明**403004 DeviceMaximumQueueDepthExceeded**錯誤的原因和解決方案。

## <a name="symptoms"></a>徵狀

嘗試傳送雲端到裝置訊息時，要求失敗，錯誤為**403004**或**DeviceMaximumQueueDepthExceeded**。

## <a name="cause"></a>原因

根本原因是為裝置排入佇列的訊息數目超過[佇列限制（50）](./iot-hub-devguide-quotas-throttling.md#other-limits)。

您遇到此限制的最可能原因，是因為您使用 HTTPS 來接收訊息，這會導致使用 `ReceiveAsync`進行連續輪詢，因而導致 IoT 中樞對要求進行節流。

## <a name="solution"></a>解決方案

使用 HTTPS 時，針對雲端到裝置訊息支援的模式是裝置以間歇方式連接而不常檢查訊息 (低於每 25 分鐘一次)。 若要降低執行佇列限制的可能性，請針對雲端到裝置訊息，切換到 AMQP 或 MQTT。

或者，增強裝置端邏輯以快速完成、拒絕或放棄佇列的訊息、縮短存留時間，或考慮傳送較少的訊息。 請參閱 [C2D 訊息存留時間](./iot-hub-devguide-messages-c2d.md#message-expiration-time-to-live)。

最後，請考慮使用[清除佇列 API](https://docs.microsoft.com/rest/api/iothub/service/purgecommandqueue) ，在達到限制之前定期清除擱置中的訊息。