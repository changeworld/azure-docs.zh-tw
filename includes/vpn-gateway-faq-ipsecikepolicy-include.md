---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/05/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 6684c4269f14b91ded651dadff3f0a2e0878a4f2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027660"
---
### <a name="is-custom-ipsecike-policy-supported-on-all-azure-vpn-gateway-skus"></a>是否所有的 Azure VPN 閘道 SKU 都支援自訂 IPsec/IKE 原則？
除了基本的 SKU 以外，所有 Azure SKU 都支援自訂 IPsec/IKE 原則。

### <a name="how-many-policies-can-i-specify-on-a-connection"></a>可以對連線指定多少個原則？
您只能針對給定的連線指定***一個** 原則組合。

### <a name="can-i-specify-a-partial-policy-on-a-connection-for-example-only-ike-algorithms-but-not-ipsec"></a>可以對連線指定部分原則嗎？ (例如，只指定 IKE 演算法，而不指定 IPsec)
不行，您必須同時對 IKE (主要模式) 和 IPsec (快速模式) 指定所有的演算法和參數。 系統不允許只指定一部分原則。

### <a name="what-are-the-algorithms-and-key-strengths-supported-in-the-custom-policy"></a>自訂原則支援什麼演算法和金鑰強度？
下表列出可供客戶設定的支援密碼編譯演算法和金鑰強度。 每個欄位都必須選取一個選項。

| _ *IPsec/IKEv2**  | **選項**                                                                   |
| ---              | ---                                                                           |
| IKEv2 加密 | AES256、AES192、AES128、DES3、DES                                             |
| IKEv2 完整性  | SHA384、SHA256、SHA1、MD5                                                     |
| DH 群組         | DHGroup24、ECP384、ECP256、DHGroup14 (DHGroup2048)、DHGroup2、DHGroup1、無  |
| IPsec 加密 | GCMAES256、GCMAES192、GCMAES128、AES256、AES192、AES128、DES3、DES、無      |
| IPsec 完整性  | GCMAES256、GCMAES192、GCMAES128、SHA256、SHA1、MD5                            |
| PFS 群組        | PFS24、ECP384、ECP256、PFS2048、PFS2、PFS1、無                              |
| QM SA 存留期   | 秒 (整數；**最小 300**/預設值 27000 秒)<br>KB 數 (整數；**最小 1024**/預設值 102400000 KB 數)           |
| 流量選取器 | UsePolicyBasedTrafficSelectors ($True/$False；預設為 $False)                 |
|                  |                                                                               |

> [!IMPORTANT]
> 1. DHGroup2048 和 PFS2048 與 IKE 和 IPsec PFS 中的 Diffie-Hellman 群組 **14** 相同。 如需完整對應，請參閱 [Diffie-Hellman 群組](#DH)。
> 2. 對於 GCMAES 演算法，您必須針對 IPsec 加密和完整性指定相同的 GCMAES 演算法和金鑰長度。
> 3. IKEv2 主要模式 SA 存留期在 Azure VPN 閘道上固定為 28,800 秒。
> 4. QM SA 存留期是選擇性參數。 如果未指定，則會使用預設值 27,000 秒 (7.5 小時) 和 102400000 KB (102 GB)。
> 5. UsePolicyBasedTrafficSelector 是連線上的選項參數。 若要了解 "UsePolicyBasedTrafficSelectors"，請參閱下一個常見問題。

### <a name="does-everything-need-to-match-between-the-azure-vpn-gateway-policy-and-my-on-premises-vpn-device-configurations"></a>Azure VPN 閘道原則與內部部署 VPN 裝置組態是否需要完全一致？
內部部署 VPN 裝置組態必須符合或包含您在 Azure IPsec/IKE 原則中指定的下列演算法和參數︰

* IKE 加密演算法
* IKE 完整性演算法
* DH 群組
* IPsec 加密演算法
* IPsec 完整性演算法
* PFS 群組
* 流量選取器 (*)

SA 存留期只需要在本機指定，不需要相符。

如果您啟用 **UsePolicyBasedTrafficSelectors**，您必須確定 VPN 裝置的流量選取器已定義了內部部署網路 (區域網路閘道) 前置詞往/返 Azure 虛擬網路前置詞的所有組合，而不是任意對任意的組合。 例如，如果內部部署網路的前置詞為 10.1.0.0/16 和 10.2.0.0/16，而虛擬網路的前置詞為 192.168.0.0/16 和 172.16.0.0/16，則需要指定下列流量選取器︰
* 10.1.0.0/16 <====> 192.168.0.0/16
* 10.1.0.0/16 <====> 172.16.0.0/16
* 10.2.0.0/16 <====> 192.168.0.0/16
* 10.2.0.0/16 <====> 172.16.0.0/16

如需詳細資訊，請參閱[連線多個內部部署原則式 VPN 裝置](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md)。

### <a name="which-diffie-hellman-groups-are-supported"></a><a name ="DH"></a>支援的 Diffie-Hellman 群組為何？
下表列出 IKE (DHGroup) 和 IPsec (PFSGroup) 支援的 Diffie-Hellman 群組：

| **Diffie-Hellman 群組**  | **DHGroup**              | **PFSGroup** | **金鑰長度** |
| ---                       | ---                      | ---          | ---            |
| 1                         | DHGroup1                 | PFS1         | 768 位元 MODP   |
| 2                         | DHGroup2                 | PFS2         | 1024 位元 MODP  |
| 14                        | DHGroup14<br>DHGroup2048 | PFS2048      | 2048 位元 MODP  |
| 19                        | ECP256                   | ECP256       | 256 位元 ECP    |
| 20                        | ECP384                   | ECP384       | 384 位元 ECP    |
| 24                        | DHGroup24                | PFS24        | 2048 位元 MODP  |
|                           |                          |              |                |

如需詳細資訊，請參閱 [RFC3526](https://tools.ietf.org/html/rfc3526) 和 [RFC5114](https://tools.ietf.org/html/rfc5114)。

### <a name="does-the-custom-policy-replace-the-default-ipsecike-policy-sets-for-azure-vpn-gateways"></a>自訂原則是否會取代 Azure VPN 閘道的預設 IPsec/IKE 原則集？
是，若連線指定了自訂原則，Azure VPN 閘道只會使用該連線的原則，來同時做為 IKE 啟動器和 IKE 回應程式。

### <a name="if-i-remove-a-custom-ipsecike-policy-does-the-connection-become-unprotected"></a>如果移除自訂 IPsec/IKE 原則，連線是否會不受保護？
否，連線仍會受到 IPsec/IKE 的保護。 移除連線的自訂原則後，Azure VPN 閘道會回復為使用[預設的 IPsec/IKE 提案清單](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md)，並對內部部署 VPN 裝置再次重新啟動 IKE 交握。

### <a name="would-adding-or-updating-an-ipsecike-policy-disrupt-my-vpn-connection"></a>新增或更新 IPsec/IKE 原則是否會中斷 VPN 連線？
是，這些作業會導致短暫中斷 (幾秒鐘)，因為 Azure VPN 閘道會終止現有連線並重新啟動 IKE 交握，以對新的密碼編譯演算法和參數重新建立 IPsec 通道。 請確定內部部署 VPN 裝置也設定了相同的演算法和金鑰強度，以便盡量減少中斷情形。

### <a name="can-i-use-different-policies-on-different-connections"></a>是否可以對不同連線使用不同原則？
是。 自訂原則會按照個別連線來套用。 您可以對不同連線建立和套用不同的 IPsec/IKE 原則。 您也可以選擇對一部分連線套用自訂原則。 其餘連線則會使用 Azure 預設的 IPsec/IKE 原則集。

### <a name="can-i-use-the-custom-policy-on-vnet-to-vnet-connection-as-well"></a>是否也可以對「VNet 對 VNet 連線」使用自訂原則？
是，IPsec 跨單位連線或 VNet 對 VNet 連線皆可套用自訂原則。

### <a name="do-i-need-to-specify-the-same-policy-on-both-vnet-to-vnet-connection-resources"></a>VNet 對 VNet 連線的兩個資源是否需要指定相同的原則？
是。 VNet 對 VNet 通道在 Azure 中包含兩個連線資源，這兩個資源各自應對一個方向。 確保這兩個連線資源具有相同的原則，否則系統不會建立 VNet 對 VNet 連線。

### <a name="what-is-the-default-dpd-timeout-value-can-i-specify-a-different-dpd-timeout"></a>什麼是預設的 DPD 逾時值？ 我可以指定不同的 DPD 逾時嗎？
預設DPD 逾時值為 45 秒。 您可以在每個 IPsec 或 VNet 對 VNet 連線上指定不同的 DPD 逾時值，介於 9 秒到 3600 秒之間。

### <a name="does-custom-ipsecike-policy-work-on-expressroute-connection"></a>自訂 IPsec/IKE 原則是否適用於 ExpressRoute 連線？
否。 IPsec/IKE 原則只適用於透過 Azure VPN 閘道的 S2S VPN 連線和 VNet 對 VNet 連線。

### <a name="how-do-i-create-connections-with-ikev1-or-ikev2-protocol-type"></a>如何建立與 IKEv1 或 IKEv2 通訊協定類型的連線？
除了基本 SKU、標準 SKU 和其他[舊版 SKU](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#gwsku) 以外，您可以在所有 RouteBased VPN 類型 SKU 上建立 IKEv1 連線。 建立連線時，您可以指定 IKEv1 或 IKEv2 的連線通訊協定類型。 如果您未指定連線通訊協定類型，即會在適用的情況下，使用 IKEv2 作為預設選項。 如需詳細資訊，請參閱 [PowerShell Cmdlet](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) 文件。 如需 SKU 類型與 IKEv1/IKEv2 支援，請參閱[將閘道連線至設定原則的 VPN 裝置](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md) (機器翻譯)。

### <a name="is-transit-between-between-ikev1-and-ikev2-connections-allowed"></a>在 IKEv1 與 IKEv2 連線之間，是否可以進行傳輸？
是。 支援在 IKEv1 與 IKEv2 連線之間進行傳輸。

### <a name="can-i-have-ikev1-site-to-site-connections-on-basic-skus-of-routebased-vpn-type"></a>在 RouteBased VPN 類型的基本 SKU 上，是否可以建立 IKEv1 站對站連線？
否。 基本 SKU 不支援此功能。

### <a name="can-i-change-the-connection-protocol-type-after-the-connection-is-created-ikev1-to-ikev2-and-vice-versa"></a>在建立連線之後，是否可變更連線通訊協定類型 (從 IKEv1 變更到 IKEv2 或變更為相反方向)？
否。 建立連線之後，即無法變更 IKEv1/IKEv2 通訊協定。 您必須刪除連線，然後重新建立具有所需通訊協定類型的新連線。

### <a name="where-can-i-find-more-configuration-information-for-ipsec"></a>哪裡可以找到更多 IPsec 設定資訊？
請參閱[設定 S2S 或 VNet 對 VNet 連線的 IPsec/IKE 原則](../articles/vpn-gateway/vpn-gateway-ipsecikepolicy-rm-powershell.md)