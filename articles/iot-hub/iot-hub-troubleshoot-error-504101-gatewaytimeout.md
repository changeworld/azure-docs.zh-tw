---
title: 故障排除 Azure IoT 中心錯誤 504101 閘道超時
description: 瞭解如何修復錯誤 504101 閘道超時
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: b74a93e15d533bf9b15797e6371a25230f7a08f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960668"
---
# <a name="504101-gatewaytimeout"></a>504101 GatewayTimeout

本文介紹了**504101 閘道超時**錯誤的原因和解決方案。

## <a name="symptoms"></a>徵狀

當嘗試將直接從 IoT 中心調用方法到設備時，請求將失敗，錯誤**為 504101 閘道超時**。

## <a name="cause"></a>原因

### <a name="cause-1"></a>原因 1

IoT 中心遇到錯誤，無法確認直接方法在超時之前是否已完成。

### <a name="cause-2"></a>原因 2

使用早期版本的 Azure IoT C++ SDK（<1.19.0）時，由於存在 Bug，設備和 IoT 中心之間的 AMQP 連結可以靜默丟棄。

## <a name="solution"></a>解決方法

### <a name="solution-1"></a>解決方案 1

發出重試。

### <a name="solution-2"></a>解決方案 2

升級到最新版本的 Azure IOT C++ SDK。