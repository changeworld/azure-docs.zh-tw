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
ms.openlocfilehash: d0a9f643516af5945037acb3dd1da24b06944171
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960629"
---
# <a name="401003-iothubunauthorized"></a>401003 IoTHubUnauthorized

本文說明**401003 IoTHubUnauthorized**錯誤的原因和解決方案。

## <a name="symptoms"></a>徵狀

### <a name="symptom-1"></a>徵兆1

在 [診斷記錄] 中，您會看到裝置與**401003 IoTHubUnauthorized**中斷連線的模式，後面接著**404104 DeviceConnectionClosedRemotely**，然後很快就會成功連接。

### <a name="symptom-2"></a>徵兆2

IoT 中樞的要求失敗，並出現下列其中一個錯誤訊息：

* 遺漏授權標頭
* IotHub '\*' 未包含指定的裝置 '\*'
* 授權規則 '\*' 不允許 '\*' 的存取
* 此裝置的驗證失敗，請更新權杖或憑證，然後重新連線
* 指紋不符合設定：指紋： SHA1Hash =\*，SHA2Hash =\*;Configuration： PrimaryThumbprint =\*，SecondaryThumbprint =\*

## <a name="cause"></a>原因

### <a name="cause-1"></a>原因 1

針對 MQTT，有些 Sdk 會依賴 IoT 中樞在 SAS 權杖到期時發出中斷連線，以知道何時重新整理。 因此 

1. SAS 權杖過期
1. IoT 中樞注意到到期日，並中斷裝置與**401003 IoTHubUnauthorized**的連線
1. 裝置已完成與**404104 DeviceConnectionClosedRemotely**的中斷連線
1. IoT SDK 會產生新的 SAS 權杖
1. 裝置已成功與 IoT 中樞重新連接

### <a name="cause-2"></a>原因 2

IoT 中樞無法驗證驗證標頭、規則或金鑰。

## <a name="solution"></a>解決方案

### <a name="solution-1"></a>解決方案 1

如果使用 IoT SDK 連接到使用裝置連接字串的連線，則不需要採取任何動作。 IoT SDK 會重新產生新的權杖，以在 SAS 權杖到期時重新連線。 

如果錯誤的數量是問題，請切換到 C SDK，這會在到期前更新 SAS 權杖。 此外，對於 AMQP，SAS 權杖可以在不中斷連線的情況下重新整理。

### <a name="solution-2"></a>解決方案 2

一般來說，出現的錯誤訊息應該會說明如何修正錯誤。 如果基於某些原因而無法存取錯誤訊息詳細資料，請確定：

- 您使用的 SAS 或其他安全性權杖未過期。 
- 授權認證的格式正確，適用于您所使用的通訊協定。 若要深入瞭解，請參閱[IoT 中樞存取控制](iot-hub-devguide-security.md)。
- 使用的授權規則具有所要求之作業的許可權。

## <a name="next-steps"></a>後續步驟

若要讓 IoT 中樞更輕鬆地進行驗證，建議使用[Azure IoT sdk](iot-hub-devguide-sdks.md)。