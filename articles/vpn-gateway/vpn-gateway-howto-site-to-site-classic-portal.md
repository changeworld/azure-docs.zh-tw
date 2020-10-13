---
title: 將內部部署網路連接至 Azure 虛擬網路：站對站 VPN (傳統)：入口網站 | Microsoft Docs
description: 建立透過公用網際網路從內部部署網路至傳統 Azure 虛擬網路的 IPsec 連線。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/08/2020
ms.author: cherylmc
ms.openlocfilehash: 7680c7ad4b2c5c8b8c1c13fb2344575659eda140
ms.sourcegitcommit: b437bd3b9c9802ec6430d9f078c372c2a411f11f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91892816"
---
# <a name="create-a-site-to-site-connection-using-the-azure-portal-classic"></a>使用 Azure 入口網站建立站對站連線 (傳統)

本文說明如何使用 Azure 入口網站來建立從內部部署網路到 VNet 的站對站 VPN 閘道連線。 本文中的步驟適用于傳統部署模型，不適用於目前的部署模型，Resource Manager。 您也可從下列清單中選取不同的選項，以使用不同的部署工具或部署模型來建立此組態：

> [!div class="op_single_selector"]
> * [Azure 入口網站](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Azure 入口網站 (傳統)](vpn-gateway-howto-site-to-site-classic-portal.md)
> 

站對站 VPN 閘道連線可用來透過 IPsec/IKE (IKEv1 或 IKEv2) VPN 通道，將內部部署網路連線到 Azure 虛擬網路。 此類型的連線需要位於內部部署的 VPN 裝置，且您已對該裝置指派對外開放的公用 IP 位址。 如需 VPN 閘道的詳細資訊，請參閱[關於 VPN 閘道](vpn-gateway-about-vpngateways.md)。

![站對站 VPN 閘道跨單位連線圖表](./media/vpn-gateway-howto-site-to-site-classic-portal/site-to-site-diagram.png)

## <a name="before-you-begin"></a><a name="before"></a>開始之前

在開始設定之前，請確認您已符合下列條件：

* 確認您想要使用傳統部署模型。 如果您想要使用 Resource Manager 部署模型，請參閱[建立站對站連線 (Resource Manager)](vpn-gateway-howto-site-to-site-resource-manager-portal.md)。 建議您使用 Resource Manager 部署模型，因為傳統模型是舊版模型。
* 確定您有相容的 VPN 裝置以及能夠對其進行設定的人員。 如需相容 VPN 裝置和裝置組態的詳細資訊，請參閱[關於 VPN 裝置](vpn-gateway-about-vpn-devices.md)。
* 確認您的 VPN 裝置有對外開放的公用 IPv4 位址。
* 如果您不熟悉位於內部部署網路組態的 IP 位址範圍，您需要與能夠提供那些詳細資料的人協調。 當您建立此組態時，您必須指定 IP 位址範圍的首碼，以供 Azure 路由傳送至您的內部部署位置。 內部部署網路的子網路皆不得與您所要連線的虛擬網路子網路重疊。
* 需要 PowerShell 才能指定共用金鑰並建立 VPN 閘道連線。 [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

### <a name="sample-configuration-values-for-this-exercise"></a><a name="values"></a>此練習的範例組態值

本文的範例使用下列值。 您可以使用這些值來建立測試環境，或參考這些值，進一步了解本文中的範例。 一般而言，使用位址空間的 IP 位址值時，您想要與網路系統管理員協調，以避免重迭的位址空間，這可能會影響路由。 在此情況下，如果您想要建立可運作的連接，請將 IP 位址值取代為您自己的值。

* **資源群組：** TestRG1
* **VNet 名稱：** TestVNet1
* **位址空間：** 10.11.0.0/16
* **子網名稱：** 前端
* **子網位址範圍：** 10.11.0.0/24
* **GatewaySubnet：** 10.11.255.0/27
* **區域：** (us) 美國東部
* **本機網站名稱︰** Site2
* **用戶端位址空間：** 位於內部部署網站上的位址空間。

## <a name="create-a-virtual-network"></a><a name="CreatVNet"></a>建立虛擬網路

當您建立要用於 S2S 連線的虛擬網路時，您必須確定您指定的位址空間，不會與您要連接之本機網站的任何用戶端位址空間重疊。 如果有重疊的子網路，您的連線便無法正常運作。

* 如果您已經有 VNet，請驗證設定是否與您的 VPN 閘道設計相容。 請特別注意任何可能與其他網路重疊的子網路。

* 如果您還沒有虛擬網路，請建立一個。 已提供螢幕擷取畫面做為範例。 請務必將值取代為您自己的值。

### <a name="to-create-a-virtual-network"></a>建立虛擬網路

[!INCLUDE [basic classic vnet](../../includes/vpn-gateway-vnet-classic.md)]

[!INCLUDE [basic classic DNS](../../includes/vpn-gateway-dns-classic.md)]

## <a name="configure-the-site-and-gateway"></a><a name="localsite"></a>設定網站和閘道

### <a name="to-configure-the-site"></a>設定網站

本機網站通常是指您的內部部署位置。 它包含您要建立連線之 VPN 裝置的 IP 位址，以及將透過 VPN 閘道路由傳送至 VPN 裝置的 IP 位址範圍。

1. 在 VNet 的頁面上，選取 [ **設定**] 下的 [ **站對站**連線]。
1. 在 [站對站連接] 頁面上，選取 [ **+ 新增**]。
1. 在 [ **設定 VPN 連線和閘道** ] 頁面上，針對 [連線 **類型**] 將 [ **站對站** ] 保持選取狀態。 在此練習中，您必須使用 [範例值](#values) 的組合和您自己的值。

   * **VPN 閘道 IP 位址：** 這是您內部部署網路之 VPN 裝置的公用 IP 位址。 VPN 裝置需要 IPv4 公用 IP 位址。 為您要連線的 VPN 裝置指定有效的公用 IP 位址。 它必須可由 Azure 存取。 如果您不知道 VPN 裝置的 IP 位址，您可以一律放入預留位置值 (只要其為有效的公用 IP 位址格式即可)，然後稍後加以變更。

   * **用戶端位址空間︰** 列出您要透過此閘道路由傳送至本機內部部署網路的 IP 位址範圍。 您可以加入多個位址空間範圍。 確定您在此指定的範圍，不會與虛擬網路要連接的其他網路範圍重疊，或與虛擬網路本身的位址範圍重疊。
1. 在頁面底部，請勿選取 [審核 + 建立]。 請改 **為選取 [下一步：閘道>] **。

### <a name="to-configure-the-virtual-network-gateway"></a><a name="sku"></a>設定虛擬網路閘道

1. 在 [ **閘道** ] 頁面上，選取下列值：

   * **大小：** 這是您用來建立虛擬網路閘道的閘道 SKU。 傳統 VPN 閘道會使用舊式 (舊版) 閘道 SKU。 如需舊版閘道 SKU 的詳細資訊，請參閱[使用虛擬網路閘道 SKU (舊式 SKU)](vpn-gateway-about-skus-legacy.md)。 您可以選取此練習的 [ **標準** ]。

   * **路由類型：** 選取您閘道的路由類型。 這也稱為 VPN 類型。 請務必選取正確的類型，因為您無法將閘道從某個類型轉換成另一個類型。 您的 VPN 裝置必須與您所選取的路由類型相容。 如需路由類型的詳細資訊，請參閱 [關於 VPN 閘道設定](vpn-gateway-about-vpn-gateway-settings.md#vpntype)。 您可能會看到參考 'RouteBased' 和 'PolicyBased' VPN 類型的文章。 'Dynamic' 對應到 'RouteBased'，而 'Static' 對應到 'PolicyBased'。 通常，您會想要動態路由。

   * **閘道子網：** 您指定的閘道子網大小取決於您要建立的 VPN 閘道設定。 雖然可以建立像 /29 這麼小的閘道子網路，但建議您使用 /27 或 /28。 這可建立包含更多位址的較大子網路。 使用較大的閘道子網路可讓您擁有足夠的 IP 位址，以因應未來可能的組態。

1. 選取頁面底部的 [ **審核 + 建立** ] 以驗證您的設定。 選取 [ **建立** ] 以部署。 建立虛擬網路閘道最多可能需要45分鐘的時間，視您選取的閘道 SKU 而定。

## <a name="configure-your-vpn-device"></a><a name="vpndevice"></a>設定您的 VPN 裝置

內部部署網路的站對站連線需要 VPN 裝置。 在此步驟中，設定 VPN 裝置。 設定 VPN 裝置時，您需要下列值：

* 共用金鑰。 這個共同金鑰與您建立站對站 VPN 連線時指定的共用金鑰相同。 在我們的範例中，我們會使用基本的共用金鑰。 我們建議您產生更複雜的金鑰以供使用。
* 虛擬網路閘道的公用 IP 位址。 您可以使用 Azure 入口網站、PowerShell 或 CLI 來檢視公用 IP 位址。

[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## <a name="retrieve-values"></a><a name="getvalues"></a>取出值

[!INCLUDE [retrieve values](../../includes/vpn-gateway-values-classic.md)]

## <a name="create-the-connection"></a><a name="CreateConnection"></a>建立連線

> [!NOTE]
> 針對傳統部署模型，此步驟不適用於 Azure 入口網站或透過 Azure Cloud Shell。 您必須在本機從桌面使用服務管理 (SM) 版本的 Azure PowerShell Cmdlet。
>

在這個步驟中，使用先前步驟中的值，您可以設定共用金鑰並建立連線。 您設定的金鑰必須是 VPN 裝置組態中使用的相同金鑰。

1. 設定共用金鑰及建立連線。

   * 變更-VNetName 值和-LocalNetworkSiteName 值。 指定包含空格的名稱時，請使用單引號括住值。
   * '-SharedKey ' 是您所產生的值，然後指定。 在此範例中，我們使用了 ' abc123 '，但是您可以 (，而且應該) 產生更複雜的內容。 重要的是，您在此指定的值必須與您在設定 VPN 裝置時指定的值相同。

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group TestRG1 TestVNet1' `
   -LocalNetworkSiteName '6C74F6E6_Site2' -SharedKey abc123
   ```

1. 建立連線後，結果是︰**狀態︰成功**。

## <a name="verify-your-connection"></a><a name="verify"></a>確認您的連線

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

如有連線問題，請參閱左窗格中目錄的 [疑難排解]**** 區段。

## <a name="how-to-reset-a-vpn-gateway"></a><a name="reset"></a>如何重設 VPN 閘道

如果您遺失一或多個站對站 VPN 通道上的跨單位 VPN 連線，重設 Azure VPN 閘道會很有幫助。 在此情況下，您的所有內部部署 VPN 裝置都會運作正常，但無法使用 Azure VPN 閘道建立 IPsec 通道。 如需相關步驟，請參閱[重設 VPN 閘道](vpn-gateway-resetgw-classic.md#resetclassic)。

## <a name="how-to-change-a-gateway-sku"></a><a name="changesku"></a>如何變更閘道 SKU

如需變更閘道 SKU 的步驟，請參閱 [調整閘道 sku 的大小](vpn-gateway-about-SKUS-legacy.md#classicresize)。

## <a name="next-steps"></a>後續步驟

* 一旦完成您的連接，就可以將虛擬機器加入您的虛擬網路。 如需詳細資訊，請參閱[虛擬機器](https://docs.microsoft.com/azure/)。
* 如需強制通道的相關資訊，請參閱 [關於強制通道](vpn-gateway-about-forced-tunneling.md)。
