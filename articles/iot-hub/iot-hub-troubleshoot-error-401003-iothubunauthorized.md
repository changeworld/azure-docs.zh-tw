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
ms.openlocfilehash: af057750e81086bf691b87057da97af3de19cd3b
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2020
ms.locfileid: "92909636"
---
# <a name="401003-iothubunauthorized"></a>401003 IoTHubUnauthorized

本文說明 **401003 IoTHubUnauthorized** 錯誤的原因和解決方案。

## <a name="symptoms"></a>徵兆

### <a name="symptom-1"></a>徵兆1

在記錄中，您會看到裝置與 **401003 IoTHubUnauthorized** 中斷連線的模式，後面接著 **404104 DeviceConnectionClosedRemotely** ，然後在不久後立即成功連接。

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

IoT 中樞無法驗證驗證標頭、規則或金鑰。 這可能是因為徵兆中提及的任何原因所致。

## <a name="solution"></a>解決方法

### <a name="solution-1"></a>解決方案 1

使用 IoT SDK 進行連接時，不需要採取任何動作，而是使用裝置連接字串。 IoT SDK 會重新產生新的權杖，以在 SAS 權杖到期時重新連接。 

如果有問題的數量，請切換至 C SDK，以在到期前更新 SAS 權杖。 此外，對於 AMQP，SAS 權杖可以重新整理，而不會中斷連接。

### <a name="solution-2"></a>解決方案 2

一般情況下，顯示的錯誤訊息應該會說明如何修正錯誤。 如果基於某些原因而無法存取錯誤訊息詳細資料，請確定：

- 您使用的 SAS 或其他安全性權杖尚未過期。
- 針對 x.509 憑證驗證，裝置憑證或與裝置相關聯的 CA 憑證未過期。 若要瞭解如何向 IoT 中樞註冊 x.509 CA 憑證，請參閱 [在您的 Azure IoT 中樞中設定 x.509 安全性](iot-hub-security-x509-get-started.md)。
- 若為 x.509 憑證指紋驗證，則會向 IoT 中樞註冊裝置憑證的指紋。
- 授權認證的格式正確，適用于您所使用的通訊協定。 若要深入瞭解，請參閱 [控制 IoT 中樞的存取權](iot-hub-devguide-security.md)。
- 使用的授權規則具有所要求作業的許可權。

## <a name="next-steps"></a>下一步

- 若要更輕鬆地向 IoT 中樞進行驗證，我們建議使用 [Azure IoT sdk](iot-hub-devguide-sdks.md)。
- 如需有關使用 IoT 中樞進行驗證的詳細資訊，請參閱 [控制 Iot 中樞的存取權](iot-hub-devguide-security.md)。
