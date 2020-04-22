---
title: 容毀毀 Azure IoT 中心錯誤 504101 閘道超時
description: 瞭解如何修復錯誤 504101 閘道超時
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom: amqp
ms.openlocfilehash: 373acc30ed652a7f540e840dfad5eeeda65ca179
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759566"
---
# <a name="504101-gatewaytimeout"></a>504101 GatewayTimeout

本文介紹了**504101 閘道超時**錯誤的原因和解決方案。

## <a name="symptoms"></a>徵狀

當嘗試將直接從 IoT 中心呼叫方法到裝置時,要求將失敗,錯誤**為 504101 閘道超時**。

## <a name="cause"></a>原因

### <a name="cause-1"></a>原因 1

IoT 中心遇到錯誤,無法確認直接方法在超時之前是否已完成。

### <a name="cause-2"></a>原因 2

使用早期版本的 Azure IoT C++ SDK(<1.19.0)時,由於存在 Bug,設備和 IoT 中心之間的 AMQP 連結可以靜默丟棄。

## <a name="solution"></a>解決方法

### <a name="solution-1"></a>解決方案 1

發出重試。

### <a name="solution-2"></a>解決方案 2

升級到最新版本的 Azure IOT C++ SDK。