---
title: S2S VPN & VNet 對 VNet 連線的 IPsec/IKE 原則： Azure 入口網站
titleSuffix: Azure VPN Gateway
description: 使用 Azure Resource Manager 和 Azure 入口網站，設定與 Azure VPN 閘道之 S2S 或 VNet 對 VNet 連線的 IPsec/IKE 原則。
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/18/2020
ms.author: yushwang
ms.openlocfilehash: 2b298185866d16da02fe8d3b3fdb41f0b0b1f726
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878539"
---
# <a name="configure-ipsecike-policy-for-s2s-vpn-or-vnet-to-vnet-connections-azure-portal"></a>設定 S2S VPN 或 VNet 對 VNet 連線的 IPsec/IKE 原則： Azure 入口網站

本文將逐步引導您完成使用 Azure 入口網站設定 VPN 閘道站對站 VPN 或 VNet 對 VNet 連線的 IPsec/IKE 原則的步驟。 下列各節可協助您建立和設定 IPsec/IKE 原則，並將原則套用至新的或現有的連接。

## <a name="about-ipsec-and-ike-policy-parameters"></a><a name="about"></a>關於 IPsec 和 IKE 原則參數

IPsec 和 IKE 通訊協定標準支援各種不同的密碼編譯演算法的各種組合。 請參閱 [有關密碼編譯需求和 AZURE VPN 閘道的資訊](vpn-gateway-about-compliance-crypto.md) ，以瞭解這如何協助確保跨單位和 VNet 對 vnet 的連線能力，以滿足您的合規性或安全性需求。

本文提供建立和設定 IPsec/IKE 原則的指示，並將其套用至新的或現有的 VPN 閘道連線。

### <a name="considerations"></a>考量

* IPsec/IKE 原則僅適用于下列閘道 Sku：
  * ***VpnGw1 ~ 5 And VpnGw1AZ ~ 5AZ** _ _ ***Standard** _ 和 _*_HighPerformance_*_ _ 您只能針對指定的連接指定 ***1** _ 原則組合。
  _ 您必須同時指定 IKE (主要模式) 和 IPsec (快速模式) 的所有演算法和參數。 系統不允許只指定一部分原則。
* 請確認 VPN 裝置廠商規格，確保內部部署 VPN 裝置支援原則。 如果原則不相容，則無法建立 S2S 或 VNet 對 VNet 連線。

## <a name="workflow"></a><a name ="workflow"></a>工作流程

本節所概述的工作流程可在 S2S VPN 或 VNet 對 VNet 連線上建立和更新 IPsec/IKE 原則：

1. 建立虛擬網路和 VPN 閘道。
2. 建立局域網路閘道以進行跨單位連線，或建立另一個虛擬網路和閘道以進行 VNet 對 VNet 連線。
3. 建立 (IPsec 或 VNet2VNet) 的連線。
4. 設定/更新/移除連接資源上的 IPsec/IKE 原則。

本文中的指示可協助您設定和設定 IPsec/IKE 原則，如下圖所示：

:::image type="content" source="./media/ipsec-ike-policy-howto/policy-diagram.png" alt-text="IPsec/IKE 原則圖表" border="false":::

## <a name="supported-cryptographic-algorithms--key-strengths"></a><a name ="params"></a>支援的密碼編譯演算法 & 金鑰長度

### <a name="algorithms-and-keys"></a><a name ="table1"></a>演算法和金鑰

下表列出可供客戶設定的支援密碼編譯演算法和金鑰強度：

| **IPsec/IKE**    | **選項**    |
| ---              | ---            |
| IKE 加密   | AES256、AES192、AES128、DES3、DES                  |
| IKE 完整性    | SHA384、SHA256、SHA1、MD5                          |
| DH 群組         | DHGroup24、ECP384、ECP256、DHGroup14、DHGroup2048、DHGroup2、DHGroup1、無 |
| IPsec 加密 | GCMAES256、GCMAES192、GCMAES128、AES256、AES192、AES128、DES3、DES、無    |
| IPsec 完整性  | GCMASE256、GCMAES192、GCMAES128、SHA256、SHA1、MD5 |
| PFS 群組        | PFS24、ECP384、ECP256、PFS2048、PFS2、PFS1、無   |
| QM SA 存留期   |  (**選擇性**：如果未指定，則會使用預設值) <br>秒 (整數；**最小 300**/預設值 27000 秒)<br>KB 數 (整數；**最小 1024**/預設值 102400000 KB 數)    |
| 流量選取器 | UsePolicyBasedTrafficSelectors** ($True/$False；**選擇性**，如果未指定，即為預設的 $False)    |
| DPD timeout      | 秒 (integer： min. 9/max. 3600;預設值45秒)  |
|  |  |

#### <a name="important-requirements"></a>重要需求

* 內部部署 VPN 裝置組態必須符合或包含您在 Azure IPsec/IKE 原則中指定的下列演算法和參數︰
  * IKE 加密演算法 (主要模式/第 1 階段)
  * IKE 完整性演算法 (主要模式/第 1 階段)
  * DH 群組 (主要模式/第 1 階段)
  * IPsec 加密演算法 (快速模式/第 2 階段)
  * IPsec 完整性演算法 (快速模式/第 2 階段)
  * PFS 群組 (快速模式/第2階段) # A0 * 流量選取器 (如果使用 UsePolicyBasedTrafficSelectors) 
  * SA 存留期只需要在本機指定，不需要相符。

* 如果 GCMAES 用於 IPsec 加密演算法，您必須為 IPsec 完整性選取相同的 GCMAES 演算法和金鑰長度;例如，同時使用 GCMAES128。

* 在上述的 [演算法和金鑰表格](#table1) 中：
  * IKE 對應到主要模式或第1階段
  * IPsec 會對應到快速模式或第 2 階段
  * DH 群組會指定在主要模式或第 1 階段中使用的 Diffie-Hellmen 群組
  * PFS 群組會指定在快速模式或第 2 階段中使用的 Diffie-Hellmen 群組

* 在 Azure VPN 閘道上，IKE 主要模式 SA 存留期會固定為28800秒。

* 如果您在連線上將 **UsePolicyBasedTrafficSelectors** 設定為 $True，則會將 Azure VPN 閘道設定為連線至內部部署以原則為基礎的 VPN 防火牆。 如果您啟用 PolicyBasedTrafficSelectors，則必須確定 VPN 裝置的流量選取器已定義內部部署網路 (區域網路閘道) 前置詞往/返 Azure 虛擬網路前置詞的所有組合，而不是任意對任意的組合。 例如，如果內部部署網路的前置詞為 10.1.0.0/16 和 10.2.0.0/16，而虛擬網路的前置詞為 192.168.0.0/16 和 172.16.0.0/16，則需要指定下列流量選取器︰
  * 10.1.0.0/16 <====> 192.168.0.0/16
  * 10.1.0.0/16 <====> 172.16.0.0/16
  * 10.2.0.0/16 <====> 192.168.0.0/16
  * 10.2.0.0/16 <====> 172.16.0.0/16

   如需原則式流量選取器的詳細資訊，請參閱[連線多個內部部署原則式 VPN 裝置](vpn-gateway-connect-multiple-policybased-rm-ps.md)。

* DPD timeout-Azure VPN 閘道上的預設值為45秒。 將 timeout 設定為較短的期間，會使 IKE 更積極地重設金鑰，導致連線在某些情況下會中斷連線。 如果您的內部部署位置遠離 VPN 閘道所在的 Azure 區域，或實體連結條件可能會導致封包遺失，這可能不是理想的做法。 一般建議是設定 **30 到 45** 秒之間的超時時間。

### <a name="diffie-hellman-groups"></a>Diffie-Hellman 群組

下表列出自訂原則所支援的對應 Diffie-Hellman 群組：

| **Diffie-Hellman 群組**  | **DHGroup**              | **PFSGroup** | **金鑰長度** |
| --- | --- | --- | --- |
| 1                         | DHGroup1                 | PFS1         | 768 位元 MODP   |
| 2                         | DHGroup2                 | PFS2         | 1024 位元 MODP  |
| 14                        | DHGroup14<br>DHGroup2048 | PFS2048      | 2048 位元 MODP  |
| 19                        | ECP256                   | ECP256       | 256 位元 ECP    |
| 20                        | ECP384                   | ECP384       | 384 位元 ECP    |
| 24                        | DHGroup24                | PFS24        | 2048 位元 MODP  |

如需詳細資訊，請參閱 [RFC3526](https://tools.ietf.org/html/rfc3526) 和 [RFC5114](https://tools.ietf.org/html/rfc5114)。

## <a name="s2s-vpn-with-ipsecike-policy"></a><a name ="S2S"></a>使用 IPsec/IKE 原則 S2S VPN

本節將逐步引導您使用 IPsec/IKE 原則建立站對站 VPN 連線。 下列步驟會建立連接，如下圖所示：

:::image type="content" source="./media/ipsec-ike-policy-howto/site-to-site-diagram.png" alt-text="站對站原則" border="false":::

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a><a name="createvnet1"></a>步驟1 - 建立虛擬網路、VPN 閘道和區域網路閘道

建立下列資源，如下列螢幕擷取畫面所示。 如需相關步驟，請參閱 [建立站對站 VPN](./tutorial-site-to-site-portal.md)連線。

* **虛擬網路：**  TestVNet1

   :::image type="content" source="./media/ipsec-ike-policy-howto/testvnet-1.png" alt-text="VNet":::

* **VPN 閘道：** VNet1GW

   :::image type="content" source="./media/ipsec-ike-policy-howto/vnet-1-gateway.png" alt-text="閘道":::

* **局域網路閘道：** Site6

   :::image type="content" source="./media/ipsec-ike-policy-howto/lng-site-6.png" alt-text="站台":::

* **連接：** VNet1 至 Site6

    :::image type="content" source="./media/ipsec-ike-policy-howto/connection-site-6.png" alt-text="[連接]":::

### <a name="step-2---configure-ipsecike-policy-on-the-s2s-vpn-connection"></a><a name="s2sconnection"></a>步驟 2-設定 S2S VPN 連接的 IPsec/IKE 原則

在本節中，使用下列演算法和參數來設定 IPsec/IKE 原則：

* IKE： AES256、SHA384、DHGroup24、DPD timeout 45 秒
* IPsec： AES256、SHA256、PFS 無、SA 存留期30000秒和 102400000 KB

1. 在 Azure 入口網站中，流覽至連接資源 **VNet1toSite6**。 選取 [設定 **] 頁面，然後選取 [** **自訂** IPsec/IKE 原則]，以顯示所有設定選項。 下列螢幕擷取畫面顯示根據清單的設定：

    :::image type="content" source="./media/ipsec-ike-policy-howto/policy-site-6.png" alt-text="網站6":::

1. 如果您針對 IPsec 使用 GCMAES，就必須針對 IPsec 加密和完整性使用相同的 GCMAES 演算法和金鑰長度。 例如，下列螢幕擷取畫面指定 IPsec 加密和 IPsec 完整性的 GCMAES128：

   :::image type="content" source="./media/ipsec-ike-policy-howto/gcmaes.png" alt-text="適用于 IPsec 的 GCMAES":::

1. 您可以選擇性地針對 [**使用原則式流量選取器**] 選項選取 [**啟用**]，讓 Azure VPN 閘道能夠連線到內部部署以原則為基礎的 VPN 裝置，如前文所述。

   :::image type="content" source="./media/ipsec-ike-policy-howto/policy-based-selector.png" alt-text="以原則為基礎的流量選取器":::

1. 選取所有選項之後，請選取 [ **儲存** ] 以認可連線資源的變更。 原則將會在大約一分鐘內強制執行。

> [!IMPORTANT]
>
> * 在連線上指定 IPsec/IKE 原則之後，Azure VPN 閘道只會傳送或接受該特定連線上的 IPsec/IKE 提案，而提案具有所指定的密碼編譯演算法和金鑰長度。 確定連線的內部部署 VPN 裝置使用或接受確切原則組合，否則不會建立 S2S VPN 通道。
>
> * 以 **原則為基礎的流量選取器** 和 **DPD timeout** 選項可以使用 **預設** 原則來指定，而不需要自訂的 IPsec/IKE 原則，如上面的螢幕擷取畫面所示。
>

## <a name="vnet-to-vnet-with-ipsecike-policy"></a><a name ="vnet2vnet"></a>使用 IPsec/IKE 原則的 VNet 對 VNet

使用 IPsec/IKE 原則建立 VNet 對 VNet 連線的步驟，類似于 S2S VPN 連線的連接。

:::image type="content" source="./media/ipsec-ike-policy-howto/vnet-policy.png" alt-text="VNet 對 VNet 原則圖表" border="false":::

1. 使用「 [建立 vnet 對 vnet](vpn-gateway-vnet-vnet-rm-ps.md) 連線」一文中的步驟，建立 VNet 對 vnet 連線。

2. 完成這些步驟之後，您會看到兩個 VNet 對 VNet 連線，如下列 VNet2GW 資源中的螢幕擷取畫面所示：

   :::image type="content" source="./media/ipsec-ike-policy-howto/vnet-vnet-connections.png" alt-text="VNet 對 VNet 連線":::

3. 流覽至連線資源，然後移至入口網站上的 [設定 **] 頁面。** 選取 [ **IPsec/IKE 原則**] 上的 [**自訂**]，以顯示自訂原則選項。 選取具有對應金鑰長度的密碼編譯演算法。

   螢幕擷取畫面顯示具有下列演算法和參數的不同 IPsec/IKE 原則：
   * IKE： AES128、SHA1、DHGroup14、DPD timeout 45 秒
   * IPsec：GCMAES128、GCMAES128、PFS14、SA 存留期 14400 秒和 102400000KB

   :::image type="content" source="./media/ipsec-ike-policy-howto/vnet-vnet-policy.png" alt-text="連線原則":::

4. 選取 [ **儲存** ]，在連線資源上套用原則變更。

5. 將相同的原則套用至其他連接資源 VNet2toVNet1。 如果沒有，IPsec/IKE VPN 通道將不會連線，因為原則不相符。

   > [!IMPORTANT]
   > 在連線上指定 IPsec/IKE 原則之後，Azure VPN 閘道只會傳送或接受該特定連線上的 IPsec/IKE 提案，而提案具有所指定的密碼編譯演算法和金鑰長度。 確定這兩個連線的 IPsec 原則相同，否則不會建立 VNet 對 VNet 連線。

6. 完成這些步驟之後，就會在幾分鐘內建立連線，而且您會有下列網路拓撲：

    :::image type="content" source="./media/ipsec-ike-policy-howto/policy-diagram.png" alt-text="IPsec/IKE 原則圖表" border="false":::

## <a name="to-remove-custom-ipsecike-policy-from-a-connection"></a><a name ="deletepolicy"></a>從連線移除自訂 IPsec/IKE 原則

1. 若要從連線移除自訂原則，請流覽至連線資源，然後移至 [設定 **] 頁面以** 查看目前的原則。

2. 選取 [ **IPsec/IKE 原則**] 選項上的 [**預設值**]。 這會移除先前在連接上指定的所有自訂原則，並還原此連線上的預設 IPsec/IKE 設定：

   :::image type="content" source="./media/ipsec-ike-policy-howto/delete-policy.png" alt-text="刪除原則":::

3. 選取 [ **儲存** ] 以移除自訂原則，並在連接上還原預設的 IPSEC/IKE 設定。

## <a name="next-steps"></a>後續步驟

如需以原則為基礎的流量選取器的詳細資訊，請參閱[連線多個內部部署以原則為基礎的 VPN 裝置](vpn-gateway-connect-multiple-policybased-rm-ps.md)。