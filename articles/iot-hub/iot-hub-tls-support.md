---
title: Azure IoT 中樞 TLS 支援
description: 使用安全 TLS 連線進行裝置和服務與 IoT 中樞通訊的最佳做法
services: iot-hub
author: rezasherafat
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: rezas
ms.openlocfilehash: ce8ef987adc0cec4fcd8acef4cc075d50c92d62a
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722885"
---
# <a name="tls-support-in-iot-hub"></a>IoT 中樞中的 TLS 支援

IoT 中樞使用傳輸層安全性（TLS）來保護來自 IoT 裝置和服務的連線。 目前支援三種 TLS 通訊協定版本，亦即版本1.0、1.1 和1.2。

TLS 1.0 和1.1 被視為舊版，並已規劃要淘汰。 如需詳細資訊，請參閱[淘汰 TLS 1.0 和 1.1 for IoT 中樞](iot-hub-tls-deprecating-1-0-and-1-1.md)。 強烈建議您在連接到 IoT 中樞時，使用 TLS 1.2 做為慣用的 TLS 版本。

## <a name="restrict-connections-to-tls-12-in-your-iot-hub-resource"></a>限制 IoT 中樞資源中的 TLS 1.2 連線

為了增加安全性，建議您將 IoT 中樞設定為*只*允許使用 TLS 1.2 版的用戶端連線，並強制使用[建議的密碼](#recommended-ciphers)。

基於此目的，請在任何[支援的區域](#supported-regions)中布建新的 IoT 中樞，並將 `minTlsVersion` 屬性設定為 Azure Resource Manager 範本的 IoT 中樞資源規格中的 `1.2`：

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

使用此設定建立的 IoT 中樞資源將會拒絕嘗試使用 TLS 版本1.0 和1.1 連線的裝置和服務用戶端。 同樣地，如果用戶端 HELLO 訊息不會列出任何[建議](#recommended-ciphers)的加密，TLS 交握也會遭到拒絕。

> [!NOTE]
> `minTlsVersion` 屬性是唯讀的，一旦建立 IoT 中樞資源之後就無法變更。 因此，您必須適當地測試並驗證您的*所有*IoT 裝置和服務都與 TLS 1.2 相容，以及預先[建議的密碼](#recommended-ciphers)。

### <a name="supported-regions"></a>支援區域

需要使用 TLS 1.2 的 IoT 中樞可以在下欄區域中建立：

* 美國東部
* 美國中南部
* 美國西部 2

> [!NOTE]
> 容錯移轉之後，您 IoT 中樞的 `minTlsVersion` 屬性會在容錯移轉後的地理配對區域中維持有效。

### <a name="recommended-ciphers"></a>建議的密碼

設定為只接受 TLS 1.2 的 IoT 中樞也會強制使用下列建議的密碼：

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`

### <a name="use-tls-12-in-your-iot-hub-sdks"></a>在您的 IoT 中樞 Sdk 中使用 TLS 1。2

使用下列連結，在 IoT 中樞用戶端 Sdk 中設定 TLS 1.2 和允許的密碼。

| Language | 支援 TLS 1。2 | 文件 |
|----------|-------------------|---------------|
| C        | 是               | [連結](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | 是               | [連結](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | 是               | [連結](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | 是               | [連結](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | 是               | [連結](https://aka.ms/Tls_Node_SDK_IoT) |


### <a name="use-tls-12-in-your-iot-edge-setup"></a>在 IoT Edge 安裝程式中使用 TLS 1。2

IoT Edge 裝置可以設定為在與 IoT 中樞通訊時使用 TLS 1.2。 基於此目的，請使用[IoT Edge 檔頁面](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md)。