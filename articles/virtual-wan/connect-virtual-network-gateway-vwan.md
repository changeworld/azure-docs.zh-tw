---
title: 將虛擬網路閘道連線至 Azure 虛擬 WAN
description: 本文可協助您將 Azure 虛擬網路閘道連線至 Azure 虛擬 WAN VPN 閘道
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 07/28/2020
ms.author: cherylmc
ms.openlocfilehash: 9d94904e580cefb53b2c71d21259bebfc07c1ad6
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2020
ms.locfileid: "87431279"
---
# <a name="connect-a-vpn-gateway-virtual-network-gateway-to-virtual-wan"></a>將 VPN 閘道（虛擬網路閘道）連線到虛擬 WAN

本文可協助您設定從 Azure VPN 閘道（虛擬網路閘道）到 Azure 虛擬 WAN （VPN 閘道）的連線能力。 建立從 VPN 閘道（虛擬網路閘道）到虛擬 WAN （VPN 閘道）的連線，類似于設定從分支 VPN 網站連線至虛擬 WAN 的連線。

為了儘量減少兩個功能之間可能的混淆，我們會在閘道前面加上我們所參考的功能名稱。 例如，VPN 閘道虛擬網路閘道，以及虛擬 WAN VPN 閘道。

## <a name="before-you-begin"></a>開始之前

開始之前，請先建立下列資源：

Azure 虛擬 WAN

* [建立虛擬 WAN](virtual-wan-site-to-site-portal.md#openvwan)。
* [建立中樞](virtual-wan-site-to-site-portal.md#hub)。 虛擬中樞包含虛擬 WAN VPN 閘道。

Azure 虛擬網路

* 建立不含任何虛擬網路閘道的虛擬網路。 驗證沒有任何內部部署網路的子網路與您要連線的虛擬網路子網路重疊。 若要在 Azure 入口網站中建立虛擬網路，請參閱[快速入門](../virtual-network/quick-create-portal.md)。

## <a name="1-create-a-vpn-gateway-virtual-network-gateway"></a><a name="vnetgw"></a>1. 建立 VPN 閘道虛擬網路閘道

針對您的虛擬網路，建立主動-主動模式的**VPN 閘道**虛擬網路閘道。 當您建立閘道時，您可以針對閘道的兩個實例使用現有的公用 IP 位址，也可以建立新的公用 IP。 設定虛擬 WAN 網站時，您將會使用這些公用 Ip。 如需主動-主動 VPN 閘道和設定步驟的詳細資訊，請參閱[設定主動-主動 vpn 閘道](../vpn-gateway/vpn-gateway-activeactive-rm-powershell.md#aagateway)。

### <a name="active-active-mode-setting"></a><a name="active-active"></a>主動-主動模式設定

在 [虛擬**網路閘道**設定] 頁面上，啟用主動-主動模式。

![主動-主動](./media/connect-virtual-network-gateway-vwan/active.png "主動-主動")

### <a name="bgp-setting"></a><a name="BGP"></a>BGP 設定

在 [虛擬**網路閘道**設定] 頁面上，您可以設定**BGP ASN**。 變更 BGP ASN。 BGP ASN 不能是65515。 Azure 虛擬 WAN 將使用66515。

![BGP](./media/connect-virtual-network-gateway-vwan/bgp.png "bgp")

### <a name="public-ip-addresses"></a><a name="pip"></a>公用 IP 位址

建立閘道時，流覽至 [**屬性**] 頁面。 屬性和設定會如下列範例所示。 請注意閘道所使用的兩個公用 IP 位址。

![properties](./media/connect-virtual-network-gateway-vwan/publicip.png "properties")

## <a name="2-create-virtual-wan-vpn-sites"></a><a name="vwansite"></a>2. 建立虛擬 WAN VPN 網站

若要建立虛擬 WAN VPN 網站，請流覽至您的虛擬 WAN，然後在 [連線**能力**] 底下，選取 [ **VPN 網站**]。 在本節中，您將建立兩個對應至您在上一節中建立之虛擬網路閘道的虛擬 WAN VPN 網站。

1. 選取 [ **+ 建立網站**]。
2. 在 [**建立 VPN 網站**] 頁面上，輸入下列值：

   * **區域**-與 Azure VPN 閘道虛擬網路閘道相同的區域。
   * **裝置廠商**-輸入裝置廠商（任何名稱）。
   * **私人位址空間**-輸入值，或在啟用 BGP 時保留空白。
   * **邊界閘道協定**-如果 Azure VPN 閘道虛擬網路閘道已啟用 BGP，則設定為 [**啟用**]。
   * 連線**到中樞**-從下拉式清單中選取您在必要條件中建立的中樞。 如果您沒有看到中樞，請確認您已為中樞建立站對站 VPN 閘道。
3. 在 [**連結**] 底下，輸入下列值：

   * **提供者名稱**-輸入連結名稱和提供者名稱（任何名稱）。
   * **速度**-速度（任何數位）。
   * **Ip 位址**-輸入 ip 位址（與 [（VPN 閘道）虛擬網路閘道屬性] 底下顯示的第一個公用 IP 位址相同）。
   * **Bgp 位址**和**asn** -bgp 位址和 asn。 這些必須與其中一個 BGP 對等互連 IP 位址，以及您在[步驟 1](#vnetgw)中設定的 VPN 閘道虛擬網路閘道的 ASN 相同。
4. 檢查並選取 [**確認**] 以建立網站。
5. 重複上述步驟來建立第二個網站，以符合 VPN 閘道虛擬網路閘道的第二個實例。 除了使用第二個公用 IP 位址和第二個 VPN 閘道設定的 BGP 對等 IP 位址以外，您會保留相同的設定。
6. 您現在已成功布建兩個網站，而且可以繼續進行下一節以下載設定檔。

## <a name="3-download-the-vpn-configuration-files"></a><a name="downloadconfig"></a>3. 下載 VPN 設定檔

在本節中，您會下載您在上一節中建立之每個網站的 VPN 設定檔。

1. 在 [虛擬 WAN **vpn 網站**] 頁面的頂端，選取**網站**，然後選取 [**下載站對站 VPN**設定]。 Azure 會建立具有設定的設定檔。

   ![下載設定檔](./media/connect-virtual-network-gateway-vwan/download.png "下載這篇文章")
2. 下載並開啟設定檔。
3. 針對第二個網站重複這些步驟。 當您開啟兩個設定檔時，您可以繼續進行下一節。

## <a name="4-create-the-local-network-gateways"></a><a name="createlocalgateways"></a>4. 建立局域網路閘道

在本節中，您會建立兩個 Azure VPN 閘道局域網路閘道。 上一個步驟中的設定檔包含閘道設定。 使用這些設定來建立及設定 Azure VPN 閘道局域網路閘道。

1. 使用這些設定來建立局域網路閘道。 如需如何建立 VPN 閘道局域網路閘道的相關資訊，請參閱 VPN 閘道文章[建立局域網路閘道](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway)。

   * **IP 位址**-使用設定檔中針對*gatewayconfiguration*所顯示的 Instance0 IP 位址。
   * **BGP** -如果連線是透過 bgp，請選取 [**設定 bgp 設定**]，然後輸入 ASN ' 65515 '。 輸入 BGP 對等 IP 位址。 將 ' Instance0 BgpPeeringAddresses ' 用於*gatewayconfiguration*的設定檔。
   * [訂用帳戶] **、[資源群組] 和 [位置**] 與 [虛擬 WAN 中樞] 相同。
2. 檢查並建立局域網路閘道。 您的局域網路閘道看起來應該類似此範例。

   ![下載設定檔](./media/connect-virtual-network-gateway-vwan/lng1.png "instance0")
3. 重複這些步驟來建立另一個局域網路閘道，但這次使用的是 ' Instance1 ' 值，而不是設定檔中的 ' Instance0 ' 值。

   ![下載設定檔](./media/connect-virtual-network-gateway-vwan/lng2.png "instance1")

## <a name="5-create-connections"></a><a name="createlocalgateways"></a>5. 建立連接

在本節中，您會建立 VPN 閘道局域網路閘道與虛擬網路閘道之間的連線。 如需如何建立 VPN 閘道連線的步驟，請參閱[設定連接](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#CreateConnection)。

1. 在入口網站中，流覽至您的虛擬網路閘道，然後按一下 [**連接**]。 在 [連線] 頁面的頂部，按一下 [+新增]**** 以開啟 [新增連線]**** 頁面。
2. 在 [**新增連接**] 頁面上，為您的連線設定下列值：

   * **名稱：** 為您的連線命名。
   * **連線類型：** 選取**站對站（IPSec）**
   * **虛擬網路閘道：** 因為您正從此閘道連線，所以值是固定的。
   * **局域網路閘道：** 此連線會將虛擬網路閘道連接到局域網路閘道。 選擇您稍早建立的其中一個局域網路閘道。
   * **共用金鑰：** 輸入共用金鑰。
   * **IKE 通訊協定：** 選擇 [IKE] 通訊協定。
3. 按一下 [確定] **** 來建立連線。
4. 您可以在虛擬網路閘道**的 [連線**] 頁面中，查看連接。

   ![[連接]](./media/connect-virtual-network-gateway-vwan/connect.png "連接")
5. 重複上述步驟以建立第二個連接。 針對第二個連接，請選取您所建立的其他局域網路閘道。
6. 如果連線是透過 BGP，在您建立連線之後，請流覽至連線**並選取 [** 設定]。 **在 [設定**] 頁面上，針對 [ **BGP**] 選取 [**已啟用**]。 然後按一下 [ **儲存**]。 針對第二個連接重複此步驟。

## <a name="6-test-connections"></a><a name="test"></a>6. 測試連接

您可以藉由建立兩部虛擬機器（VPN 閘道虛擬網路閘道的一端，另一個位於虛擬 WAN 的虛擬網路中），然後 ping 兩部虛擬機器，來測試連線能力。

1. 在 Azure VPN 閘道（Test1-VNG）的虛擬網路（Test1-VNet）中建立虛擬機器。 請勿在 GatewaySubnet 中建立虛擬機器。
2. 建立另一個虛擬網路以連線至虛擬 WAN。 在此虛擬網路的子網中建立虛擬機器。 此虛擬網路不能包含任何虛擬網路閘道。 您可以使用[站對站](virtual-wan-site-to-site-portal.md#vnet)連線一文中的 PowerShell 步驟，快速建立虛擬網路。 執行 Cmdlet 之前，請務必變更這些值。
3. 將 VNet 連線至虛擬 WAN 中樞。 在您的虛擬 WAN 頁面上，依序選取 [**虛擬網路**連線] 和 [ **+ 新增**連線]。 在 [新增連線] 頁面上，填寫下列欄位︰

    * **名稱** - 為您的連線命名。
    * **中樞** - 選取要與此連線產生關聯的中樞。
    * **訂用帳戶** - 請確認訂用帳戶。
    * **虛擬網路** - 選取要與此中樞連線的虛擬網路。 虛擬網路不能有現有的虛擬網路閘道。
4. 按一下 [確定] 以建立虛擬網路連線。
5. 現在 Vm 之間已設定連線。 您應該能夠 ping 另一個 VM，除非有任何防火牆或其他原則封鎖通訊。

## <a name="next-steps"></a>後續步驟

如需設定自訂 IPsec 原則的步驟，請參閱[設定適用于虛擬 WAN 的自訂 ipsec 原則](virtual-wan-custom-ipsec-portal.md)。
如需虛擬 WAN 的詳細資訊，請參閱[關於 Azure 虛擬 WAN](virtual-wan-about.md) 和 [Azure 虛擬 WAN 常見問題集](virtual-wan-faq.md)。
