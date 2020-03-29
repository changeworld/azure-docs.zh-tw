---
title: 將虛擬網路閘道連接到 Azure 虛擬 WAN
description: 本文可説明您將 Azure 虛擬網路閘道連接到 Azure 虛擬 WAN VPN 閘道
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: cherylmc
ms.openlocfilehash: 688183bc07aa14d5e5df182d7de0897cec93f0b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066241"
---
# <a name="connect-a-vpn-gateway-virtual-network-gateway-to-virtual-wan"></a>將 VPN 閘道（虛擬網路閘道）連接到虛擬廣域網路

本文可説明您設置從 Azure VPN 閘道（虛擬網路閘道）到 Azure 虛擬 WAN（VPN 閘道）的連接。 從 VPN 閘道（虛擬網路閘道）創建到虛擬 WAN （VPN 閘道）的連接類似于從分支 VPN 網站設置到虛擬 WAN 的連接。

為了儘量減少兩個功能之間的可能混淆，我們將以我們所指的功能的名稱來介紹閘道。 例如，VPN 閘道虛擬網路閘道和虛擬 WAN VPN 閘道。

## <a name="before-you-begin"></a>開始之前

開始之前，請創建以下資源：

Azure 虛擬 WAN

* [創建虛擬 WAN。](virtual-wan-site-to-site-portal.md#openvwan)
* [創建中心](virtual-wan-site-to-site-portal.md#hub)。 虛擬中心包含虛擬 WAN VPN 閘道。

Azure 虛擬網路

* 創建沒有任何虛擬網路閘道的虛擬網路。 驗證沒有任何內部部署網路的子網路與您要連線的虛擬網路子網路重疊。 若要在 Azure 入口網站中建立虛擬網路，請參閱[快速入門](../virtual-network/quick-create-portal.md)。

## <a name="1-create-an-azure-virtual-network-gateway"></a><a name="vnetgw"></a>1. 創建 Azure 虛擬網路閘道

為虛擬網路在主動-主動模式下為虛擬網路創建 VPN 閘道虛擬網路閘道。 創建閘道時，可以為閘道的兩個實例使用現有的公共 IP 位址，也可以創建新的公共 IP。 在設置虛擬 WAN 網站時，可以使用這些公共 IP。 有關活動-活動模式的詳細資訊，請參閱[配置活動-活動連接](../vpn-gateway/vpn-gateway-activeactive-rm-powershell.md#aagateway)。

### <a name="active-active-mode-setting"></a><a name="active-active"></a>主動-主動模式設置

![主動活動](./media/connect-virtual-network-gateway-vwan/active.png "主動-主動")

### <a name="bgp-setting"></a><a name="BGP"></a>BGP 設置

BGP ASN 不能為 65515。 66515 將由 Azure 虛擬 WAN 使用。

![Bgp](./media/connect-virtual-network-gateway-vwan/bgp.png "Bgp")

### <a name="public-ip-addresses"></a><a name="pip"></a>公共 IP 位址

創建閘道時，導航到 **"屬性"** 頁。 屬性和配置設置將類似于以下示例。 請注意用於閘道的兩個公共 IP 位址。

![性能](./media/connect-virtual-network-gateway-vwan/publicip.png "properties")

## <a name="2-create-virtual-wan-vpn-sites"></a><a name="vwansite"></a>2. 創建虛擬廣域網路 VPN 網站

要創建虛擬 WAN VPN 網站，請導航到虛擬 WAN，並在 **"連接"** 下選擇**VPN 網站**。 在本節中，您將創建兩個虛擬 WAN VPN 網站，它們對應于您在上一節中創建的虛擬網路閘道。

1. 選擇 **"創建網站**"。
2. 在 **"創建 VPN 網站"** 頁上，鍵入以下值：

   * **區域**- （與 Azure VPN 閘道虛擬網路閘道相同的區域）
   * **設備供應商**- 輸入裝置供應商（任何名稱）
   * **專用位址空間**- （輸入值，或在啟用 BGP 時留空）
   * **邊界閘道協定**- （如果 Azure VPN 閘道虛擬網路閘道啟用 BGP，則設置為**啟用**）
   * **連接到集線器**（從下拉清單中選擇在必備條件中創建的集線器）
3. 在**連結下**，輸入以下值：

   * **提供程式名稱**- 輸入連結名稱和提供程式名稱（任何名稱）
   * **速度**- 速度（任一數字）
   * **IP 位址**- 輸入 IP 位址（與 （VPN 閘道） 虛擬網路閘道屬性下顯示的第一個公共 IP 位址相同）
   * **BGP 位址**和**ASN** - BGP 位址和 ASN。 這些位址必須與 BGP 對等 IP 位址之一相同，並且從[您在步驟 1](#vnetgw)中配置的 VPN 閘道虛擬網路閘道中的 ASN 相同。
4. 查看並選擇 **"確認"** 以創建網站。
5. 重複前面的步驟以創建第二個網站以匹配 VPN 閘道虛擬網路閘道的第二個實例。 除了使用 VPN 閘道配置的第二個公共 IP 位址和第二個 BGP 對等 IP 位址外，您將保留相同的設置。
6. 現在，您已成功預配了兩個網站，可以轉到下一節以下載設定檔。

## <a name="3-download-the-vpn-configuration-files"></a><a name="downloadconfig"></a>3. 下載 VPN 設定檔

在本節中，您可以下載上一節中創建的每個網站的 VPN 設定檔。

1. 在虛擬 WAN VPN**網站頁面**的頂部，選擇 **"網站**"，然後選擇 **"下載網站到網站 VPN"配置**。 Azure 會創建具有設置的設定檔。

   ![下載設定檔](./media/connect-virtual-network-gateway-vwan/download.png "下載這篇文章")
2. 下載並打開設定檔。
3. 對第二個網站重複這些步驟。 打開兩個設定檔後，可以繼續下一節。

## <a name="4-create-the-local-network-gateways"></a><a name="createlocalgateways"></a>4. 創建本地網路閘道

在本節中，您將創建兩個 Azure VPN 閘道本地網路閘道。 上一步的設定檔包含閘道配置設置。 使用這些設置創建和配置 Azure VPN 閘道本地網路閘道。

1. 使用這些設置創建本地網路閘道。 有關如何創建 VPN 閘道本地網路閘道的資訊，請參閱 VPN 閘道文章["創建本地網路閘道](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway)"。

   * **IP 位址**- 使用從設定檔中顯示的閘道*配置*的實例0 IP 位址。
   * **BGP** - 如果連接超過 BGP，請選擇 **"配置 BGP 設置**"並輸入 ASN "65515"。 輸入 BGP 對等 IP 位址。 使用"實例0 Bgp對等位址"從設定檔的*閘道配置*。
   * **訂閱、資源組和位置**與虛擬廣域網路中心相同。
2. 查看並創建本地網路閘道。 本地網路閘道應與此示例類似。

   ![下載設定檔](./media/connect-virtual-network-gateway-vwan/lng1.png "實例0")
3. 重複這些步驟以創建另一個本地網路閘道，但這次使用設定檔中的"實例1"值而不是"實例0"值。

   ![下載設定檔](./media/connect-virtual-network-gateway-vwan/lng2.png "instance1")

## <a name="5-create-connections"></a><a name="createlocalgateways"></a>5. 創建連接

在本節中，您將在 VPN 閘道本地網路閘道和虛擬網路閘道之間創建連接。 有關如何創建 VPN 閘道連接的步驟，請參閱[配置連接](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#CreateConnection)。

1. 在門戶中，導航到虛擬網路閘道，然後按一下"**連接**"。 在 [連線] 頁面的頂部，按一下 [+新增]**** 以開啟 [新增連線]**** 頁面。
2. 在 **"添加連接**"頁上，為連接配置以下值：

   * **名稱：** 為您的連線命名。
   * **連線類型：** 選擇**網站到網站 （IPSec）**
   * **虛擬網路閘道：** 因為您正從此閘道連線，所以值是固定的。
   * **本地網路閘道：** 此連接將虛擬網路閘道連接到本地網路閘道。 選擇您之前創建的本地網路閘道之一。
   * **共用金鑰：** 輸入共用金鑰。
   * **IKE 協定：** 選擇 IKE 協定。
   * **BGP：** 如果連接超過**BGP，請選擇啟用 BGP。**
3. 按一下 [確定] **** 來建立連線。
4. 您可以在虛擬網路閘道的 **"連接"** 頁中查看連接。

   ![連接](./media/connect-virtual-network-gateway-vwan/connect.png "連接")
5. 重複上述步驟以創建第二個連接。 對於第二個連接，請選擇您創建的其他本地網路閘道。

## <a name="6-test-connections"></a><a name="test"></a>6. 測試連接

您可以通過創建兩個虛擬機器（一個位於 VPN 閘道虛擬網路閘道側，另一個在虛擬 WAN 的虛擬網路中）來測試連接，然後 ping 兩個虛擬機器。

1. 在虛擬網路 （Test1-VNet） 中為 Azure VPN 閘道 （Test1-VNG） 創建虛擬機器。 請勿在閘道子網中創建虛擬機器。
2. 創建另一個虛擬網路以連接到虛擬廣域網路。 在此虛擬網路的子網中創建虛擬機器。 此虛擬網路不能包含任何虛擬網路閘道。 您可以使用[網站到網站連接](virtual-wan-site-to-site-portal.md#vnet)文章中的 PowerShell 步驟快速建立虛擬網路。 在運行 Cmdlet 之前，請確保更改值。
3. 將 VNet 連接到虛擬廣域網路中心。 在虛擬 WAN 的頁面上，選擇**虛擬網路連接**，然後 **[添加連接**。 在 [新增連線]**** 頁面上，填寫下列欄位︰

    * **名稱** - 為您的連線命名。
    * **中樞** - 選取要與此連線產生關聯的中樞。
    * **訂用帳戶** - 請確認訂用帳戶。
    * **虛擬網路** - 選取要與此中樞連線的虛擬網路。 虛擬網路不能有現有的虛擬網路閘道。
4. 按一下 [確定]**** 以建立虛擬網路連線。
5. 現在，VM 之間已設置連接。 您應該能夠從另一個 VM ping 一個 VM，除非有任何防火牆或其他策略阻止通信。

## <a name="next-steps"></a>後續步驟

有關配置自訂 IPsec 策略的步驟，請參閱[為虛擬 WAN 配置自訂 IPsec 策略](virtual-wan-custom-ipsec-portal.md)。
如需虛擬 WAN 的詳細資訊，請參閱[關於 Azure 虛擬 WAN](virtual-wan-about.md) 和 [Azure 虛擬 WAN 常見問題集](virtual-wan-faq.md)。