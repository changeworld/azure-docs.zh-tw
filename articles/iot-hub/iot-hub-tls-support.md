---
title: Azure IoT 中樞的 TLS 支援
description: 瞭解如何使用安全的 TLS 連線來與 IoT 中樞通訊的裝置和服務
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: jlian
ms.openlocfilehash: e569cbe9030b2ac5a42bd99233b4fefc925a5662
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98220300"
---
# <a name="transport-layer-security-tls-support-in-iot-hub"></a>IoT 中樞內的傳輸層安全性 (TLS) 支援

IoT 中樞使用傳輸層安全性 (TLS) 來保護 IoT 裝置和服務的連線。 目前支援三種 TLS 通訊協定版本，亦即 1.0、1.1 和 1.2 版。

TLS 1.0 和 1.1 被視為舊版，並已規劃淘汰。 如需詳細資訊，請參閱 [IoT 中樞即將淘汰 TLS 1.0 和 1.1](iot-hub-tls-deprecating-1-0-and-1-1.md)。 若要避免未來發生問題，請在連線到 IoT 中樞時，使用 TLS 1.2 作為唯一的 TLS 版本。

## <a name="iot-hubs-server-tls-certificate"></a>IoT 中樞的伺服器 TLS 憑證

在 TLS 信號交換期間，IoT 中樞會提供 RSA 金鑰的伺服器憑證來連接用戶端。 它的根是巴爾的摩 Cybertrust 根 CA。 我們最近推出了對 TLS 伺服器憑證的變更，因此它現在是由新的中繼憑證授權單位單位發行 (的) 。 如需詳細資訊，請參閱 [IoT 中樞 TLS 憑證更新](https://azure.microsoft.com/updates/iot-hub-tls-certificate-update/)。

### <a name="elliptic-curve-cryptography-ecc-server-tls-certificate-preview"></a> (ECC 的橢圓曲線密碼編譯) server TLS 憑證 (預覽) 

IoT 中樞 ECC 伺服器 TLS 憑證可供公開預覽。 雖然提供與 RSA 憑證類似的安全性，但 ECC 憑證驗證 (使用僅限 ECC 的加密套件，) 使用的計算、記憶體和頻寬最高可達40%。 這些節省對於 IoT 裝置而言很重要，因為它們的設定檔和記憶體較小，而且支援網路頻寬受限環境中的使用案例。 

若要預覽 IoT 中樞的 ECC 伺服器憑證：

1. [建立新的 IoT 中樞，並開啟預覽模式](iot-hub-preview-mode.md)。
1. [將您的用戶端設定](#tls-configuration-for-sdk-and-iot-edge) 為 *僅* 包含 ECDSA 加密套件，並 *排除* 任何 RSA。 以下是支援的 ECC 憑證公開預覽加密套件：
    - `TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256`
    - `TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384`
    - `TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256`
    - `TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384`
1. 將用戶端連線至預覽版 IoT 中樞。

## <a name="tls-12-enforcement-available-in-select-regions"></a>選取區域中可用的 TLS 1.2 強制

為了增加安全性，請將您的 IoT 中樞設定為 *只* 允許使用 TLS 1.2 版的用戶端連線，以及強制使用 [加密套件](#cipher-suites)。 這項功能僅在下欄區域中受到支援：

* 美國東部
* 美國中南部
* 美國西部 2
* US Gov 亞利桑那州
* 在此區域中無法使用 US Gov 維吉尼亞州 (TLS 1.0/1.1 支援-必須啟用 TLS 1.2 強制，否則無法建立 IoT 中樞) 

若要啟用 TLS 1.2 強制，請遵循在 [Azure 入口網站中建立 IoT 中樞](/.iot-hub-create-through-portal.md)的步驟，但

- 從上述清單中的其中一個 **區域選擇區域** 。
- 在 [ **管理-> Advanced-> 傳輸層安全性 (tls) -> 最小 tls 版本**，請選取 **1.2**。 只有在支援的區域中建立的 IoT 中樞才會顯示此設定。

    :::image type="content" source="media/iot-hub-tls-12-enforcement.png" alt-text="顯示如何在建立 IoT 中樞期間開啟 TLS 1.2 強制的螢幕擷取畫面":::

若要使用 ARM 範本建立，請在任何支援的區域中布建新的 IoT 中樞，並 `minTlsVersion` `1.2` 在資源規格中將屬性設為：

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

## <a name="tls-configuration-for-sdk-and-iot-edge"></a>適用于 SDK 和 IoT Edge 的 TLS 設定

使用下列連結，在 IoT 中樞用戶端 SDK 中設定 TLS 1.2 和允許的加密。

| Language | 支援 TLS 1.2 的版本 | 文件 |
|----------|------------------------------------|---------------|
| C        | 標記 2019-12-11 或更新版本            | [連結](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | 2\.0.0 版或更新版本             | [連結](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | 1\.21.4 版或更新版本            | [連結](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | 1\.19.0 版或更新版本            | [連結](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | 1\.12.2 版或更新版本            | [連結](https://aka.ms/Tls_Node_SDK_IoT) |

您可將 IoT Edge 裝置設定為使用 TLS 1.2 來與 IoT 中樞通訊。 基於此目的，請使用 [IoT Edge 文件頁面](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md)。

## <a name="device-authentication"></a>裝置驗證

成功進行 TLS 信號交換之後，IoT 中樞可以使用對稱金鑰或 x.509 憑證來驗證裝置。 若是以憑證為基礎的驗證，這可以是任何的 x.509 憑證（包括 ECC）。 IoT 中樞會根據您提供的憑證指紋或憑證授權單位單位來驗證憑證 (CA) 。 若要深入瞭解，請參閱 [支援的 x.509 憑證](iot-hub-devguide-security.md#supported-x509-certificates)。

## <a name="tls-maximum-fragment-length-negotiation-preview"></a>TLS 最大片段長度協商 (預覽) 

IoT 中樞也支援 TLS 最大片段長度的協商，有時也稱為 TLS 框架大小的協商。 這項功能處於公開預覽狀態。 

您可以使用這項功能，將純文字片段長度上限指定為小於預設值 2 ^ 14 個位元組的值。 一旦經過協商之後，IoT 中樞和用戶端就會開始將訊息，以確保所有片段都小於協商長度。 此行為對計算或記憶體限制裝置很有説明。 若要深入瞭解，請參閱 [官方 TLS 延伸模組規格](https://tools.ietf.org/html/rfc6066#section-4)。

此公開預覽功能目前尚未提供正式的 SDK 支援。 快速入門

1. [建立新的 IoT 中樞，並開啟預覽模式](iot-hub-preview-mode.md)。
1. 使用 OpenSSL 時，請呼叫 [SSL_CTX_set_tlsext_max_fragment_length](https://manpages.debian.org/testing/libssl-doc/SSL_CTX_set_max_send_fragment.3ssl.en.html) 來指定片段大小。
1. 將用戶端連線至預覽版 IoT 中樞。

## <a name="next-steps"></a>後續步驟

- 若要深入瞭解 IoT 中樞的安全性和存取控制，請參閱 [控制 Iot 中樞的存取權](iot-hub-devguide-security.md)。
- 若要深入瞭解如何使用 X509 憑證進行裝置驗證，請參閱 [使用 X.509 CA 憑證的裝置驗證。](iot-hub-x509ca-overview.md)
