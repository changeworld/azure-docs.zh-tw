---
title: 從 SSTP 過渡到 OpenVPN 或 IKEv2 |Azure VPN 閘道
description: 本文可説明您瞭解克服 SSTP 的 128 併發連接限制的方法。
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: alzam
ms.openlocfilehash: 5500d993a4bf3c664f14182d983f9abed8ebb08a
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398372"
---
# <a name="transition-to-openvpn-protocol-or-ikev2-from-sstp"></a>從 SSTP 過渡到 OpenVPN 協定或 IKEv2

點對站 (P2S) VPN 閘道連線可讓您建立從個別用戶端電腦到您的虛擬網路的安全連線。 P2S 連線的建立方式是從用戶端電腦開始。 本文適用於資源管理器部署模型,並討論如何通過過渡到 OpenVPN 協定或 IKEv2 來克服 SSTP 的 128 個併發連接限制。

## <a name="what-protocol-does-p2s-use"></a><a name="protocol"></a>P2S 使用哪種通訊協定？

店對站 VPN 可以使用下列其中一個通訊協定：

* **OpenVPN&reg;協定**,基於 SSL/TLS 的 VPN 協定。 SSL VPN 解決方案可以穿透防火牆,因為大多數防火牆都打開 TCP 埠 443 出站,這是 SSL 使用的。 OpenVPN 可用於從 Android、iOS(版本 11.0 及以上)、Windows、Linux 和 Mac 設備(OSX 版本 10.13 及以上)進行連接。

* **安全通訊接字隧道協定 (SSTP),** 一種基於 SSL 的專有 VPN 協定. SSL VPN 解決方案可以穿透防火牆,因為大多數防火牆都打開 TCP 埠 443 出站,這是 SSL 使用的。 SSTP 僅在 Microsoft 裝置上提供支援。 Azure 支援所有具有 SSTP (Windows 7 及更新版本) 的 Windows 版本。 **SSTP 僅支援多達 128 個併發連線,而不考慮閘道 SKU**。

* IKEv2 VPN，標準型 IPsec VPN 解決方案。 IKEv2 VPN 可用於從 Mac 裝置連線 (OSX 版本 10.11 和更新版本)。


>[!NOTE]
>適用於 P2S 的 IKEv2 與 OpenVPN 僅供 Resource Manager 部署模型使用， 不適用於傳統部署模型。 基本閘道 SKU 不支援 IKEv2 或 OpenVPN 協定。 如果使用基本 SKU,則必須刪除並重新創建生產 SKU 虛擬網路閘道。
>

## <a name="migrating-from-sstp-to-ikev2-or-openvpn"></a>從 SSTP 移植到 IKEv2 或 OpenVPN

在某些情況下,您可能希望支援到 VPN 閘道的 128 個併發 P2S 連接,但使用 SSTP。 在這種情況下,您需要遷移到 IKEv2 或 OpenVPN 協定。

### <a name="option-1---add-ikev2-in-addition-to-sstp-on-the-gateway"></a>選項 1 - 除了閘道上的 SSTP 外新增 IKEv2

這是最簡單的選項。 SSTP 和IKEv2可以共存於同一閘道上,並為您提供數量較多的併發連接。 只需在現有閘道上啟用 IKEv2 並重新下載用戶端即可。

將 IKEv2 添加到現有 SSTP VPN 閘道不會影響現有用戶端,您可以將其配置為小批量使用 IKEv2,或者僅將新用戶端配置為使用 IKEv2。 如果為 SSTP 和 IKEv2 配置了 Windows 用戶端,它將嘗試首先使用 IKEV2 進行連接,如果失敗,它將回落到 SSTP。

**IKEv2 使用非標準 UDP 連接埠,因此您需要確保這些埠不會在使用者的防火牆上被阻止。正在使用的埠是UDP 500和4500。**

要將 IKEv2 添加到現有閘道,只需轉到門戶中虛擬網路閘道下的「點對點配置」選項卡,然後從下拉框中選擇**IKEv2 和 SSTP (SSL)。**

![點對點](./media/ikev2-openvpn-from-sstp/sstptoikev2.png "IKEv2")


### <a name="option-2---remove-sstp-and-enable-openvpn-on-the-gateway"></a>選項 2 - 移除 SSTP 並在閘道上啟用 OpenVPN

由於 SSTP 和 OpenVPN 都是基於 TLS 的協定,因此它們不能共存於同一閘道上。 如果您決定從 SSTP 遷移到 OpenVPN,您必須禁用 SSTP 並在閘道上啟用 OpenVPN。 此操作將導致現有用戶端失去與 VPN 閘道的連接,直到用戶端上配置了新配置檔。

如果需要,您可以與 IKEv2 一起啟用 OpenVPN。 OpenVPN 基於 TLS,使用標準 TCP 443 連接埠。 要切換到 OpenVPN,請轉到門戶中虛擬網路閘道下的「點對點設定」選項卡,然後從下拉框中選擇**OpenVPN (SSL)** 或**IKEv2 和 OpenVPN (SSL)。**

![點對點](./media/ikev2-openvpn-from-sstp/sstptoopenvpn.png "OpenVPN")

配置閘道後,在[部署和配置 OpenVPN 用戶端](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-openvpn-clients)之前,現有用戶端將無法連接。

如果使用 Windows 10,也可以對 Windows 使用[Azure VPN 用戶端](https://docs.microsoft.com/azure/vpn-gateway/openvpn-azure-ad-client#to-download-the-azure-vpn-client)


## <a name="frequently-asked-questions"></a>常見問題集
### <a name="what-are-the-client-configuration-requirements"></a>設定用戶端有哪些需求？

>[!NOTE]
>對於 Windows 用戶端，您在用戶端裝置上必須具備系統管理員權限，才能將用戶端裝置到 Azure 的 VPN 連線初始化。
>

使用者會在 P2S 的 Windows 和 Mac 裝置上使用原生 VPN 用戶端。 Azure 會提供 VPN 用戶端組態 zip 檔案，其中包含這些原生用戶端連線到 Azure 所需的設定。

* 對於 Windows 裝置，VPN 用戶端組態包含使用者在其裝置上安裝的安裝程式套件。
* 對於 Mac 裝置，其中包含使用者在其裝置上安裝的 mobileconfig 檔案。

Zip 檔案也會提供 Azure 端的某些重要設定值，以便用於為這些裝置建立自己的設定檔。 這些值包括 VPN 閘道位址，已設定的通道類型、路由，以及用於閘道驗證的根憑證。

>[!NOTE]
>[!INCLUDE [TLS version changes](../../includes/vpn-gateway-tls-change.md)]
>

### <a name="which-gateway-skus-support-p2s-vpn"></a><a name="gwsku"></a>哪個閘道 SKU 支援 P2S VPN?

[!INCLUDE [aggregate throughput sku](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

* 如需閘道 SKU 建議，請參閱[關於 VPN 閘道設定](vpn-gateway-about-vpn-gateway-settings.md#gwsku)。

>[!NOTE]
>基本 SKU 不支援 IKEv2 或 RADIUS 驗證。
>

### <a name="what-ikeipsec-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="IKE/IPsec policies"></a>在 P2S 的 VPN 閘道上配置了哪些 IKE/IPsec 策略?


**IKEv2**

|**密碼** | **完整性** | **Prf** | **DH 群組** |
|---        | ---            | ---        | ---     |
|GCM_AES256 |    GCM_AES256    | SHA384    | GROUP_24 |
|GCM_AES256 |    GCM_AES256    | SHA384    | GROUP_14 |
|GCM_AES256 |    GCM_AES256    | SHA384    | GROUP_ECP384 |
|GCM_AES256 |    GCM_AES256    | SHA384    | GROUP_ECP256 |
|GCM_AES256 |    GCM_AES256    | SHA256    | GROUP_24 |
|GCM_AES256 |    GCM_AES256    | SHA256    | GROUP_14 |
|GCM_AES256 |    GCM_AES256    | SHA256    | GROUP_ECP384 |
|GCM_AES256 |    GCM_AES256    | SHA256    | GROUP_ECP256 |
|AES256     |   SHA384        | SHA384    | GROUP_24 |
|AES256     |   SHA384        | SHA384    | GROUP_14 |
|AES256     |   SHA384        | SHA384    | GROUP_ECP384 |
|AES256     |   SHA384        | SHA384    | GROUP_ECP256 |
|AES256     |   SHA256        | SHA256    | GROUP_24 |
|AES256     |   SHA256        | SHA256    | GROUP_14 |
|AES256     |   SHA256        | SHA256    | GROUP_ECP384 |
|AES256     |   SHA256        | SHA256    | GROUP_ECP256 |
|AES256     |   SHA256        | SHA256    | GROUP_2 |

**Ipsec**

|**密碼** | **完整性** | **PFS 群組** |
|---        | ---            | ---        |
|GCM_AES256    | GCM_AES256 | GROUP_NONE |
|GCM_AES256    | GCM_AES256 | GROUP_24 |
|GCM_AES256    | GCM_AES256 | GROUP_14 |
|GCM_AES256    | GCM_AES256 | GROUP_ECP384 |
|GCM_AES256    | GCM_AES256 | GROUP_ECP256 |
| AES256    | SHA256 | GROUP_NONE |
| AES256    | SHA256 | GROUP_24 |
| AES256    | SHA256 | GROUP_14 |
| AES256    | SHA256 | GROUP_ECP384 |
| AES256    | SHA256 | GROUP_ECP256 |
| AES256    | SHA1 | GROUP_NONE |

### <a name="what-tls-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="TLS policies"></a>P2S 的 VPN 閘道上配置了哪些 TLS 策略?
**TLS**

|**原則** |
|---| 
|TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 |
|TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 |
|TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 |
|TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 |
|TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256 |
|TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384 |
|TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 |
|TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 |
|TLS_RSA_WITH_AES_128_GCM_SHA256 |
|TLS_RSA_WITH_AES_256_GCM_SHA384 |
|TLS_RSA_WITH_AES_128_CBC_SHA256 |
|TLS_RSA_WITH_AES_256_CBC_SHA256 |

### <a name="how-do-i-configure-a-p2s-connection"></a><a name="configure"></a>如何設定 P2S 連線？

P2S 設定需要相當多的特定步驟。 下列文章包含的步驟可引導您進行 P2S 設定，然後連結以設定 VPN 用戶端裝置：

* [設定 P2S 連線 - RADIUS 驗證](point-to-site-how-to-radius-ps.md)

* [設定 P2S 連線 - Azure 原生憑證驗證](vpn-gateway-howto-point-to-site-rm-ps.md)

* [設定 OpenVPN](vpn-gateway-howto-openvpn.md)

## <a name="next-steps"></a>後續步驟

* [設定 P2S 連線 - RADIUS 驗證](point-to-site-how-to-radius-ps.md)

* [設定 P2S 連線 - Azure 原生憑證驗證](vpn-gateway-howto-point-to-site-rm-ps.md)

**"OpenVPN"是OpenVPN公司的商標。**
