---
title: 疑難排解 Azure IoT 中樞錯誤 504101 GatewayTimeout
description: 瞭解如何修正錯誤 504101 GatewayTimeout
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom: amqp
ms.openlocfilehash: 373acc30ed652a7f540e840dfad5eeeda65ca179
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "81759566"
---
# <a name="504101-gatewaytimeout"></a>504101 GatewayTimeout

本文說明 **504101 GatewayTimeout** 錯誤的原因和解決方案。

## <a name="symptoms"></a>徵兆

嘗試從 IoT 中樞對裝置叫用直接方法時，要求會失敗，並出現錯誤 **504101 GatewayTimeout**。

## <a name="cause"></a>原因

### <a name="cause-1"></a>原因 1

IoT 中樞發生錯誤，無法確認直接方法是否在超時前完成。

### <a name="cause-2"></a>原因 2

使用舊版的 Azure IoT c # SDK ( # B0 1.19.0) 時，裝置與 IoT 中樞之間的 AMQP 連結可以無訊息地卸載，因為發生錯誤。

## <a name="solution"></a>解決方法

### <a name="solution-1"></a>解決方案 1

發出重試。

### <a name="solution-2"></a>解決方案 2

升級至最新版本的 Azure IOT c # SDK。