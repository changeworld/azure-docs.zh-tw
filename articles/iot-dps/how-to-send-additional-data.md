---
title: 如何在裝置與 Azure 裝置布建服務之間傳輸承載
description: 本檔說明如何在裝置和裝置布建服務（DPS）之間傳輸承載
author: menchi
ms.author: menchi
ms.date: 02/11/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: de6bb3fe0879a69467283e93a04a355876a02cba
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80246682"
---
# <a name="how-to-transfer-a-payload-between-device-and-dps"></a>如何在裝置與 DPS 之間傳輸承載
有時候 DPS 需要裝置的更多資料，才能正確地將其布建到正確的 IoT 中樞，且該資料必須由裝置提供。 相反地，DPS 可以將資料傳回給裝置，以協助用戶端邏輯。 

## <a name="when-to-use-it"></a>使用時機
這項功能可用來做為[自訂配置](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies)的增強功能。 例如，您想要在沒有人為介入的情況下，根據裝置型號來配置您的裝置。 在此情況下，您將使用[自訂配置](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies)。 您可以設定裝置，在[註冊裝置呼叫](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice)中報告模型資訊。 DPS 會將裝置的承載傳遞至自訂配置 webhook。 而您的函式可以決定當此裝置收到裝置型號資訊時，會前往哪一個 IoT 中樞。 同樣地，如果 webhook 希望將一些資料傳回給裝置，它會將資料以字串形式傳入 webhook 回應中。  

## <a name="device-sends-data-payload-to-dps"></a>裝置將資料承載傳送至 DPS
當您的裝置將[註冊裝置呼叫](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice)傳送至 DPS 時，可以增強註冊呼叫以接受本文中的其他欄位。 本文看起來如下所示： 
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
如果自訂配置原則 webhook 想要將某些資料傳回給裝置，它會將資料以字串形式傳入 webhook 回應中。 這項變更是在以下的裝載區段中。 
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
這項功能適用于 C、c #、JAVA 和 node.js[用戶端 sdk](https://docs.microsoft.com/azure/iot-dps/)。  

## <a name="next-steps"></a>後續步驟
* 針對 Azure IoT 中樞和 Azure IoT 中樞裝置佈建服務，使用 [Azure IoT SDK]( https://github.com/Azure/azure-iot-sdks) \(英文\) 進行開發
