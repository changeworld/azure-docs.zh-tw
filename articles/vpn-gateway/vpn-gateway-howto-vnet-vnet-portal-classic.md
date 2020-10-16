---
title: 建立 Vnet：傳統： Azure 入口網站之間的連接
description: 使用 PowerShell 和 Azure 入口網站將 Azure 虛擬網路連接在一起。
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/15/2020
ms.author: cherylmc
ms.openlocfilehash: 0d81e0474d898ffee7f128c0bcea61f077c3d758
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2020
ms.locfileid: "92103215"
---
# <a name="configure-a-vnet-to-vnet-connection-classic"></a>設定 VNet 對 VNet 連線 (傳統)

本文可協助您建立虛擬網路之間的 VPN 閘道連線。 虛擬網路可位於相同或不同的區域，以及來自相同或不同的訂用帳戶。

:::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-portal-classic/v2vclassic.png" alt-text="顯示傳統 VNet 對 VNet 架構的圖表":::

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

本文中的步驟適用於傳統部署模型和 Azure 入口網站。 您也可從下列清單中選取不同的選項，以使用不同的部署工具或部署模型來建立此組態：

> [!div class="op_single_selector"]
> * [傳統](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Resource Manager](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [連接不同部署模型中的 Vnet](vpn-gateway-connect-different-deployment-models-portal.md)
>
>

## <a name="about-vnet-to-vnet-connections"></a>關於 VNet 對 VNet 連線

在傳統部署模型中使用 VPN 閘道將一個虛擬網路連線到另一個虛擬網路 (VNet 對 VNet)，類似於將虛擬網路連線到內部部署網站位置。 這兩種連線類型都使用 VPN 閘道提供使用 IPsec/IKE 的安全通道。

您所連線的 Vnet 可位於不同的訂用帳戶和不同的區域。 您可以將 VNet 對 VNet 通訊與多站台組態結合。 這可讓您建立結合了跨單位連線與內部虛擬網路連線的網路拓撲。

:::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-portal-classic/aboutconnections.png" alt-text="顯示傳統 VNet 對 VNet 架構的圖表":::

### <a name="why-connect-virtual-networks"></a><a name="why"></a>為什麼要連線虛擬網路？

針對下列原因，您可能希望連接虛擬網路：

* **跨區域的異地備援和異地目前狀態**

  * 您可以使用安全連線設定自己的異地複寫或同步處理，而不用查看網際網路對向端點。
  * 有了 Azure Load Balancer 和 Microsoft 或第三方叢集技術，您便可以利用跨多個 Azure 區域的異地備援，設定具有高可用性的工作負載。 其中一個重要的範例就是使用分散在多個 Azure 區域的可用性群組來設定 SQL Always On。
* **具有嚴密隔離界限的區域性多層式應用程式**

  * 在相同的區域中，您可以設定既有多個 VNet 相連又有嚴密的隔離及安全的層次間通訊的多層式應用程式。
* **Azure 中的跨訂用帳戶、組織間通訊**

  * 如果您有多個 Azure 訂用帳戶，您可以將虛擬網路之間不同訂用帳戶的工作負載安全地連接在一起。
  * 針對企業或服務提供者，您可以在 Azure 中使用安全 VPN 技術啟用跨組織通訊。

如需 VNet 對 VNet 連接的詳細資訊，請參閱本文結尾處的 [VNet 對 VNet 的考量](#faq)。

## <a name="prerequisites"></a>必要條件

我們使用入口網站來執行大部分的步驟，但是您必須使用 PowerShell 來建立 Vnet 之間的連線。 因為沒有任何方法可在入口網站中指定共用金鑰，所以您無法使用 Azure 入口網站建立連接。 [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

## <a name="planning"></a><a name="planning"></a>規劃

決定將用來設定虛擬網路的範圍相當重要。 就此組態而言，您必須確定沒有任何 VNet 範圍彼此重疊，或是與其連接的區域網路重疊。

### <a name="vnets"></a><a name="vnet"></a>Vnet

在此練習中，我們會使用下列範例值：

**TestVNet1 的值**

名稱︰TestVNet1<br>
位址空間︰10.11.0.0/16、10.12.0.0/16 (選擇性)<br>
子網路名稱：default<br>
子網路位址範圍：10.11.0.0/24<br>
資源群組：ClassicRG<br>
位置：美國東部<br>
GatewaySubnet：10.11.1.0/27

**TestVNet4 的值**

名稱︰TestVNet4<br>
位址空間︰10.41.0.0/16、10.42.0.0/16 (選擇性)<br>
子網路名稱：default<br>
子網路位址範圍︰10.41.0.0/24<br>
資源群組：ClassicRG<br>
位置：美國西部<br>
GatewaySubnet：10.41.1.0/27

### <a name="connections"></a><a name="plan"></a>連接

下表顯示您將如何連接 Vnet 的範例。 範圍僅供作為指導方針。 請記下您虛擬網路的範圍。 稍後的步驟將會需要這項資訊。

在此範例中，TestVNet1 會連接到您建立名為 ' VNet4Local ' 的區域網路網站。 VNet4Local 的設定包含 TestVNet4 位址首碼。
每個 VNet 的本機網站是其他 VNet。 在我們的組態中使用下列範例值︰

**範例**

| 虛擬網路 | 位址空間 | Location | 連接至區域網路站台 |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |美國東部 |SiteVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |美國西部 |SiteVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

## <a name="create-virtual-networks"></a><a name="vnetvalues"></a>建立虛擬網路

在此步驟中，您會建立兩個傳統虛擬網路： TestVNet1 和 TestVNet4。 如果您使用這篇文章作為練習，請使用 [範例值](#vnet)。

**在建立 VNet 時，請牢記下列設定︰**

* **虛擬網路位址空間** – 在 [虛擬網路位址空間] 頁面上，指定您想要用於虛擬網路的位址範圍。 這些是將指派給 VM 的動態 IP 位址，以及指派給您部署至此虛擬網路之其他角色執行個體的動態 IP 位址。<br>您選取的位址空間不能與任何其他 VNet 的位址空間，或此 VNet 連線之內部部署位置的位址空間重疊。

* **位置** – 在建立虛擬網路時，您會將此網路與 Azure 位置 (區域) 產生關聯。 例如，如果您希望部署到虛擬網路的 VM 實際位於美國西部，請選取該位置。 建立關聯之後，您就無法變更與您的虛擬網路相關聯的位置。

**建立 VNet 之後，您可以新增下列設定︰**

* **位址空間** – 此組態不需要額外的位址空間，但是您可以在建立 VNet 之後新增額外的位址空間。

* **子網路** – 此組態不需要額外的子網路，但是您可能想要讓您的 VM 位於與其他角色執行個體不同的子網路中。

* **DNS 伺服器** – 輸入 DNS 伺服器名稱和 IP 位址。 此設定不會建立 DNS 伺服器。 它可讓您指定要用於此虛擬網路之名稱解析的 DNS 服務。

### <a name="to-create-a-classic-virtual-network"></a>若要建立傳統虛擬網路

[!INCLUDE [basic classic vnet](../../includes/vpn-gateway-vnet-classic.md)]

[!INCLUDE [basic classic DNS](../../includes/vpn-gateway-dns-classic.md)]

## <a name="configure-sites-and-gateways"></a><a name="localsite"></a>設定網站和閘道

Azure 會使用每個區域網路站台中指定的設定，來決定如何路由傳送 VNet 之間的流量。 每個 VNet 都必須指向要做為流量路由傳送目的地的個別區域網路。 您需決定要用來參考每個區域網路站台的名稱。 最好使用描述性的項目。

例如，TestVNet1 會連線到您所建立名為 "VNet4Local" 的區域網路網站。 VNet4Local 的設定包含 TestVNet4 位址首碼。

請記住，每個 VNet 的本機網站都是另一個 VNet。

| 虛擬網路 | 位址空間 | Location | 連接至區域網路站台 |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |美國東部 |SiteVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |美國西部 |SiteVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

### <a name="to-configure-a-site"></a><a name="site"></a>設定網站

本機網站通常是指您的內部部署位置。 它包含您要建立連線之 VPN 裝置的 IP 位址，以及將透過 VPN 閘道路由傳送至 VPN 裝置的 IP 位址範圍。

1. 在 VNet 的頁面上，選取 [ **設定**] 下的 [ **站對站**連線]。
1. 在 [站對站連接] 頁面上，選取 [ **+ 新增**]。
1. 在 [ **設定 VPN 連線和閘道** ] 頁面上，針對 [連線 **類型**] 將 [ **站對站** ] 保持選取狀態。

   * **VPN 閘道 IP 位址：** 這是您內部部署網路之 VPN 裝置的公用 IP 位址。 在此練習中，您可以放入虛擬位址，因為您還沒有其他網站的 VPN 閘道 IP 位址。 例如，5.4.3.2。 之後，當您設定其他 VNet 的閘道之後，您可以調整此值。

   * **用戶端位址空間：** 列出您要透過此閘道路由至其他 VNet 的 IP 位址範圍。 您可以加入多個位址空間範圍。 確定您在此指定的範圍，不會與虛擬網路要連接的其他網路範圍重疊，或與虛擬網路本身的位址範圍重疊。
1. 在頁面底部，請勿選取 [審核 + 建立]。 請改 **為選取 [下一步：閘道>] **。

### <a name="to-configure-a-virtual-network-gateway"></a><a name="sku"></a>設定虛擬網路閘道

1. 在 [ **閘道** ] 頁面上，選取下列值：

   * **大小：** 這是您用來建立虛擬網路閘道的閘道 SKU。 傳統 VPN 閘道會使用舊式 (舊版) 閘道 SKU。 如需舊版閘道 SKU 的詳細資訊，請參閱[使用虛擬網路閘道 SKU (舊式 SKU)](vpn-gateway-about-skus-legacy.md)。 您可以選取此練習的 [ **標準** ]。

   * **路由類型：** 選取您閘道的路由類型。 這也稱為 VPN 類型。 請務必選取正確的類型，因為您無法將閘道從某個類型轉換成另一個類型。 您的 VPN 裝置必須與您所選取的路由類型相容。 如需路由類型的詳細資訊，請參閱 [關於 VPN 閘道設定](vpn-gateway-about-vpn-gateway-settings.md#vpntype)。 您可能會看到參考 'RouteBased' 和 'PolicyBased' VPN 類型的文章。 'Dynamic' 對應到 'RouteBased'，而 'Static' 對應到 'PolicyBased'。 針對此設定，請選取 [ **動態**]。

   * **閘道子網：** 您指定的閘道子網大小取決於您要建立的 VPN 閘道設定。 雖然可以建立像 /29 這麼小的閘道子網路，但建議您使用 /27 或 /28。 這可建立包含更多位址的較大子網路。 使用較大的閘道子網路可讓您擁有足夠的 IP 位址，以因應未來可能的組態。

1. 選取頁面底部的 [ **審核 + 建立** ] 以驗證您的設定。 選取 [ **建立** ] 以部署。 建立虛擬網路閘道最多可能需要45分鐘的時間，視您選取的閘道 SKU 而定。
1. 建立此閘道時，您可以開始進行下一個步驟。

### <a name="configure-testvnet4-settings"></a>設定 TestVNet4 設定

重複 [建立網站和閘道](#localsite) 的步驟來設定 TestVNet4，並在必要時替換值。 如果您是練習，請使用 [範例值](#planning)。

## <a name="update-local-sites"></a><a name="updatelocal"></a>更新本機網站

為這兩個 Vnet 建立虛擬網路閘道之後，您必須調整 **VPN 閘道 IP 位址**的本機網站內容。

|VNet 名稱|已連線網站|閘道 IP 位址|
|:--- |:--- |:--- |
|TestVNet1|VNet4Local|TestVNet4 的 VPN 閘道 IP 位址|
|TestVNet4|VNet1Local|TestVNet1 的 VPN 閘道 IP 位址|

### <a name="part-1---get-the-virtual-network-gateway-public-ip-address"></a>第 1 部分 - 取得虛擬網路閘道公用 IP 位址

1. 前往 **資源群組** ，然後選取虛擬網路，以流覽至您的 VNet。
1. 在虛擬網路的頁面上，于右邊的 [ **基本** 資訊] 窗格中，找出 **閘道 IP 位址** 並複製到剪貼簿。

### <a name="part-2---modify-the-local-site-properties"></a>第2部分-修改本機網站屬性

1. 在 [站對站連線] 下，選取連接。 例如，SiteVNet4。
1. 在站對站連線的 [ **屬性** ] 頁面上，選取 [ **編輯本機網站**]。
1. 在 [ **vpn 閘道 ip 位址** ] 欄位中，貼上您在上一節中複製的 VPN 閘道 ip 位址。
1. 選取 [確定]  。
1. 系統會更新此欄位。 您也可以使用這個方法，新增您想要路由至此網站的其他 IP 位址。

### <a name="part-3---repeat-steps-for-the-other-vnet"></a>第3部分-針對另一個 VNet 重複步驟

重複 TestVNet4 的步驟。

## <a name="retrieve-configuration-values"></a><a name="getvalues"></a>取得設定值

[!INCLUDE [retrieve values](../../includes/vpn-gateway-values-classic.md)]

## <a name="create-connections"></a><a name="createconnections"></a>建立連線

當所有先前的步驟都已完成時，您可以設定 IPsec/IKE 預先共用金鑰並建立連線。 這組步驟會使用 PowerShell。 因為無法在入口網站中指定共用金鑰，所以無法在 Azure 入口網站中設定傳統部署模型的 VNet 對 VNet 連線。

在這些範例中，請注意，共用金鑰是完全相同的。 共用金鑰必須一律相符。 請務必使用 VNet 和區域網路網站的確切名稱，取代這些範例中的值。

1. 建立 TestVNet1 至 TestVNet4 的連線。 請務必變更這些值。

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group ClassicRG TestVNet1' `
   -LocalNetworkSiteName 'value for _VNet4Local' -SharedKey A1b2C3D4
   ```
2. 建立 TestVNet4 至 TestVNet1 的連線。

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group ClassicRG TestVNet4' `
   -LocalNetworkSiteName 'value for _VNet1Local' -SharedKey A1b2C3D4
   ```
3. 等待連線初始化。 一旦閘道初始化，其狀態為「成功」。

   ```
   Error          :
   HttpStatusCode : OK
   Id             :
   Status         : Successful
   RequestId      :
   StatusCode     : OK
   ```

## <a name="faq-and-considerations"></a><a name="faq"></a>常見問題和考慮

這些考慮適用于傳統虛擬網路和傳統虛擬網路閘道。

* 虛擬網路可位於相同或不同的訂用帳戶。
* 虛擬網路可位於相同或不同的 Azure 區域 (位置)。
* 雲端服務或負載平衡端點無法跨越虛擬網路，即使它們已連接在一起。
* 將多個虛擬網路連接在一起並不需要任何 VPN 裝置。
* VNet 對 VNet 支援連接 Azure 虛擬網路。 它不支援連接未部署到虛擬網路中的虛擬機器或雲端服務。
* VNet 對 VNet 需要動態路由閘道。 不支援 Azure 靜態路由閘道。
* 虛擬網路連線能力可以與多站台 VPN 同時使用。 一個虛擬網路 VPN 閘道最多可以有 10 條 VPN 通道連接至其他虛擬網路或內部部署站台。
* 虛擬網路與內部部署區域網路網站的位址空間不得重疊。 位址空間重疊會導致建立虛擬網路或上傳 netcfg 組態檔失敗。
* 不支援成對虛擬網路之間的備援通道。
* VNet 的所有 VPN 通道 (包括 P2S VPN) 在 Azure 中皆共用 VPN 閘道的可用頻寬及相同的 VPN 閘道執行時間 SLA。
* VNet 對 VNet 流量會經過 Azure 的骨幹。

## <a name="next-steps"></a>後續步驟

確認您的連線。 [確認 VPN 閘道連線](vpn-gateway-verify-connection-resource-manager.md)。
