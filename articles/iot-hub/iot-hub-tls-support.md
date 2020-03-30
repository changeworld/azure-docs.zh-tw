---
title: Azure IoT 中心 TLS 支援
description: 為與 IoT 中心通信的設備和服務使用安全 TLS 連接的最佳做法
services: iot-hub
author: rezasherafat
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: rezas
ms.openlocfilehash: 7ab3b48d22f116a707f68cbf6284928c7d2557e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409488"
---
# <a name="tls-support-in-iot-hub"></a>IoT 中心中的 TLS 支援

IoT 中心使用傳輸層安全 （TLS） 來保護來自 IoT 設備和服務的連接。 當前支援 TLS 協定的三個版本，即版本 1.0、1.1 和 1.2。

TLS 1.0 和 1.1 被視為遺留的，並計畫棄用。 有關詳細資訊，請參閱為[IoT 中心棄用 TLS 1.0 和 1.1](iot-hub-tls-deprecating-1-0-and-1-1.md)。 強烈建議您在連接到 IoT 中心時使用 TLS 1.2 作為首選 TLS 版本。

## <a name="restrict-connections-to-tls-12-in-your-iot-hub-resource"></a>在 IoT 中心資源中限制到 TLS 1.2 的連接

為了增加安全性，建議將 IoT 中心配置為*僅*允許使用 TLS 版本 1.2 的用戶端連接，並強制使用[推薦的密碼](#recommended-ciphers)。

為此，在任何[受支援的區域](#supported-regions)中預配新的 IoT 中心，`minTlsVersion`並將該屬性設置為 Azure`1.2`資源管理器範本的 IoT 中心資源規範中：

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

使用此配置創建的 IoT 中心資源將拒絕嘗試使用 TLS 版本 1.0 和 1.1 進行連接的設備和服務用戶端。 同樣，如果用戶端 HELLO 消息未列出任何[推薦的密碼](#recommended-ciphers)，TLS 握手將被拒絕。

> [!NOTE]
> 該`minTlsVersion`屬性是唯讀的，在創建 IoT 中心資源後無法更改。 因此，您必須提前正確測試和驗證*所有*IoT 設備和服務是否與 TLS 1.2 和[建議的密碼](#recommended-ciphers)相容。

### <a name="supported-regions"></a>支援區域

需要在以下區域創建需要使用 TLS 1.2 的 IoT 中心：

* 美國東部
* 美國中南部
* 美國西部 2
* US Gov 亞利桑那州
* US Gov 維吉尼亞州

> [!NOTE]
> 容錯移轉後，IoT`minTlsVersion`中心的屬性將在容錯移轉後的地理配對區域中保持有效。

### <a name="recommended-ciphers"></a>推薦的密碼

配置為僅接受 TLS 1.2 的 IoT 中心也將強制使用以下推薦的密碼：

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`

### <a name="use-tls-12-in-your-iot-hub-sdks"></a>在 IoT 中心 SDK 中使用 TLS 1.2

使用以下連結配置 TLS 1.2，並允許在 IoT 中心用戶端 SDK 中使用密碼。

| 語言 | 支援 TLS 1.2 的版本 | 文件 |
|----------|------------------------------------|---------------|
| C        | 標籤 2019-12-11 或更新            | [連結](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | 版本 2.0.0 或更新             | [連結](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | 版本 1.21.4 或更新版本            | [連結](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | 版本 1.19.0 或更新版本            | [連結](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | 版本 1.12.2 或更新版本            | [連結](https://aka.ms/Tls_Node_SDK_IoT) |


### <a name="use-tls-12-in-your-iot-edge-setup"></a>在 IoT 邊緣設置中使用 TLS 1.2

在與 IoT 中心通信時，IoT 邊緣設備可配置為使用 TLS 1.2。 為此，請使用[IoT 邊緣文檔頁](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md)。