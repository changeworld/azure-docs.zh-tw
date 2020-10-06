---
title: " (DPS) TLS 支援的 Azure IoT 裝置布建服務"
description: '針對與 IoT 裝置布建服務進行通訊的裝置和服務，使用安全 TLS 連線的最佳作法 (DPS) '
services: iot-dps
author: wesmc7777
ms.service: iot-dps
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: wesmc
ms.openlocfilehash: 2adda858e6d39d41de49fa843d1ba226b300f8e7
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "91743534"
---
# <a name="tls-support-in-azure-iot-hub-device-provisioning-service-dps"></a>Azure IoT 中樞裝置布建服務 (DPS) 的 TLS 支援

DPS 會使用 [傳輸層安全性 (TLS) ](http://wikipedia.org/wiki/Transport_Layer_Security) 來保護來自 IoT 裝置的連線。 

DPS 目前支援的 TLS 通訊協定版本如下： 
* TLS 1.2

TLS 1.0 和 1.1 被視為舊版，並已規劃淘汰。 如需詳細資訊，請參閱 [IoT 中樞即將淘汰 TLS 1.0 和 1.1](../iot-hub/iot-hub-tls-deprecating-1-0-and-1-1.md)。 

## <a name="restrict-connections-to-tls-12"></a>限制連接至 TLS 1。2

為了增加安全性，建議您將 DPS 實例設定為 *只* 允許使用 TLS 1.2 版的裝置用戶端連線，並強制使用 [建議的密碼](#recommended-ciphers)。

若要這樣做，請 `minTlsVersion` `1.2` 在 Azure Resource Manager 範本的 dps 資源規格中，將屬性設定為的新 dps 資源。 下列範例範本 JSON 會指定 `minTlsVersion` 新 DPS 實例的屬性。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Devices/ProvisioningServices",
            "apiVersion": "2020-01-01",
            "name": "<provide-a-valid-DPS-resource-name>",
            "location": "<any-region>",
            "properties": {
                "minTlsVersion": "1.2"
            },
            "sku": {
                "name": "S1",
                "capacity": 1
            },
        }     
    ]
}
```

您可以使用下列 Azure CLI 命令來部署範本。 

```azurecli
az deployment group create -g <your resource group name> --template-file template.json
```

如需使用 Resource Manager 範本建立 DPS 資源的詳細資訊，請參閱、 [使用 Azure Resource Manager 範本設定 dps](quick-setup-auto-provision-rm.md)。

使用此設定建立的 DPS 資源將拒絕嘗試使用 TLS 1.0 和1.1 連接的裝置。 同樣地，如果裝置用戶端的 HELLO 訊息未列出任何 [建議的密碼](#recommended-ciphers)，TLS 信號交換將會遭到拒絕。

> [!NOTE]
> `minTlsVersion`屬性是唯讀的，而且在建立 DPS 資源之後，就無法變更。 因此，您必須適當地測試並驗證 *所有* 的 IoT 裝置都能與 TLS 1.2 相容，並預先 [建議加密密碼](#recommended-ciphers) 。


> [!NOTE]
> 在 `minTlsVersion` 容錯移轉之後，您 DPS 的屬性將會在容錯移轉後的地理配對區域中保持有效。

## <a name="recommended-ciphers"></a>建議的加密

設定為僅接受 TLS 1.2 的 DPS 實例也會強制使用下列加密套件：


| TLS 1.2 加密套件 |
| :--- |
| `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`<br>`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`<br>`TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`<br>`TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256` |


### <a name="legacy-cipher-suites"></a>舊版加密套件 

DPS 目前仍支援這些加密套件，但是將會進行折舊。 如果可能的話，請使用上述建議的加密套件。

| 選項 #1 (更佳的安全性)  |
| :--- |
| `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P384   (uses SHA-1)`<br>`TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256   (uses SHA-1)`<br>`TLS_RSA_WITH_AES_256_GCM_SHA384           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_128_GCM_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_256_CBC_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_128_CBC_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_256_CBC_SHA              (uses SHA-1, lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_128_CBC_SHA              (uses SHA-1, lack of Perfect Forward Secrecy)` |

| 選項 #2 (更佳的效能)  |
| :--- |
| `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256   (uses SHA-1)`<br>`TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P384   (uses SHA-1)`<br>`TLS_RSA_WITH_AES_128_GCM_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_256_GCM_SHA384           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_128_CBC_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_256_CBC_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_128_CBC_SHA              (uses SHA-1, lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_256_CBC_SHA              (uses SHA-1, lack of Perfect Forward Secrecy)` |


## <a name="use-tls-12-in-the-iot-sdks"></a>在 IoT Sdk 中使用 TLS 1。2

使用下列連結，在 Azure IoT 用戶端 Sdk 中設定 TLS 1.2 和允許的密碼。

| Language | 支援 TLS 1.2 的版本 | 文件 |
|----------|------------------------------------|---------------|
| C        | 標記 2019-12-11 或更新版本            | [連結](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | 2\.0.0 版或更新版本             | [連結](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | 1\.21.4 版或更新版本            | [連結](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | 1\.19.0 版或更新版本            | [連結](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | 1\.12.2 版或更新版本            | [連結](https://aka.ms/Tls_Node_SDK_IoT) |

## <a name="use-tls-12-with-iot-hub"></a>搭配使用 TLS 1.2 與 IoT 中樞

IoT 中樞可以設定為在與裝置通訊時使用 TLS 1.2。 如需詳細資訊，請參閱 [IoT 中樞即將淘汰 TLS 1.0 和 1.1](../iot-hub/iot-hub-tls-deprecating-1-0-and-1-1.md)。

## <a name="use-tls-12-with-iot-edge"></a>搭配 IoT Edge 使用 TLS 1。2

IoT Edge 裝置可設定為在與 IoT 中樞和 DPS 通訊時使用 TLS 1.2。 如需詳細資訊，請參閱 [IoT Edge 檔頁面](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md)。
