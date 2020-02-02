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
ms.openlocfilehash: b74a93e15d533bf9b15797e6371a25230f7a08f7
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960668"
---
# <a name="504101-gatewaytimeout"></a>504101 GatewayTimeout

本文說明**504101 GatewayTimeout**錯誤的原因和解決方案。

## <a name="symptoms"></a>徵狀

嘗試叫用從 IoT 中樞到裝置的直接方法時，要求會失敗，並出現錯誤**504101 GatewayTimeout**。

## <a name="cause"></a>原因

### <a name="cause-1"></a>原因 1

IoT 中樞發生錯誤，且無法確認直接方法是否在超時前完成。

### <a name="cause-2"></a>原因 2

使用舊版的 Azure IoT C# SDK （< 1.19.0）時，裝置和 IoT 中樞之間的 AMQP 連結可以透過無訊息方式卸載，因為有錯誤。

## <a name="solution"></a>解決方案

### <a name="solution-1"></a>解決方案 1

發出重試。

### <a name="solution-2"></a>解決方案 2

升級至最新版的 Azure IOT C# SDK。