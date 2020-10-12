---
title: 疑難排解 Azure IoT 中樞錯誤 401003 IoTHubUnauthorized
description: 瞭解如何修正錯誤 401003 IoTHubUnauthorized
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
ms.openlocfilehash: f46d41c8287d03cbe9582ed560244cbd85cdeeaa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "81759589"
---
# <a name="401003-iothubunauthorized"></a>401003 IoTHubUnauthorized

本文說明 **401003 IoTHubUnauthorized** 錯誤的原因和解決方案。

## <a name="symptoms"></a>徵兆

### <a name="symptom-1"></a>徵兆1

在診斷記錄中，您會看到裝置與 **401003 IoTHubUnauthorized**中斷連線的模式，後面接著 **404104 DeviceConnectionClosedRemotely**，然後在不久之後成功連接。

### <a name="symptom-2"></a>徵兆2

IoT 中樞的要求失敗，並出現下列其中一個錯誤訊息：

* 缺少授權標頭
* IotHub ' \* ' 不包含指定的裝置 ' \* '
* 授權規則 ' ' 不 \* 允許存取 ' \* '
* 此裝置的驗證失敗，更新權杖或憑證並重新連線
* 指紋不符合設定：指紋： SHA1Hash = \* ，SHA2Hash = \* ;Configuration： PrimaryThumbprint = \* ，SecondaryThumbprint =\*

## <a name="cause"></a>原因

### <a name="cause-1"></a>原因 1

針對 MQTT，某些 Sdk 會依賴 IoT 中樞在 SAS 權杖到期時發出中斷連線，以得知何時重新整理。 所以， 

1. SAS 權杖到期
1. IoT 中樞會通知到期，並中斷裝置與401003的連線 **IoTHubUnauthorized**
1. 裝置已完成與404104的連線 **DeviceConnectionClosedRemotely**
1. IoT SDK 會產生新的 SAS 權杖
1. 裝置已成功與 IoT 中樞重新連接

### <a name="cause-2"></a>原因 2

IoT 中樞無法驗證驗證標頭、規則或金鑰。

## <a name="solution"></a>解決方法

### <a name="solution-1"></a>解決方案 1

使用 IoT SDK 進行連接時，不需要採取任何動作，而是使用裝置連接字串。 IoT SDK 會重新產生新的權杖，以在 SAS 權杖到期時重新連接。 

如果有問題的數量，請切換至 C SDK，以在到期前更新 SAS 權杖。 此外，對於 AMQP，SAS 權杖可以重新整理，而不會中斷連接。

### <a name="solution-2"></a>解決方案 2

一般情況下，顯示的錯誤訊息應該會說明如何修正錯誤。 如果基於某些原因而無法存取錯誤訊息詳細資料，請確定：

- 您使用的 SAS 或其他安全性權杖尚未過期。 
- 授權認證的格式正確，適用于您所使用的通訊協定。 若要深入瞭解，請參閱 [IoT 中樞存取控制](iot-hub-devguide-security.md)。
- 使用的授權規則具有所要求作業的許可權。

## <a name="next-steps"></a>接下來的步驟

若要更輕鬆地向 IoT 中樞進行驗證，我們建議使用 [Azure IoT sdk](iot-hub-devguide-sdks.md)。