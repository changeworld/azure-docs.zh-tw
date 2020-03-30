---
title: 如何在設備和 Azure 設備佈建服務之間傳輸有效負載
description: 本文檔介紹如何在設備和設備佈建服務 （DPS） 之間傳輸有效負載
author: menchi
ms.author: menchi
ms.date: 02/11/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: de6bb3fe0879a69467283e93a04a355876a02cba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246682"
---
# <a name="how-to-transfer-a-payload-between-device-and-dps"></a>如何在設備和 DPS 之間傳輸有效負載
有時，DPS 需要來自設備的更多資料來將它們正確預配到正確的 IoT 中心，並且資料需要由設備提供。 反之亦然，DPS 可以將資料返回設備以方便用戶端邏輯。 

## <a name="when-to-use-it"></a>使用時機
此功能可用作[自訂分配的](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies)增強功能。 例如，您希望根據設備模型分配設備，而無需人工干預。 在這種情況下，您將使用[自訂分配](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies)。 您可以將設備配置為將模型資訊報告為[寄存器設備調用](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice)的一部分。 DPS 會將設備的有效負載傳遞到自訂分配 Webhook。 當設備接收設備模型資訊時，您的功能可以決定此設備將轉到哪個 IoT 中心。 同樣，如果 Webhook 希望將某些資料返回到設備，它將將資料作為 Webhook 回應中的字串傳遞回。  

## <a name="device-sends-data-payload-to-dps"></a>設備將資料有效負載發送到 DPS
當您的設備向 DPS 發送[寄存器設備呼叫](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice)時，可以增強寄存器調用以獲取正文中的其他欄位。 正文如下所示： 
   ```
   { 
       “registrationId”: “mydevice”, 
       “tpm”:                
       { 
           “endorsementKey”: “stuff”, 
           “storageRootKey”: “things” 
       }, 
       “payload”: “your additional data goes here. It can be nested JSON.” 
    } 
   ```

## <a name="dps-returns-data-to-the-device"></a>DPS 將資料返回給設備
如果自訂分配策略 Webhook 希望將某些資料返回到設備，它將將資料作為 webhook 回應中的字串傳遞回。 更改在下面的有效負載部分中。 
   ```
   { 
       "iotHubHostName": "sample-iot-hub-1.azure-devices.net", 
       "initialTwin": { 
           "tags": { 
               "tag1": true 
               }, 
               "properties": { 
                   "desired": { 
                       "tag2": true 
                    } 
                } 
            }, 
        "payload": "whatever is returned by the webhook" 
    } 
   ```

## <a name="sdk-support"></a>SDK 支援
此功能在 C、C#、JAVA 和 Node.js[用戶端 SDK 中可用](https://docs.microsoft.com/azure/iot-dps/)。  

## <a name="next-steps"></a>後續步驟
* 針對 Azure IoT 中樞和 Azure IoT 中樞裝置佈建服務，使用 [Azure IoT SDK]( https://github.com/Azure/azure-iot-sdks) \(英文\) 進行開發
