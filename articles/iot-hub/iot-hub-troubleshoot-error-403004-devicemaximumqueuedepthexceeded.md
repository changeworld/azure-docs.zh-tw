---
title: 容損毀 Azure IoT 中心錯誤 403004 裝置最大佇列深度超過
description: 瞭解如何修復錯誤 403004 設備最大佇列深度超過
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
ms.openlocfilehash: 5cc8bae0f0245f5c4b45ca0cd446582b04788c21
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758763"
---
# <a name="403004-devicemaximumqueuedepthexceeded"></a>403004 DeviceMaximumQueueDepthExceeded

本文介紹了**403004 設備最大佇列深度超過**錯誤的原因和解決方案。

## <a name="symptoms"></a>徵狀

嘗試傳送雲到裝置訊息時,請求失敗,錯誤**為 403004**或**裝置最大佇列深度超過**。

## <a name="cause"></a>原因

根本原因是設備排隊的消息數超過[佇列限制 (50)。](./iot-hub-devguide-quotas-throttling.md#other-limits)

您遇到此限制的最可能原因是,您使用 HHS 接收消息,從而導致使用連續`ReceiveAsync`輪詢, 從而導致 IoT Hub 限制請求。

## <a name="solution"></a>解決方法

使用 HTTPS 時，針對雲端到裝置訊息支援的模式是裝置以間歇方式連接而不常檢查訊息 (低於每 25 分鐘一次)。 要降低運行到佇列限制的可能性,請切換到 AMQP 或 MQTT 進行雲到設備的消息。

或者,增強設備端邏輯以快速完成、拒絕或放棄排隊消息、縮短存留時間或考慮發送更少的消息。 請參閱 [C2D 訊息存留時間](./iot-hub-devguide-messages-c2d.md#message-expiration-time-to-live)。

最後,請考慮使用[清除佇列 API](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/purgecommandqueue)在達到限制之前定期清理掛起的消息。