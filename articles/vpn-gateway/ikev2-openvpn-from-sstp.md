---
title: 從 SSTP 轉換至 OpenVPN 或 IKEv2 |Azure VPN 閘道
description: 本文可協助您瞭解如何克服 SSTP 的128並行連接限制。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: alzam
ms.openlocfilehash: 59aa6f5560917651d8f60c667145b0953bf72ef5
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660860"
---
# <a name="transition-to-openvpn-protocol-or-ikev2-from-sstp"></a>從 SSTP 轉換至 OpenVPN 通訊協定或 IKEv2

點對站 (P2S) VPN 閘道連線可讓您建立從個別用戶端電腦到您的虛擬網路的安全連線。 P2S 連線的建立方式是從用戶端電腦開始。 本文適用于 Resource Manager 部署模型，並說明如何藉由轉換成 OpenVPN 通訊協定或 IKEv2 來克服128的並行連線限制。

## <a name="what-protocol-does-p2s-use"></a><a name="protocol"></a>P2S 使用何種通訊協定？

店對站 VPN 可以使用下列其中一個通訊協定：

* **OpenVPN &reg; 通訊協定**，以 SSL/TLS 為基礎的 VPN 通訊協定。 SSL VPN 解決方案可以滲透防火牆，因為大部分的防火牆都會開啟 TCP 埠443輸出（SSL 會使用此埠）。 OpenVPN 可用於從 Android、iOS (11.0 版和更新版本) 、Windows、Linux 和 Mac 裝置， (OSX 10.13 版和更新版本) 。

* **(SSTP) 的安全通訊端通道通訊協定**，這是專屬的 SSL 型 VPN 通訊協定。 SSL VPN 解決方案可以滲透防火牆，因為大部分的防火牆都會開啟 TCP 埠443輸出（SSL 會使用此埠）。 SSTP 僅在 Microsoft 裝置上提供支援。 Azure 支援所有具有 SSTP (Windows 7 及更新版本) 的 Windows 版本。 **SSTP 最多可支援128個並行連線，無論閘道 SKU** 為何。

* IKEv2 VPN，標準型 IPsec VPN 解決方案。 IKEv2 VPN 可用於從 Mac 裝置連線 (OSX 版本 10.11 和更新版本)。


>[!NOTE]
>適用於 P2S 的 IKEv2 與 OpenVPN 僅供 Resource Manager 部署模型使用， 不適用於傳統部署模型。 基本閘道 SKU 不支援 IKEv2 或 OpenVPN 通訊協定。 如果您使用的是基本 SKU，就必須刪除並重新建立生產 SKU 虛擬網路閘道。
>

## <a name="migrating-from-sstp-to-ikev2-or-openvpn"></a>從 SSTP 遷移至 IKEv2 或 OpenVPN

在某些情況下，您可能會想要支援超過128的並行 P2S 連線至 VPN 閘道，但使用 SSTP。 在這種情況下，您必須移至 IKEv2 或 OpenVPN 通訊協定。

### <a name="option-1---add-ikev2-in-addition-to-sstp-on-the-gateway"></a>選項 1-新增 IKEv2 和閘道上的 SSTP

這是最簡單的選項。 SSTP 和 IKEv2 可以並存于相同的閘道，並提供您更多的並行連線數目。 您可以直接在現有的閘道上啟用 IKEv2，並重新下載用戶端。

將 IKEv2 新增至現有的 SSTP VPN 閘道不會影響現有的用戶端，您可以將它們設定為使用小型批次中的 IKEv2，或只將新的用戶端設定為使用 IKEv2。 如果 Windows 用戶端同時設定 SSTP 和 IKEv2，它會先嘗試使用 IKEV2 進行連線，如果失敗，則會回復為 SSTP。

**IKEv2 使用非標準的 UDP 埠，因此您必須確保這些埠不會在使用者的防火牆上遭到封鎖。使用中的埠是 UDP 500 和4500。**

若要將 IKEv2 新增至現有的閘道，只要移至入口網站中虛擬網路閘道底下的 [點對站設定] 索引標籤，然後從下拉式方塊中選取 [ **IKEv2 和 SSTP (SSL)** 。

![顯示 [點對站設定] 頁面的螢幕擷取畫面，其中已選取 [通道類型] 下拉式清單，並已選取 [IKEv2 和 SSTP (SSL) ]。](./media/ikev2-openvpn-from-sstp/sstptoikev2.png "IKEv2")


### <a name="option-2---remove-sstp-and-enable-openvpn-on-the-gateway"></a>選項 2-移除 SSTP 並在閘道上啟用 OpenVPN

因為 SSTP 和 OpenVPN 都是以 TLS 為基礎的通訊協定，所以它們不能並存于相同的閘道。 如果您決定將 SSTP 移至 OpenVPN，則必須停用 SSTP 並在閘道上啟用 OpenVPN。 在用戶端上設定新設定檔之前，這種作業會導致現有的用戶端失去對 VPN 閘道的連線能力。

如果您想要的話，可以搭配 IKEv2 來啟用 OpenVPN。 OpenVPN 是以 TLS 為基礎，而且會使用標準 TCP 443 埠。 若要切換到 OpenVPN，請移至入口網站中虛擬網路閘道底下的 [點對站設定] 索引標籤，然後從下拉式方塊中選取 [ **OpenVPN (ssl)** 或 [ **IKEv2 和 OpenVPN (ssl)** ]。

![點對站](./media/ikev2-openvpn-from-sstp/sstptoopenvpn.png "OpenVPN")

設定閘道之後，除非您 [部署和設定 OpenVPN 用戶端](./vpn-gateway-howto-openvpn-clients.md)，否則現有的用戶端將無法連接。

如果您使用 Windows 10，也可以使用 [適用于 Windows 的 Azure VPN Client](./openvpn-azure-ad-client.md#to-download-the-azure-vpn-client)


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

### <a name="which-gateway-skus-support-p2s-vpn"></a><a name="gwsku"></a>哪些閘道 Sku 支援 P2S VPN？

[!INCLUDE [aggregate throughput sku](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

* 如需閘道 SKU 建議，請參閱[關於 VPN 閘道設定](vpn-gateway-about-vpn-gateway-settings.md#gwsku)。

>[!NOTE]
>基本 SKU 不支援 IKEv2 或 RADIUS 驗證。
>

### <a name="what-ikeipsec-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="IKE/IPsec policies"></a>哪些 IKE/IPsec 原則是在 P2S 的 VPN 閘道上設定？


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

**IPsec**

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

### <a name="what-tls-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="TLS policies"></a>P2S VPN 閘道上設定了哪些 TLS 原則？
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

**"OpenVPN" 是 OpenVPN Inc. 的商標。**