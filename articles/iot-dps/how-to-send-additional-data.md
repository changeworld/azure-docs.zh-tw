---
title: 如何在裝置與 Azure 裝置布建服務之間傳輸承載
description: '本檔說明如何在裝置與裝置布建服務 (DPS 之間傳輸承載) '
author: menchi
ms.author: menchi
ms.date: 02/11/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: d94bc2fde63090d66da7e98cc239386d958e6bb2
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94950959"
---
# <a name="how-to-transfer-a-payload-between-device-and-dps"></a>如何在裝置與 DPS 之間傳輸承載
DPS 有時需要來自裝置的更多資料，才能正確地將其布建到正確的 IoT 中樞，而且該資料必須由裝置提供。 相反地，DPS 可以將資料傳回給裝置，以協助用戶端邏輯。 

## <a name="when-to-use-it"></a>使用時機
這項功能可用來做為 [自訂配置](./how-to-use-custom-allocation-policies.md)的增強功能。 例如，您想要根據裝置型號來配置您的裝置，而不需要人為介入。 在此情況下，您將使用 [自訂配置](./how-to-use-custom-allocation-policies.md)。 您可以將裝置設定為 [註冊裝置呼叫](/rest/api/iot-dps/runtimeregistration/registerdevice)的一部分，以報告模型資訊。 DPS 會將裝置的承載傳遞至自訂配置 webhook。 而且您的函式可以決定此裝置收到裝置型號資訊時所要前往的 IoT 中樞。 同樣地，如果 webhook 希望將某些資料傳回給裝置，則會將資料回傳為 webhook 回應中的字串。  

## <a name="device-sends-data-payload-to-dps"></a>裝置將資料裝載傳送至 DPS
當您的裝置將登錄 [裝置呼叫](/rest/api/iot-dps/runtimeregistration/registerdevice) 傳送到 DPS 時，註冊呼叫可以增強以採用本文中的其他欄位。 主體看起來如下所示： 
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

## <a name="dps-returns-data-to-the-device"></a>DPS 將資料傳回到裝置
如果自訂配置原則 webhook 希望將某些資料傳回給裝置，則會將資料回傳為 webhook 回應中的字串。 這項變更是在下面的 [裝載] 區段中。 
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
這項功能適用于 C、c #、JAVA 和 Node.js [用戶端 sdk](./index.yml)。  

## <a name="next-steps"></a>後續步驟
* 針對 Azure IoT 中樞和 Azure IoT 中樞裝置佈建服務，使用 [Azure IoT SDK]( https://github.com/Azure/azure-iot-sdks) \(英文\) 進行開發