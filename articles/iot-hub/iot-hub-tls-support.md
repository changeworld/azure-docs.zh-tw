---
title: Azure IoT 中樞的 TLS 支援
description: 使用安全 TLS 連線讓裝置和服務與 IoT 中樞通訊的最佳做法
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 11/13/2020
ms.author: jlian
ms.openlocfilehash: c9dd66fe9d71f0a857e4b0821190bceb5d6d4680
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2020
ms.locfileid: "94628793"
---
# <a name="tls-support-in-iot-hub"></a>IoT 中樞的 TLS 支援

IoT 中樞使用傳輸層安全性 (TLS) 來保護 IoT 裝置和服務的連線。 目前支援三種 TLS 通訊協定版本，亦即 1.0、1.1 和 1.2 版。

TLS 1.0 和 1.1 被視為舊版，並已規劃淘汰。 如需詳細資訊，請參閱 [IoT 中樞即將淘汰 TLS 1.0 和 1.1](iot-hub-tls-deprecating-1-0-and-1-1.md)。 強烈建議在連線到 IoT 中樞時，使用 TLS 1.2 作為慣用的 TLS 版本。

## <a name="tls-12-enforcement-available-in-select-regions"></a>選取區域中可用的 TLS 1.2 強制

為了增加安全性，請將您的 IoT 中樞設定為 *只* 允許使用 TLS 1.2 版的用戶端連線，以及強制使用 [加密套件](#cipher-suites)。 這項功能僅在下欄區域中受到支援：

* 美國東部
* 美國中南部
* 美國西部 2
* US Gov 亞利桑那州
* US Gov 維吉尼亞州

基於此目的，請在任何支援的區域中佈建新的 IoT 中樞，並在 Azure Resource Manager 範本的 IoT 中樞資源規格中，將 `minTlsVersion` 屬性設定為 `1.2`：

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

使用此設定建立的 IoT 中樞資源將會拒絕嘗試使用 TLS 1.0 和 1.1 版連線的裝置和服務用戶端。 同樣地，如果訊息不會 `ClientHello` 列出任何 [建議的密碼](#cipher-suites)，TLS 信號交換將會遭到拒絕。

> [!NOTE]
> `minTlsVersion` 屬性是唯讀的，且無法在建立 IoT 中樞資源之後變更。 因此，您必須事先適當地測試並驗證「所有」IoT 裝置和服務都符合 TLS 1.2 和[建議的加密](#cipher-suites)規範。
> 
> 容錯移轉之後，您 IoT 中樞的 `minTlsVersion` 屬性會在容錯移轉後的異地配對區域中維持有效。

## <a name="cipher-suites"></a>加密套件

設定為僅接受 TLS 1.2 的 IoT 中樞也會強制使用下列建議的加密套件：

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`

針對未針對 TLS 1.2 強制進行設定的 IoT 中樞，TLS 1.2 仍適用于下列加密套件：

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

用戶端可以建議在期間使用的較高加密套件清單 `ClientHello` 。 不過，IoT 中樞可能不支援其中部分 (例如 `ECDHE-ECDSA-AES256-GCM-SHA384`) 。 在此情況下，「IoT 中樞」會嘗試遵循用戶端的喜好設定，但最後會與加密套件進行協調 `ServerHello` 。

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

## <a name="device-authentication"></a>裝置驗證

成功進行 TLS 信號交換之後，IoT 中樞可以使用對稱金鑰或 x.509 憑證來驗證裝置。 若是以憑證為基礎的驗證，這可以是任何的 x.509 憑證（包括 ECC）。 IoT 中樞會根據您提供的憑證指紋或憑證授權單位單位來驗證憑證 (CA) 。 若要深入瞭解，請參閱 [支援的 x.509 憑證](iot-hub-devguide-security.md#supported-x509-certificates)。
