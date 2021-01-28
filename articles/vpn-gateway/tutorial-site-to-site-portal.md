---
title: 教學課程 - 將內部部署網路連線至虛擬網路：Azure 入口網站
description: 使用入口網站透過公用網際網路建立從內部部署網路到 Azure 虛擬網路的站對站 VPN 閘道 IPsec 連線。
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.author: cherylmc
ms.service: vpn-gateway
ms.topic: tutorial
ms.date: 12/04/2020
ms.openlocfilehash: ccb43c3e7efb9289450ad9a71c003f54e5362b66
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945205"
---
# <a name="tutorial-create-a-site-to-site-connection-in-the-azure-portal"></a>教學課程：在 Azure 入口網站中建立站對站連線

Azure VPN 閘道提供客戶組織內部與 Azure 之間的跨單位連線。 本教學課程說明如何使用 Azure 入口網站建立從內部部署網路到 VNet 的站對站 VPN 閘道連線。 您也可以使用 [Azure PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md) 或 [Azure CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md) 來建立此設定。

:::image type="content" source="./media/tutorial-site-to-site-portal/diagram.png" alt-text="站對站 VPN 閘道跨單位連線圖表":::

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立虛擬網路
> * 建立 VPN 閘道
> * 建立區域網路閘道
> * 建立 VPN 連線
> * 驗證連線
> * 連接到虛擬機器

## <a name="prerequisites"></a>必要條件

* 具有有效訂用帳戶的 Azure 帳戶。 如果您沒有帳戶，請[免費建立一個](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
* 確定您有相容的 VPN 裝置以及能夠對其進行設定的人員。 如需相容 VPN 裝置和裝置組態的詳細資訊，請參閱[關於 VPN 裝置](vpn-gateway-about-vpn-devices.md)。
* 確認您的 VPN 裝置有對外開放的公用 IPv4 位址。
* 如果您不熟悉位於內部部署網路組態的 IP 位址範圍，您需要與能夠提供那些詳細資料的人協調。 當您建立此組態時，您必須指定 IP 位址範圍的首碼，以供 Azure 路由傳送至您的內部部署位置。 內部部署網路的子網路皆不得與您所要連線的虛擬網路子網路重疊。

## <a name="create-a-virtual-network"></a><a name="CreatVNet"></a>建立虛擬網路

使用下列值建立虛擬網路 (VNet)：

* **資源群組：** TestRG1
* **名稱：** VNet1
* **區域：** (美國) 美國東部
* **IPv4 位址空間：** 10.1.0.0/16
* **子網路名稱：** FrontEnd
* **子網路位址空間：** 10.1.0.0/24

[!INCLUDE [About cross-premises addresses](../../includes/vpn-gateway-cross-premises.md)]

[!INCLUDE [Create a virtual network](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="create-a-vpn-gateway"></a><a name="VNetGateway"></a>建立 VPN 閘道

此步驟將帶您建立 VNet 的虛擬網路閘道。 建立閘道通常可能需要 45 分鐘或更久，視選取的閘道 SKU 而定。

### <a name="about-the-gateway-subnet"></a>關於閘道子網路

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

### <a name="create-the-gateway"></a>建立閘道

使用下列值建立 VPN 閘道：

* **名稱：** VNet1GW
* **區域：** 美國東部
* **閘道類型：** VPN
* **VPN 類型：** 以路由為基礎
* **SKU：** VpnGw1
* **世代：** Generation1
* **虛擬網路：** VNet1
* **閘道子網路位址範圍：** 10.1.255.0/27
* **公用 IP 位址：** 新建
* **公用 IP 位址名稱：** VNet1GWpip
* **啟用主動-主動模式：** 已停用
* **設定 BGP：** 已停用

[!INCLUDE [Create a vpn gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="view-the-public-ip-address"></a><a name="view"></a>檢視公用 IP 位址

您可以在閘道的 [概觀] 頁面上，檢視閘道的公用 IP 位址。

:::image type="content" source="./media/tutorial-create-gateway-portal/address.png" alt-text="概觀分頁":::

若要查看公用 IP 位址物件的其他資訊，請按一下 [公用 IP 位址] 旁的 [名稱/IP 位址] 連結。

## <a name="create-a-local-network-gateway"></a><a name="LocalNetworkGateway"></a>建立區域網路閘道

區域網路閘道是一個特定物件，代表您用於路由的內部部署位置 (網站)。 請賦予網站可供 Azure 參考的名稱，然後指定您想要與其建立連線之內部部署 VPN 裝置的 IP 位址。 也請指定 IP 位址首碼，以供系統透過 VPN 閘道路由至 VPN 裝置。 您指定的位址首碼是位於內部部署網路上的首碼。 如果您的內部部署網路變更，或者您需要變更 VPN 裝置的公用 IP 位址，您稍後可以輕鬆地更新這些值。

使用下列值建立區域網路閘道：

* **名稱：** Site1
* **資源群組：** TestRG1
* **位置：** 美國東部

[!INCLUDE [Add a local network gateway](../../includes/vpn-gateway-add-local-network-gateway-portal-include.md)]

## <a name="configure-your-vpn-device"></a><a name="VPNDevice"></a>設定 VPN 裝置

內部部署網路的站對站連線需要 VPN 裝置。 在此步驟中，設定 VPN 裝置。 設定 VPN 裝置時，您需要下列值：

* 共用金鑰。 這個共同金鑰與您建立站對站 VPN 連線時指定的共用金鑰相同。 在我們的範例中，我們會使用基本的共用金鑰。 我們建議您產生更複雜的金鑰以供使用。
* 虛擬網路閘道的公用 IP 位址。 您可以使用 Azure 入口網站、PowerShell 或 CLI 來檢視公用 IP 位址。 若要使用 Azure 入口網站尋找 VPN 閘道的公用 IP 位址，請瀏覽至 [虛擬網路閘道]，然後選取閘道名稱。

[!INCLUDE [Configure a VPN device](../../includes/vpn-gateway-configure-vpn-device-include.md)]

## <a name="create-a-vpn-connection"></a><a name="CreateConnection"></a>建立 VPN 連線

您會在虛擬網路閘道與內部部署 VPN 裝置之間建立站對站 VPN 連線。

使用下列值建立連線：

* **區域網路閘道名稱：** Site1
* **連線名稱：** VNet1toSite1
* **共用的金鑰：** 此範例中，我們會使用 abc123。 但是，您可以使用任何與您 VPN 硬體相容的項目。 值務必符合連線的兩端。

[!INCLUDE [Add a site-to-site connection](../../includes/vpn-gateway-add-site-to-site-connection-portal-include.md)]

## <a name="verify-the-vpn-connection"></a><a name="VerifyConnection"></a>驗證 VPN 連線

[!INCLUDE [Verify the connection](../../includes/vpn-gateway-verify-connection-portal-include.md)]

## <a name="connect-to-a-virtual-machine"></a><a name="connectVM"></a>連接到虛擬機器

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]

## <a name="optional-steps"></a>選擇性步驟

### <a name="add-additional-connections-to-the-gateway"></a><a name="addconnect"></a>將其他連線新增至閘道

您可以新增其他的連線，前提是連線之間沒有任何位址空間重疊。

1. 若要新增其他連線，請瀏覽至 VPN 閘道，然後選取 [連線] 以開啟 [連線] 頁面。
1. 選取 [+新增] 以新增連線。 調整連線類型以反映 VNet 對 VNet (如果連線到另一個 VNet 閘道) 或站對站。
1. 如果您要使用站對站進行連線，且您尚未建立所需連線之站台的區域網路閘道，您可以新建立一個。
1. 指定您要使用的共用金鑰，然後選取 [確定] 以建立連線。

### <a name="resize-a-gateway-sku"></a><a name="resize"></a>調整閘道 SKU 的大小

閘道 SKU 的大小調整與變更有相關特定規則。 在本節中，我們會調整 SKU 的大小。 如需詳細資訊，請參閱[閘道設定 - 調整大小和變更 SKU](vpn-gateway-about-vpn-gateway-settings.md#resizechange)。

[!INCLUDE [resize a gateway](../../includes/vpn-gateway-resize-gw-portal-include.md)]

### <a name="reset-a-gateway"></a><a name="reset"></a>重設閘道

如果您遺失一或多個站對站 VPN 通道上的跨單位 VPN 連線，重設 Azure VPN 閘道會很有幫助。 在此情況下，您的所有內部部署 VPN 裝置都會運作正常，但無法使用 Azure VPN 閘道建立 IPsec 通道。

[!INCLUDE [reset a gateway](../../includes/vpn-gateway-reset-gw-portal-include.md)]

### <a name="additional-configuration-considerations"></a><a name="additional"></a>其他設定考量

S2S 設定可透過多種方式進行自訂。 如需詳細資訊，請參閱下列文章：

* 如需 BGP 的相關資訊，請參閱 [BGP 概觀](vpn-gateway-bgp-overview.md)和[如何設定 BGP](vpn-gateway-bgp-resource-manager-ps.md)。
* 如需強制通道的相關資訊，請參閱[關於強制通道](vpn-gateway-forced-tunneling-rm.md)。
* 如需高可用性主動-主動連線的相關資訊，請參閱[高可用性跨單位和 VNet 對 VNet 連線能力](vpn-gateway-highlyavailable.md)。
* 如需如何在虛擬網路中限制資源之網路流量的資訊，請參閱[網路安全性](../virtual-network/network-security-groups-overview.md)。
* 如需 Azure 如何在 Azure、內部部署和網際網路資源間路由流量的資訊，請參閱[虛擬網路流量路由](../virtual-network/virtual-networks-udr-overview.md)。

## <a name="clean-up-resources"></a>清除資源

如果您不打算繼續使用此應用程式或移至下一個教學課程，請使用下列步驟刪除這些資源：

1. 在入口網站頂端的 [搜尋] 方塊中輸入您的資源群組，然後從搜尋結果中進行選取。

1. 選取 [刪除資源群組]。

1. 針對 [輸入資源群組名稱] 輸入您的資源群組，然後選取 [刪除]。

## <a name="next-steps"></a>後續步驟

設定了 S2S 連線後，您可以將 P2S 連線新增至相同的閘道。

> [!div class="nextstepaction"]
> [點對站 VPN 連線](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
