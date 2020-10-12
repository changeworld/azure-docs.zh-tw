---
title: Azure VPN 閘道：關於連接的 VPN 裝置
description: 本文討論 S2S VPN 閘道跨單位連接的 VPN 裝置和 IPsec 參數。 其中提供設定指示和範例的連結。
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/01/2020
ms.author: yushwang
ms.openlocfilehash: 92f589e6a587febc10a4b407fe3616aca42d27d3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89318942"
---
# <a name="about-vpn-devices-and-ipsecike-parameters-for-site-to-site-vpn-gateway-connections"></a>關於 VPN 裝置和站對站 VPN 閘道連線的 IPsec/IKE 參數

使用 VPN 閘道設定站對站 (S2S) 跨單位 VPN 連接需要有 VPN 裝置。 站對站連線可以用來建立混合式解決方案，或者用於您想要在內部部署網路與虛擬網路之間建立安全連線之時。 本文提供已驗證的 VPN 裝置清單和 VPN 閘道的 IPsec/IKE 參數清單。

> [!IMPORTANT]
> 如果您的內部部署 VPN 裝置與 VPN 閘道之間發生連線問題，請參考[已知的裝置相容性問題](#known)。
>

### <a name="items-to-note-when-viewing-the-tables"></a>檢視表格時應注意的項目：

* Azure VPN 閘道的名稱已經變更。 只有名稱會變更。 功能未變更。
  * 靜態路由 = 原則式
  * 動態路由 = 路由式
* 除非另有說明，否則高效能 VPN 閘道和路由式 VPN 閘道的規格相同。 例如，已經驗證與路由式 VPN 閘道相容的 VPN 裝置，也能與高效能 VPN 閘道相容。

## <a name="validated-vpn-devices-and-device-configuration-guides"></a><a name="devicetable"></a>已經驗證的 VPN 裝置及裝置設定指南

我們已與裝置廠商合作驗證一組標準 VPN 裝置。 在以下清單的裝置系列中，所有裝置應該都能與 VPN 閘道搭配運作。 請參閱[關於 VPN 閘道設定](vpn-gateway-about-vpn-gateway-settings.md#vpntype)，了解您要設定的 VPN 閘道解決方案使用的 VPN 類型 (原則式或路由式)。

為了協助設定您的 VPN 裝置，請參閱對應到適當裝置系列的連結。 會以最佳方式來提供組態指示的連結。 如需 VPN 裝置的支援，請連絡裝置製造商。

|**廠商**          |**裝置系列**     |**作業系統最低版本** |**原則式設定指示** |**路由式設定指示** |
| ---                | ---                  | ---                   | ---            | ---           |
| A10 Networks, Inc. |Thunder CFW           |ACOS 4.1.1             |不相容  |[設定指南](https://www.a10networks.com/wp-content/uploads/A10-DG-16161-EN.pdf)|
| Allied Telesis     |AR 系列 VPN 路由器 |AR 系列 5.4.7+               | [設定指南](https://www.alliedtelesis.com/documents/how-to/configure/site-to-site-vpn-between-azure-and-ar-series-router) |[設定指南](https://www.alliedtelesis.com/documents/how-to/configure/site-to-site-vpn-between-azure-and-ar-series-router)|
| Arista | CloudEOS 路由器 | vEOS 4.24.0 FX |  (未測試)  | [設定指南](https://www.arista.com/en/cg-veos-router/veos-router-cloudeos-ipsec-connectivity-to-azure-virtual-network-gateway) |
| Barracuda Networks, Inc. |Barracuda CloudGen Firewall |原則式︰5.4.3<br>路由式︰6.2.0 |[設定指南](https://campus.barracuda.com/product/cloudgenfirewall/doc/79462887/how-to-configure-an-ikev1-ipsec-site-to-site-vpn-to-the-static-microsoft-azure-vpn-gateway/) |[設定指南](https://campus.barracuda.com/product/cloudgenfirewall/doc/79462889/how-to-configure-bgp-over-ikev2-ipsec-site-to-site-vpn-to-an-azure-vpn-gateway/) |
| Check Point |Security Gateway |R80.10 |[設定指南](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |[設定指南](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |
| Cisco              |ASA       |8.3<br>8.4+ (IKEv2\*) |支援 |[設定指南 *](https://www.cisco.com/c/en/us/support/docs/security/adaptive-security-appliance-asa-software/214109-configure-asa-ipsec-vti-connection-to-az.html) |
| Cisco |ASR |原則式：IOS 15.1<br>路由式：IOS 15.2 |支援 |支援 |
| Cisco | Csr | 路由式： IOS-XE 16.10 |  (未測試)  | [設定指令碼](vpn-gateway-download-vpndevicescript.md) |
| Cisco |ISR |原則式：IOS 15.0<br>路由式\*：IOS 15.1 |支援 |支援 |
| Cisco |Meraki (MX)  | MX v 15.12 |不相容 | [設定指南](https://documentation.meraki.com/MX/Site-to-site_VPN/Configuring_Site_to_Site_VPN_tunnels_to_Azure_VPN_Gateway) |
| Cisco | vEdge (Viptela 作業系統)  | 18.4.0 (主動/被動模式) <br><br>19.2 (主動/主動模式)  | 不相容 |  [手動設定 (主動/被動) ](https://community.cisco.com/t5/networking-documents/how-to-configure-ipsec-vpn-connection-between-cisco-vedge-and/ta-p/3841454)<br><br>[Cloud 通往 configuration (Active/Active) ](https://www.cisco.com/c/en/us/td/docs/routers/sdwan/configuration/Network-Optimization-and-High-Availability/Network-Optimization-High-Availability-book/b_Network-Optimization-and-HA_chapter_00.html) |
| Citrix |NetScaler MPX、SDX、VPX |10.1 和更新版本 |[設定指南](https://docs.citrix.com/en-us/netscaler/11-1/system/cloudbridge-connector-introduction/cloudbridge-connector-azure.html) |不相容 |
| F5 |BIG-IP 系列 |12.0 |[設定指南](https://devcentral.f5.com/articles/connecting-to-windows-azure-with-the-big-ip) |[設定指南](https://devcentral.f5.com/articles/big-ip-to-azure-dynamic-ipsec-tunneling) |
| Fortinet |FortiGate |FortiOS 5.6 |  (未測試)  |[設定指南](https://docs.fortinet.com/document/fortigate/5.6.0/cookbook/255100/ipsec-vpn-to-azure) |
| Hillstone 網路 | 下一代防火牆 (NGFW)  | 5.5 r7  |  (未測試)  | [設定指南](https://www.hillstonenet.com/wp-content/uploads/How-to-setup-Site-to-Site-VPN-between-Microsoft-Azure-and-an-on-premise-Hillstone-Networks-Security-Gateway.pdf) |
| Internet Initiative Japan (IIJ) |SEIL 系列 |SEIL/X 4.60<br>SEIL/B1 4.60<br>SEIL/x86 3.20 |[設定指南](https://www.iij.ad.jp/biz/seil/ConfigAzureSEILVPN.pdf) |不相容 |
| Juniper |SRX |原則式：JunOS 10.2<br>路由式：JunOS 11.4 |支援 |[設定指令碼](vpn-gateway-download-vpndevicescript.md) |
| Juniper |J 系列 |原則式：JunOS 10.4r9<br>路由式：JunOS 11.4 |支援 |[設定指令碼](vpn-gateway-download-vpndevicescript.md) |
| Juniper |ISG |ScreenOS 6.3 |支援 |[設定指令碼](vpn-gateway-download-vpndevicescript.md) |
| Juniper |SSG |ScreenOS 6.2 |支援 |[設定指令碼](vpn-gateway-download-vpndevicescript.md) |
| Juniper |MX |JunOS 12.x|支援 |[設定指令碼](vpn-gateway-download-vpndevicescript.md) |
| Microsoft |路由及遠端存取服務 |Windows Server 2012 |不相容 |支援 |
| 開啟系統 AG |任務控制安全性閘道 |N/A |[設定指南](https://open-systems.com/wp-content/uploads/2019/12/OpenSystems-AzureVPNSetup-Installation-Guide.pdf) |不相容 |
| Palo Alto Networks |所有執行 PAN-OS 的裝置 |PAN-OS<br>原則式：6.1.5 或更新版本<br>路由式：7.1.4 |支援 |[設定指南](https://knowledgebase.paloaltonetworks.com/KCSArticleDetail?id=kA10g000000Cm6WCAS) |
| Sentrium (開發人員)  | VyOS | VyOS 1.2。2 |  (未測試)  | [設定指南 ](https://vyos.readthedocs.io/en/latest/appendix/examples/azure-vpn-bgp.html)|
| ShareTech | 新一代 UTM (NU 系列) | 9.0.1.3 | 不相容 | [設定指南](http://www.sharetech.com.tw/images/file/Solution/NU_UTM/S2S_VPN_with_Azure_Route_Based_en.pdf) |
| SonicWall |TZ 系列、NSA 系列<br>SuperMassive 系列<br>E-Class NSA 系列 |SonicOS 5.8.x<br>SonicOS 5.9.x<br>SonicOS 6.x |不相容 |[設定指南](https://www.sonicwall.com/support/knowledge-base/170505320011694) |
| Sophos | XG 新一代防火牆 | XG v17 |  (未測試)  | [設定指南](https://community.sophos.com/kb/127546)<br><br>[設定指南 - 多個 SA](https://community.sophos.com/kb/en-us/133154) |
| Synology | MR2200ac <br>RT2600ac <br>RT1900ac | SRM 1.1.5/VpnPlusServer-1.2。0 |  (未測試)  | [設定指南](https://www.synology.com/en-global/knowledgebase/SRM/tutorial/VPN/How_to_set_up_Site_to_Site_VPN_between_Synology_Router_and_MS_Azure) |
| Ubiquiti | EdgeRouter | EdgeOS v1.10 |  (未測試)  | [透過 IKEv2/IPsec 的 BGP](https://help.ubnt.com/hc/en-us/articles/115012374708) \(英文\)<br><br>[透過 IKEv2/IPsec 的 VTI](https://help.ubnt.com/hc/en-us/articles/115012305347) \(英文\) |
| Ultra | 3E-636L3 | 5.2.0 T3 組建-13  |  (未測試)  | [設定指南](https://ultra-3eti.com/wp-content/uploads/2020/07/Azure-VPN-636L3-Site-to-Site-Test-Notes.pdf) |
| WatchGuard |全部 |Fireware XTM<br> 原則式：v11.11.x<br>路由式：v11.12.x |[設定指南](http://watchguardsupport.force.com/publicKB?type=KBArticle&SFDCID=kA2F00000000LI7KAM&lang=en_US) |[設定指南](http://watchguardsupport.force.com/publicKB?type=KBArticle&SFDCID=kA22A000000XZogSAG&lang=en_US)|
| Zyxel |ZyWALL USG 系列<br>ZyWALL ATP 系列<br>ZyWALL VPN 系列 | ZLD v 4.32 + |  (未測試)  | [透過 IKEv2/IPsec 的 VTI](https://businessforum.zyxel.com/discussion/2648/) \(英文\)<br><br>[透過 IKEv2/IPsec 的 BGP](https://businessforum.zyxel.com/discussion/2650/) \(英文\)|

> [!NOTE]
>
> (\*) Cisco ASA 8.4 版以上新增了 IKEv2 支援，可使用自訂 IPsec/IKE 原則並搭配 "UsePolicyBasedTrafficSelectors" 選項來連線到 Azure VPN 閘道。 請參閱這篇[操作說明文章](vpn-gateway-connect-multiple-policybased-rm-ps.md)。
>
> (\*\*) ISR 7200 系列路由器僅支援原則式 VPN。

## <a name="download-vpn-device-configuration-scripts-from-azure"></a><a name="configscripts"></a>從 Azure 下載 VPN 裝置設定指令碼

針對特定裝置，您可以直接從 Azure 下載設定指令碼。 如需詳細資訊和下載指示，請參閱[下載 VPN 裝置設定指令碼](vpn-gateway-download-vpndevicescript.md)。

### <a name="devices-with-available-configuration-scripts"></a>具有可用設定指令碼的裝置

[!INCLUDE [scripts](../../includes/vpn-gateway-device-configuration-scripts.md)]

## <a name="non-validated-vpn-devices"></a><a name="additionaldevices"></a>未經驗證的 VPN 裝置

如果沒有看到您的裝置列在「已經驗證的 VPN 裝置」表格中，您的裝置仍然可能可以與站對站連線搭配使用。 如需額外支援和設定指示，請連絡裝置製造商。

## <a name="editing-device-configuration-samples"></a><a name="editing"></a>編輯裝置組態範本

下載提供的 VPN 裝置組態範本之後，您將需要取代其中一些值，以反映您環境的設定。

### <a name="to-edit-a-sample"></a>編輯範本：

1. 使用 [記事本] 開啟範本。
2. 搜尋所有 <文字**> 字串並使用適合您環境的值加以取代。 請務必加上 < 和 >。 當有指定名稱時，您選取的名稱應該是唯一名稱。 如果命令無法運作，請參閱裝置製造商文件。

| **範本中的文字** | **變更為** |
| --- | --- |
| &lt;RP_OnPremisesNetwork&gt; |您為此物件選擇的名稱。 例如：myOnPremisesNetwork |
| &lt;RP_AzureNetwork&gt; |您為此物件選擇的名稱。 例如：myAzureNetwork |
| &lt;RP_AccessList&gt; |您為此物件選擇的名稱。 例如：myAzureAccessList |
| &lt;RP_IPSecTransformSet&gt; |您為此物件選擇的名稱。 例如：myIPSecTransformSet |
| &lt;RP_IPSecCryptoMap&gt; |您為此物件選擇的名稱。 例如：myIPSecCryptoMap |
| &lt;SP_AzureNetworkIpRange&gt; |指定範圍。 例如：192.168.0.0 |
| &lt;SP_AzureNetworkSubnetMask&gt; |指定子網路遮罩。 例如：255.255.0.0 |
| &lt;SP_OnPremisesNetworkIpRange&gt; |指定內部部署範圍。 例如：10.2.1.0 |
| &lt;SP_OnPremisesNetworkSubnetMask&gt; |指定內部部署子網路遮罩。 例如：255.255.255.0 |
| &lt;SP_AzureGatewayIpAddress&gt; |此資訊專屬於您的虛擬網路，位於管理入口網站中做為 **[閘道 IP 位址]**。 |
| &lt;SP_PresharedKey&gt; |此資訊專屬於您的虛擬網路，是 [管理入口網站] 中的管理金鑰。 |

## <a name="ipsecike-parameters"></a><a name="ipsec"></a>IPsec/IKE 參數

> [!IMPORTANT]
> 1. 下面的資料表包含了 Azure VPN 閘道在預設組態中使用的演算法和參數的組合。 對於使用 Azure Resource Management 部署模型所建立的路由式 VPN 閘道，您可以對每個個別的連線指定自訂原則。 如需詳細指示，請參閱[設定 IPsec/IKE 原則](vpn-gateway-ipsecikepolicy-rm-powershell.md)。
>
> 2. 此外，您必須將 TCP **MSS** 固定在 **1350**。 或者，如果您的 VPN 裝置不支援 MSS 固定，您也可以將通道介面上的 **MTU** 改設為 **1400** 位元組。
>

在下列資料表中：

* SA = 安全性關聯
* IKE 階段 1 也稱為「主要模式」
* IKE 階段 2 也稱為「快速模式」

### <a name="ike-phase-1-main-mode-parameters"></a>IKE 階段 1 (主要模式) 參數

| **屬性**          |**PolicyBased**    | **RouteBased**    |
| ---                   | ---               | ---               |
| IKE 版本           |IKEv1              |IKEv1 和 IKEv2    |
| Diffie-Hellman 群組  |群組 2 (1024 位元) |群組 2 (1024 位元) |
| 驗證方法 |預先共用金鑰     |預先共用金鑰     |
| 加密與雜湊演算法 |1. AES256、SHA256<br>2. AES256、SHA1<br>3. AES128、SHA1<br>4. 3DES、SHA1 |1. AES256、SHA1<br>2. AES256、SHA256<br>3. AES128、SHA1<br>4. AES128、SHA256<br>5. 3DES、SHA1<br>6. 3DES、SHA256 |
| SA 存留期           |28,800 秒     |28,800 秒     |

### <a name="ike-phase-2-quick-mode-parameters"></a>IKE 階段 2 (快速模式) 參數

| **屬性**                  |**PolicyBased**| **RouteBased**                              |
| ---                           | ---           | ---                                         |
| IKE 版本                   |IKEv1          |IKEv1 和 IKEv2                              |
| 加密與雜湊演算法 |1. AES256、SHA256<br>2. AES256、SHA1<br>3. AES128、SHA1<br>4. 3DES、SHA1 |[RouteBased QM SA 供應項目](#RouteBasedOffers) |
| SA 存留期 (時間)            |3,600 秒  |27,000 秒                               |
| SA 存留期 (位元組)           |102,400,000 KB |102,400,000 KB                               |
| 完整轉寄密碼 (PFS) |否             |[RouteBased QM SA 供應項目](#RouteBasedOffers) |
| 停用的對等偵測 (DPD)     |不支援  |支援                                    |


### <a name="routebased-vpn-ipsec-security-association-ike-quick-mode-sa-offers"></a><a name ="RouteBasedOffers"></a>RouteBased VPN IPsec 安全性關聯 (IKE 快速模式 SA) 供應項目

下表列出 IPsec SA (IKE 快速模式) 供應項目。 供應項目是依其呈現或被接受的喜好設定順序而列出。

#### <a name="azure-gateway-as-initiator"></a>Azure 閘道器為啟動者

|-  |**加密**|**驗證**|**PFS 群組**|
|---| ---          |---               |---          |
| 1 |GCM AES256    |GCM (AES256)      |無         |
| 2 |AES256        |SHA1              |無         |
| 3 |3DES          |SHA1              |無         |
| 4 |AES256        |SHA256            |無         |
| 5 |AES128        |SHA1              |None         |
| 6 |3DES          |SHA256            |無         |

#### <a name="azure-gateway-as-responder"></a>Azure 閘道器為回應者

|-  |**加密**|**驗證**|**PFS 群組**|
|---| ---          | ---              |---          |
| 1 |GCM AES256    |GCM (AES256)      |無         |
| 2 |AES256        |SHA1              |無         |
| 3 |3DES          |SHA1              |無         |
| 4 |AES256        |SHA256            |無         |
| 5 |AES128        |SHA1              |None         |
| 6 |3DES          |SHA256            |無         |
| 7 |DES           |SHA1              |無         |
| 8 |AES256        |SHA1              |1            |
| 9 |AES256        |SHA1              |2            |
| 10|AES256        |SHA1              |14           |
| 11|AES128        |SHA1              |1            |
| 12|AES128        |SHA1              |2            |
| 13|AES128        |SHA1              |14           |
| 14|3DES          |SHA1              |1            |
| 15|3DES          |SHA1              |2            |
| 16|3DES          |SHA256            |2            |
| 17|AES256        |SHA256            |1            |
| 18|AES256        |SHA256            |2            |
| 19|AES256        |SHA256            |14           |
| 20|AES256        |SHA1              |24           |
| 21|AES256        |SHA256            |24           |
| 22|AES128        |SHA256            |無         |
| 23|AES128        |SHA256            |1            |
| 24|AES128        |SHA256            |2            |
| 25|AES128        |SHA256            |14           |
| 26|3DES          |SHA1              |14           |

* 您可以使用路由式和高效能 VPN 閘道指定 IPsec ESP NULL 加密。 以 Null 為基礎的加密不提供傳輸中資料的保護，應該只用於時需要最大輸送量和最小延遲時。 用戶端可能會選擇在 VNet 對 VNet 通訊案例中，或當加密套用至解決方案中的其他地方時，使用此功能。
* 透過網際網路的跨單位連線，請使用含有加密和雜湊演算法的預設 Azure VPN 閘道設定 (如上表所列)，以確保重要通訊的安全性。

## <a name="known-device-compatibility-issues"></a><a name="known"></a>已知的裝置相容性問題

> [!IMPORTANT]
> 協力廠商 VPN 裝置與 Azure VPN 閘道之間有已知的相容性問題。 Azure 小組正積極與廠商合作來解決這裡所列出的問題。 解決這些問題之後，就會更新此頁面來提供最新資訊。 請定期回來查看。
>
>

### <a name="feb-16-2017"></a>2017 年 2 月 16 日

適用於 Azure 路由式 VPN 但**版本比 7.1.4 舊的 Palo Alto Networks 裝置**：如果您使用來自 Palo Alto Networks、PAN-OS 版本比 7.1.4 舊的 VPN 裝置，而在連線到 Azure 路由式 VPN 閘道時發生問題，請執行下列步驟：

1. 檢查您 Palo Alto Networks 裝置的韌體版本。 如果您的 PAN-OS 版本比 7.1.4 舊，請升級至 7.1.4。
2. 在 Palo Alto Networks 裝置上，於連線到 Azure VPN 閘道時，將 [Phase 2 SA (第 2 階段 SA)] \(或 [Quick Mode SA (快速模式 SA)]) 存留期變更為 28,800 秒 (8 小時)。
3. 如果您仍然遇到連線問題，請從 Azure 入口網站開啟支援要求。
