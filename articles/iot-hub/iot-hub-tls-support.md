---
title: Azure IoT 中樞的 TLS 支援
description: 使用安全 TLS 連線讓裝置和服務與 IoT 中樞通訊的最佳做法
services: iot-hub
author: rezasherafat
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: rezas
ms.openlocfilehash: 5b9f6b993b0d0f527d041b4ee055bf51fefa1253
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/26/2020
ms.locfileid: "83848240"
---
# <a name="tls-support-in-iot-hub"></a>IoT 中樞的 TLS 支援

IoT 中樞使用傳輸層安全性 (TLS) 來保護 IoT 裝置和服務的連線。 目前支援三種 TLS 通訊協定版本，亦即 1.0、1.1 和 1.2 版。

TLS 1.0 和 1.1 被視為舊版，並已規劃淘汰。 如需詳細資訊，請參閱 [IoT 中樞即將淘汰 TLS 1.0 和 1.1](iot-hub-tls-deprecating-1-0-and-1-1.md)。 強烈建議在連線到 IoT 中樞時，使用 TLS 1.2 作為慣用的 TLS 版本。

## <a name="restrict-connections-to-tls-12-in-your-iot-hub-resource"></a>將 IoT 中樞資源的連線限制為 TLS 1.2

為提高安全性，建議將 IoT 中樞設定為「只」允許使用 TLS 1.2 版的用戶端連線，並強制使用[建議的加密](#recommended-ciphers)。

基於此目的，請在任何[支援的區域](#supported-regions)中佈建新的 IoT 中樞，並在 Azure Resource Manager 範本的 IoT 中樞資源規格中，將 `minTlsVersion` 屬性設定為 `1.2`：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Devices/IotHubs",
            "apiVersion": "2020-01-01",
            "name": "<provide-a-valid-resource-name>",
            "location": "<any-of-supported-regions-below>",
            "properties": {
                "minTlsVersion": "1.2"
            },
            "sku": {
                "name": "<your-hubs-SKU-name>",
                "tier": "<your-hubs-SKU-tier>",
                "capacity": 1
            }
        }
    ]
}
```

使用此設定建立的 IoT 中樞資源將會拒絕嘗試使用 TLS 1.0 和 1.1 版連線的裝置和服務用戶端。 同樣地，如果用戶端 HELLO 訊息未列出任何[建議的加密](#recommended-ciphers)，也會拒絕 TLS 交握。

> [!NOTE]
> `minTlsVersion` 屬性是唯讀的，且無法在建立 IoT 中樞資源之後變更。 因此，您必須事先適當地測試並驗證「所有」IoT 裝置和服務都符合 TLS 1.2 和[建議的加密](#recommended-ciphers)規範。

## <a name="supported-regions"></a>支援區域

您可在下列區域建立需要使用 TLS 1.2 的 IoT 中樞：

* 美國東部
* 美國中南部
* 美國西部 2
* US Gov 亞利桑那州
* US Gov 維吉尼亞州

> [!NOTE]
> 容錯移轉之後，您 IoT 中樞的 `minTlsVersion` 屬性會在容錯移轉後的異地配對區域中維持有效。

## <a name="recommended-ciphers"></a>建議的加密

設定為只接受 TLS 1.2 的 IoT 中樞也會強制使用下列所建議加密：

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`

針對未設定強制執行 TLS 1.2 的 IoT 中樞，TLS 1.2 仍然可使用下列加密：

* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_DHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_DHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_RSA_WITH_AES_256_CBC_SHA256`
* `TLS_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_AES_256_CBC_SHA`
* `TLS_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_3DES_EDE_CBC_SHA`

## <a name="use-tls-12-in-your-iot-hub-sdks"></a>在 IoT 中樞 SDK 中使用 TLS 1.2

使用下列連結，在 IoT 中樞用戶端 SDK 中設定 TLS 1.2 和允許的加密。

| Language | 支援 TLS 1.2 的版本 | 文件 |
|----------|------------------------------------|---------------|
| C        | 標記 2019-12-11 或更新版本            | [連結](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | 2\.0.0 版或更新版本             | [連結](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | 1\.21.4 版或更新版本            | [連結](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | 1\.19.0 版或更新版本            | [連結](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | 1\.12.2 版或更新版本            | [連結](https://aka.ms/Tls_Node_SDK_IoT) |


## <a name="use-tls-12-in-your-iot-edge-setup"></a>在 IoT Edge 設定中使用 TLS 1.2

您可將 IoT Edge 裝置設定為使用 TLS 1.2 來與 IoT 中樞通訊。 基於此目的，請使用 [IoT Edge 文件頁面](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md)。