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
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: fc5029f26e5d615502925c4def4e2973c118f38d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90029982"
---
# <a name="403004-devicemaximumqueuedepthexceeded"></a>403004 DeviceMaximumQueueDepthExceeded

本文說明 **403004 DeviceMaximumQueueDepthExceeded** 錯誤的原因和解決方案。

## <a name="symptoms"></a>徵兆

當嘗試傳送雲端到裝置的訊息時，要求會失敗，並出現錯誤 **403004** 或 **DeviceMaximumQueueDepthExceeded**。

## <a name="cause"></a>原因

根本原因是針對裝置排入佇列的訊息數目超過 [佇列限制 (50) ](./iot-hub-devguide-quotas-throttling.md#other-limits)。

您遇到這項限制的最可能原因是因為您使用 HTTPS 來接收訊息（這會導致使用持續輪詢 `ReceiveAsync` ），而導致 IoT 中樞節流要求。

## <a name="solution"></a>解決方法

使用 HTTPS 時，針對雲端到裝置訊息支援的模式是裝置以間歇方式連接而不常檢查訊息 (低於每 25 分鐘一次)。 若要降低進入佇列限制的可能性，請切換至 AMQP 或 MQTT，以取得雲端到裝置的訊息。

或者，您也可以增強裝置端邏輯，以快速完成、拒絕或放棄佇列的訊息、縮短存留時間，或考慮傳送較少的訊息。 請參閱 [C2D 訊息存留時間](./iot-hub-devguide-messages-c2d.md#message-expiration-time-to-live)。

最後，請考慮使用 [清除佇列 API](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-registrymanager-h/iothubregistrymanager-deletedevice) ，在達到限制之前定期清除擱置中的訊息。