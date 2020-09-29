---
title: 將虛擬網路閘道連接到 Azure 虛擬 WAN
description: 本文可協助您將 Azure 虛擬網路閘道連線到 Azure 虛擬 WAN VPN 閘道
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 29f5b549bd5f5dbc421487739bb1eb8c7f120bb0
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91441024"
---
# <a name="connect-a-vpn-gateway-virtual-network-gateway-to-virtual-wan"></a>將 VPN 閘道 (虛擬網路閘道) 連線到虛擬 WAN

本文可協助您設定從 Azure VPN 閘道 (虛擬網路閘道) 到 Azure 虛擬 WAN (VPN 閘道) 之間的連線能力。 從 VPN 閘道建立連線， (虛擬網路閘道) 至虛擬 WAN (VPN 閘道) 類似于設定從分支 VPN 網站連線到虛擬 WAN 的連接。

為了將兩項功能之間可能的混淆降至最低，我們會在閘道前面加上我們所指的功能名稱。 例如，VPN 閘道虛擬網路閘道和虛擬 WAN VPN 閘道。

## <a name="before-you-begin"></a>開始之前

開始之前，請先建立下列資源：

Azure 虛擬 WAN

* [建立虛擬 WAN](virtual-wan-site-to-site-portal.md#openvwan)。
* [建立中樞](virtual-wan-site-to-site-portal.md#hub)。 虛擬中樞包含虛擬 WAN VPN 閘道。

Azure 虛擬網路

* 建立沒有任何虛擬網路閘道的虛擬網路。 驗證沒有任何內部部署網路的子網路與您要連線的虛擬網路子網路重疊。 若要在 Azure 入口網站中建立虛擬網路，請參閱[快速入門](../virtual-network/quick-create-portal.md)。

## <a name="1-create-a-vpn-gateway-virtual-network-gateway"></a><a name="vnetgw"></a>1. 建立 VPN 閘道虛擬網路閘道

以主動-主動模式為您的虛擬網路建立 **VPN 網** 關虛擬網路閘道。 當您建立閘道時，您可以將現有的公用 IP 位址用於閘道的兩個實例，也可以建立新的公用 IP。 您將在設定虛擬 WAN 網站時使用這些公用 Ip。 如需主動-主動 VPN 閘道和設定步驟的詳細資訊，請參閱 [設定主動-主動 vpn 閘道](../vpn-gateway/vpn-gateway-activeactive-rm-powershell.md#aagateway)。

### <a name="active-active-mode-setting"></a><a name="active-active"></a>主動-主動模式設定

在 [虛擬 **網路閘道** 設定] 頁面上，啟用主動-主動模式。

![主動-主動](./media/connect-virtual-network-gateway-vwan/active.png "主動-主動")

### <a name="bgp-setting"></a><a name="BGP"></a>BGP 設定

在 [虛擬 **網路閘道** 設定] 頁面上，您可以設定 **BGP ASN**。 變更 BGP ASN。 BGP ASN 不能是65515。 Azure 虛擬 WAN 將使用66515。

![螢幕擷取畫面顯示已選取設定 BGP ASN 的虛擬網路閘道設定頁面。](./media/connect-virtual-network-gateway-vwan/bgp.png "Bgp")

### <a name="public-ip-addresses"></a><a name="pip"></a>公用 IP 位址

建立閘道時，流覽至 [ **屬性** ] 頁面。 屬性和設定將會類似下列範例。 請注意用於閘道的兩個公用 IP 位址。

![properties](./media/connect-virtual-network-gateway-vwan/publicip.png "properties")

## <a name="2-create-virtual-wan-vpn-sites"></a><a name="vwansite"></a>2. 建立虛擬 WAN VPN 網站

若要建立虛擬 WAN VPN 網站，請流覽至您的虛擬 WAN，並在 [連線 **能力**] 底下選取 [ **VPN 網站**]。 在本節中，您將建立兩個對應至您在上一節中建立之虛擬網路閘道的虛擬 WAN VPN 網站。

1. 選取 [ **+ 建立網站**]。
2. 在 [ **建立 VPN 網站** ] 頁面上，輸入下列值：

   * **區域** -與 Azure VPN 閘道虛擬網路閘道相同的區域。
   * **裝置廠商** -輸入裝置廠商 (任何名稱) 。
   * **私人位址空間** -輸入值，或在啟用 BGP 時保留空白。
   * **邊界閘道協定** -如果 Azure VPN 閘道虛擬網路閘道已啟用 BGP，則設定為 **啟用** 。
   * **連接到中樞** -從下拉式清單中選取您在必要條件中建立的中樞。 如果您沒有看到中樞，請確認您已建立中樞的站對站 VPN 閘道。
3. 在 [ **連結**] 底下，輸入下列值：

   * **提供者名稱** -輸入連結名稱和提供者名稱， (任何名稱) 。
   * **加速** (任何數位) 。
   * **Ip 位址** -輸入 ip 位址 (與 (VPN 閘道) 虛擬網路閘道屬性) 下所顯示的第一個公用 ip 位址相同。
   * **Bgp 位址** 和 **asn** -bgp 位址和 asn。 這些必須與其中一個 BGP 對等 IP 位址相同，以及您在 [步驟 1](#vnetgw)中設定的 VPN 閘道虛擬網路閘道的 ASN。
4. 檢查並選取 [ **確認** ] 以建立網站。
5. 重複先前的步驟，以建立第二個網站，使其符合 VPN 閘道虛擬網路閘道的第二個實例。 除了使用第二個公用 IP 位址和來自 VPN 閘道設定的第二個 BGP 對等 IP 位址之外，您將會保留相同的設定。
6. 您現在已成功布建兩個網站，而且可以繼續進行下一節以下載設定檔。

## <a name="3-download-the-vpn-configuration-files"></a><a name="downloadconfig"></a>3. 下載 VPN 設定檔

在本節中，您會針對您在上一節中建立的每個網站下載 VPN 設定檔。

1. 在 [虛擬 WAN **VPN 網站** ] 頁面的頂端，選取 [ **網站**]，然後選取 [ **下載站對站 VPN**設定]。 Azure 會建立含有設定的設定檔。

   ![顯示 [VPN 網站] 頁面的螢幕擷取畫面，其中已選取 [下載站對站 VPN 設定] 動作。](./media/connect-virtual-network-gateway-vwan/download.png "下載這篇文章")
2. 下載並開啟設定檔。
3. 針對第二個網站重複這些步驟。 當您開啟這兩個設定檔之後，您可以繼續進行下一節。

## <a name="4-create-the-local-network-gateways"></a><a name="createlocalgateways"></a>4. 建立局域網路閘道

在本節中，您會建立兩個 Azure VPN 閘道局域網路閘道。 上一個步驟中的設定檔包含閘道設定。 使用這些設定來建立及設定 Azure VPN 閘道局域網路閘道。

1. 使用這些設定來建立局域網路閘道。 如需有關如何建立 VPN 閘道局域網路閘道的詳細資訊，請參閱 VPN 閘道文章 [建立區域網路網](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway)關。

   * **IP 位址** -使用從設定檔中針對 *gatewayconfiguration* 所顯示的 Instance0 IP 位址。
   * **Bgp** -如果連線是透過 bgp，請選取 [ **設定 bgp 設定** ]，然後輸入 ASN ' 65515 '。 輸入 BGP 對等 IP 位址。 從設定檔使用 ' Instance0 BgpPeeringAddresses ' 進行 *gatewayconfiguration* 。
   * **訂用帳戶、資源群組和位置** 與虛擬 WAN 中樞的訂用帳戶、資源群組和位置相同。
2. 檢查和建立局域網路閘道。 您的局域網路閘道看起來應該類似此範例。

   ![顯示 [設定] 頁面的螢幕擷取畫面，其中已反白顯示 IP 位址，並已選取 [設定 BGP 設定]。](./media/connect-virtual-network-gateway-vwan/lng1.png "instance0")
3. 重複這些步驟來建立另一個局域網路閘道，但這次請使用 ' Instance1 ' 值，而不是設定檔中的 ' Instance0 ' 值。

   ![下載設定檔](./media/connect-virtual-network-gateway-vwan/lng2.png "instance1")

## <a name="5-create-connections"></a><a name="createlocalgateways"></a>5. 建立連接

在本節中，您會建立 VPN 閘道局域網路閘道與虛擬網路閘道之間的連線。 如需有關如何建立 VPN 閘道連線的步驟，請參閱 [設定連接](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#CreateConnection)。

1. 在入口網站中，流覽至您的虛擬網路閘道， **然後按一下 [** 連線]。 在 [連線] 頁面的頂部，按一下 [+新增]**** 以開啟 [新增連線]**** 頁面。
2. 在 [ **加入連接** ] 頁面上，為您的連接設定下列值：

   * **名稱：** 為您的連線命名。
   * **連線類型：** 選取 [ **站對站] (IPSec) **
   * **虛擬網路閘道：** 因為您正從此閘道連線，所以值是固定的。
   * **局域網路閘道：** 此連接會將虛擬網路閘道連接到局域網路閘道。 選擇您稍早建立的其中一個局域網路閘道。
   * **共用金鑰：** 輸入共用金鑰。
   * **IKE 通訊協定：** 選擇 IKE 通訊協定。
3. 按一下 [確定] **** 來建立連線。
4. 您可以在虛擬網路閘道 **的 [連線** ] 頁面中查看連接。

   ![[連接]](./media/connect-virtual-network-gateway-vwan/connect.png "連接")
5. 重複上述步驟來建立第二個連接。 針對第二個連接，請選取您所建立的其他局域網路閘道。
6. 如果連線是透過 BGP，則在建立連線之後，請流覽至連線， **然後選取 [** 設定]。 **在 [設定**] 頁面上，針對 [ **BGP**] 選取 [**已啟用**]。 然後按一下 [ **儲存**]。 針對第二個連接重複執行。

## <a name="6-test-connections"></a><a name="test"></a>6. 測試連接

您可以藉由建立兩個虛擬機器（一個位於 VPN 閘道虛擬網路閘道的一端，另一個位於虛擬 WAN 的虛擬網路中）來測試連線能力，然後再偵測兩部虛擬機器。

1. 在虛擬網路中建立虛擬機器 (Test1-VNet) 適用于 Azure VPN 閘道 (Test1->VNG-AZURE-VNET-1) 。 請勿在 GatewaySubnet 中建立虛擬機器。
2. 建立另一個虛擬網路以連線到虛擬 WAN。 在此虛擬網路的子網中建立虛擬機器。 此虛擬網路不能包含任何虛擬網路閘道。 您可以使用 [站對站連接](virtual-wan-site-to-site-portal.md#vnet) 文章中的 PowerShell 步驟，快速建立虛擬網路。 執行 Cmdlet 之前，請務必變更這些值。
3. 將 VNet 連線到虛擬 WAN 中樞。 在虛擬 WAN 的頁面上，選取 [ **虛擬網路**連線]，然後選取 [ **+ 新增連接**]。 在 [新增連線] 頁面上，填寫下列欄位︰

    * **名稱** - 為您的連線命名。
    * **中樞** - 選取要與此連線產生關聯的中樞。
    * **訂用帳戶** - 請確認訂用帳戶。
    * **虛擬網路** - 選取要與此中樞連線的虛擬網路。 虛擬網路不能有現有的虛擬網路閘道。
4. 按一下 [確定] 以建立虛擬網路連線。
5. Vm 之間現在已設定連線。 除非有任何防火牆或其他原則封鎖通訊，否則您應該能夠 ping 另一部 VM。

## <a name="next-steps"></a>後續步驟

如需設定自訂 IPsec 原則的步驟，請參閱 [設定虛擬 WAN 的自訂 ipsec 原則](virtual-wan-custom-ipsec-portal.md)。
如需虛擬 WAN 的詳細資訊，請參閱[關於 Azure 虛擬 WAN](virtual-wan-about.md) 和 [Azure 虛擬 WAN 常見問題集](virtual-wan-faq.md)。
