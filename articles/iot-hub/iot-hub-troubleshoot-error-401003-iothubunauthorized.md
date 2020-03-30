---
title: 故障排除 Azure IoT 中心錯誤 401003 IoTHub 未經授權
description: 瞭解如何修復錯誤 401003 IoTHub 未經授權
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: d0a9f643516af5945037acb3dd1da24b06944171
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284404"
---
# <a name="401003-iothubunauthorized"></a>401003 IoTHubUnauthorized

本文介紹了**401003 IoTHub 未經授權**錯誤的原因和解決方案。

## <a name="symptoms"></a>徵狀

### <a name="symptom-1"></a>症狀 1

在診斷日誌中，您將看到設備與**401003 IoTHub 斷開連接**的模式，然後是**404104 設備連接關閉遠端**，然後不久後成功連接。

### <a name="symptom-2"></a>症狀 2

向 IoT 中心的請求失敗，出現以下錯誤訊息之一：

* 缺少授權標頭
* IotHub'\*' 不包含指定的設備 '\*
* 授權規則\*' 不允許訪問 '\*
* 此設備的身份驗證失敗、續訂權杖或證書並重新連接
* 指紋與配置不匹配：指紋：SHA1雜湊*，SHA2\*雜湊*;\*配置：主拇指列印\**\*

## <a name="cause"></a>原因

### <a name="cause-1"></a>原因 1

對於 MQTT，某些 SDK 依賴于 IoT 中心在 SAS 權杖過期時發出斷開連接，以便知道何時刷新它。 因此，若有下列情況的話， 

1. SAS 權杖過期
1. IoT 中心通知過期，並斷開設備與**401003 IoTHub 未經授權**
1. 設備通過**404104 設備連接遠端斷開**完成斷開連接
1. IoT SDK 生成新的 SAS 權杖
1. 設備成功與 IoT 中心重新連接

### <a name="cause-2"></a>原因 2

IoT 中心無法對身份驗證標頭、規則或金鑰進行身份驗證。

## <a name="solution"></a>解決方法

### <a name="solution-1"></a>解決方案 1

如果使用 IoT SDK 進行連接，則無需執行任何操作。 IoT SDK 重新生成新權杖，在 SAS 權杖過期時重新連接。 

如果問題涉及錯誤量，請切換到 C SDK，該 SDK 在過期前重新頒發 SAS 權杖。 此外，對於 AMQP，SAS 權杖可以在不斷開連接的情況下刷新。

### <a name="solution-2"></a>解決方案 2

通常，提供的錯誤訊息應解釋如何修復錯誤。 如果由於某種原因無法訪問錯誤訊息詳細資訊，請確保：

- 您使用的 SAS 或其他安全權杖未過期。 
- 授權憑據已針對您使用的協定格式良好。 要瞭解更多資訊，請參閱[IoT 中心存取控制](iot-hub-devguide-security.md)。
- 使用的授權規則具有請求的操作的許可權。

## <a name="next-steps"></a>後續步驟

為了更輕鬆地對 IoT 中心進行身份驗證，我們建議使用[Azure IoT SDK](iot-hub-devguide-sdks.md)。