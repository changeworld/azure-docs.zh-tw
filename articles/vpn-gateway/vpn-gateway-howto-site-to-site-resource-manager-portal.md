---
title: 將內部部署網路連線到 Azure 虛擬網路：站對站 VPN：入口網站
description: 使用入口網站，透過公用網際網路，建立從內部部署網路到 Azure 虛擬網路的 IPsec 站對站 VPN 閘道連線。
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/22/2020
ms.author: cherylmc
ms.openlocfilehash: 069add9a7e5dd6b19810b8b36a4fca49818bfb02
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92479543"
---
# <a name="create-a-site-to-site-connection-in-the-azure-portal"></a>在 Azure 入口網站中建立站對站連線

本文說明如何使用 Azure 入口網站來建立從內部部署網路到 VNet 的站對站 VPN 閘道連線。 本文中的步驟適用於 Resource Manager 部署模型。 您也可從下列清單中選取不同的選項，以使用不同的部署工具或部署模型來建立此組態：

> [!div class="op_single_selector"]
> * [Azure 入口網站](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Azure 入口網站 (傳統)](vpn-gateway-howto-site-to-site-classic-portal.md)
>

站對站 VPN 閘道連線可用來透過 IPsec/IKE (IKEv1 或 IKEv2) VPN 通道，將內部部署網路連線到 Azure 虛擬網路。 此類型的連線需要位於內部部署的 VPN 裝置，且您已對該裝置指派對外開放的公用 IP 位址。 如需 VPN 閘道的詳細資訊，請參閱[關於 VPN 閘道](vpn-gateway-about-vpngateways.md)。

:::image type="content" source="./media/vpn-gateway-howto-site-to-site-resource-manager-portal/site-to-site-diagram.png" alt-text="站對站 VPN 閘道跨單位連線圖表":::

## <a name="prerequisites"></a>必要條件

在開始設定之前，請確認您已符合下列條件：

* 具有有效訂用帳戶的 Azure 帳戶。 如果您沒有帳戶，請 [建立一個免費的](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)帳戶。
* 確定您有相容的 VPN 裝置以及能夠對其進行設定的人員。 如需相容 VPN 裝置和裝置組態的詳細資訊，請參閱[關於 VPN 裝置](vpn-gateway-about-vpn-devices.md)。
* 確認您的 VPN 裝置有對外開放的公用 IPv4 位址。
* 如果您不熟悉位於內部部署網路組態的 IP 位址範圍，您需要與能夠提供那些詳細資料的人協調。 當您建立此組態時，您必須指定 IP 位址範圍的首碼，以供 Azure 路由傳送至您的內部部署位置。 內部部署網路的子網路皆不得與您所要連線的虛擬網路子網路重疊。

## <a name="create-a-virtual-network"></a><a name="CreatVNet"></a>建立虛擬網路

使用下列值建立虛擬網路 (VNet) ：

* **資源群組：** TestRG1
* **名稱：** VNet1
* **區域：** (us) 美國東部
* **IPv4 位址空間：** 10.1.0.0/16
* **子網名稱：** 前端
* **子網位址空間：** 10.1.0.0/24

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
* **閘道類型：** Vpn
* **VPN 類型：** 以路由為基礎
* **SKU：** VpnGw1
* **世代：** Generation1.xml
* **虛擬網路：** VNet1
* **閘道子網位址範圍：** 10.1.255.0/27
* **公用 IP 位址：** 建立新的
* **公用 IP 位址名稱：** VNet1GWpip
* **啟用主動-主動模式：** 禁用
* **設定 BGP：** 禁用

[!INCLUDE [Create a vpn gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="create-a-local-network-gateway"></a><a name="LocalNetworkGateway"></a>建立區域網路閘道

局域網路閘道是特定物件，代表您的內部部署位置 (網站) 以供路由之用。 請賦予網站可供 Azure 參考的名稱，然後指定您想要與其建立連線之內部部署 VPN 裝置的 IP 位址。 也請指定 IP 位址首碼，以供系統透過 VPN 閘道路由至 VPN 裝置。 您指定的位址首碼是位於內部部署網路上的首碼。 如果您的內部部署網路變更，或者您需要變更 VPN 裝置的公用 IP 位址，您稍後可以輕鬆地更新這些值。

使用下列值建立局域網路閘道：

* **名稱：** Site1
* **資源群組：** TestRG1
* **位置：** 美國東部

[!INCLUDE [Add a local network gateway](../../includes/vpn-gateway-add-local-network-gateway-portal-include.md)]

## <a name="configure-your-vpn-device"></a><a name="VPNDevice"></a>設定您的 VPN 裝置

內部部署網路的站對站連線需要 VPN 裝置。 在此步驟中，設定 VPN 裝置。 設定 VPN 裝置時，您需要下列值：

* 共用金鑰。 這個共同金鑰與您建立站對站 VPN 連線時指定的共用金鑰相同。 在我們的範例中，我們會使用基本的共用金鑰。 我們建議您產生更複雜的金鑰以供使用。
* 虛擬網路閘道的公用 IP 位址。 您可以使用 Azure 入口網站、PowerShell 或 CLI 來檢視公用 IP 位址。 若要使用 Azure 入口網站尋找 VPN 閘道的公用 IP 位址，請流覽至 [ **虛擬網路閘道**]，然後選取您的閘道名稱。

[!INCLUDE [Configure a VPN device](../../includes/vpn-gateway-configure-vpn-device-include.md)]

## <a name="create-a-vpn-connection"></a><a name="CreateConnection"></a>建立 VPN 連線

您會在虛擬網路閘道與內部部署 VPN 裝置之間建立站對站 VPN 連線。

使用下列值建立連接：

* **局域網路閘道名稱：** Site1
* **連接名稱：** >vnet1tosite1
* **共用的金鑰：** 此範例中，我們會使用 abc123。 但是，您可以使用任何與您 VPN 硬體相容的項目。 值務必符合連線的兩端。

[!INCLUDE [Add a site-to-site connection](../../includes/vpn-gateway-add-site-to-site-connection-portal-include.md)]

## <a name="verify-the-vpn-connection"></a><a name="VerifyConnection"></a>驗證 VPN 連線

[!INCLUDE [Verify the connection](../../includes/vpn-gateway-verify-connection-portal-include.md)]

## <a name="how-to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>如何連接至虛擬機器

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-s2s-include.md)]

## <a name="how-to-reset-a-vpn-gateway"></a><a name="reset"></a>如何重設 VPN 閘道

如果您遺失一或多個站對站 VPN 通道上的跨單位 VPN 連線，重設 Azure VPN 閘道會很有幫助。 在此情況下，您的所有內部部署 VPN 裝置都會運作正常，但無法使用 Azure VPN 閘道建立 IPsec 通道。 如需相關步驟，請參閱[重設 VPN 閘道](reset-gateway.md)。

## <a name="how-to-change-a-gateway-sku-resize-a-gateway"></a><a name="resize"></a>如何變更閘道 SKU (調整閘道的大小)

如需變更閘道 SKU 的步驟，請參閱[閘道 SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku)。

## <a name="how-to-add-an-additional-connection-to-a-vpn-gateway"></a><a name="addconnect"></a>如何將其他連線新增至 VPN 閘道

您可以新增其他的連線，前提是連線之間沒有任何位址空間重疊。

1. 若要新增其他連線，請流覽至 VPN 閘道，然後選取 [ **連接** ] 以開啟 [連線] 頁面。
1. 選取 [ **+ 新增** ] 以新增您的連接。 調整連線類型以反映 VNet 對 VNet (如果連線到另一個 VNet 閘道) 或站對站。
1. 如果您要使用站對站進行連線，且您尚未建立所需連線之站台的區域網路閘道，您可以新建立一個。
1. 指定您要使用的共用金鑰，然後選取 **[確定]** 以建立連線。

## <a name="next-steps"></a>後續步驟

* 如需 BGP 的相關資訊，請參閱 [BGP 概觀](vpn-gateway-bgp-overview.md)和[如何設定 BGP](vpn-gateway-bgp-resource-manager-ps.md)。
* 如需強制通道的相關資訊，請參閱[關於強制通道](vpn-gateway-forced-tunneling-rm.md)。
* 如需高可用性主動-主動連線的相關資訊，請參閱[高可用性跨單位和 VNet 對 VNet 連線能力](vpn-gateway-highlyavailable.md)。
* 如需如何在虛擬網路中限制資源之網路流量的資訊，請參閱[網路安全性](../virtual-network/security-overview.md)。
* 如需 Azure 如何在 Azure、內部部署和網際網路資源間路由流量的資訊，請參閱[虛擬網路流量路由](../virtual-network/virtual-networks-udr-overview.md)。
* 如需使用 Azure Resource Manager 範本建立站對站 VPN 連線的相關資訊，請參閱[建立站對站 VPN 連線](https://azure.microsoft.com/resources/templates/101-site-to-site-vpn-create/)。
* 如需使用 Azure Resource Manager 範本建立 VNet 對 VNet VPN 連線的相關資訊，請參閱 [部署 HBase 異地](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-geo/)複寫。
