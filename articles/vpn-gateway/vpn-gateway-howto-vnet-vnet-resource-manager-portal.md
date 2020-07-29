---
title: 設定 VNet 對 VNet VPN 閘道連線： Azure 入口網站
description: 使用 Resource Manager 和 Azure 入口網站建立 VNet 間的 VPN 閘道連接。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 03/05/2020
ms.author: cherylmc
ms.openlocfilehash: 5abf79d23ca2de661383cc002dac9d7f9e4dc5bf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84985562"
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-by-using-the-azure-portal"></a>使用 Azure 入口網站設定 VNet 對 VNet 的 VPN 閘道連線

本文協助您使用 VNet 對 VNet 連線類型來連線虛擬網路 (VNet)。 VNet 可位於不同的區域且來自不同的訂用帳戶。 當您連線來自不同訂用帳戶的 VNet 時，訂用帳戶不需與相同的 Active Directory 租用戶相關聯。 

![v2v 圖表](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v2vrmps.png)

本文中的步驟適用於 Azure Resource Manager 部署模型並使用 Azure 入口網站。 您可以使用下列文章中所述的選項，透過不同的部署工具或模型來建立這個組態：

> [!div class="op_single_selector"]
> * [Azure 入口網站](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Azure CLI](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Azure 入口網站 (傳統)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [連線不同的部署模型 - Azure 入口網站](vpn-gateway-connect-different-deployment-models-portal.md)
> * [連線不同的部署模型 - PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

## <a name="about-connecting-vnets"></a>關於連線 VNet

下列各節說明不同的虛擬網路連線方式。

### <a name="vnet-to-vnet"></a>VNet 對 VNet

設定 VNet 對 VNet 連線是輕鬆連線 VNet 的方法。 當您透過 VNet 對 VNet 連線類型 (VNet2VNet) 將虛擬網路連線到另一個虛擬網路時，類似於建立內部部署位置的站對站 IPsec 連線。 這兩種連線類型都使用 VPN 閘道來提供採用 IPsec/IKE 的安全通道，而且在通訊時的運作方式相同。 不過，其差異在於區域網路閘道的設定方式。 

當您建立 VNet 對 VNet 連線時，系統會自動建立和填入區域網路閘道位址空間。 如果您更新一個 VNet 的位址空間，另一個 VNet 就會自動路由到已更新的位址空間。 相較於站對站連線，建立 VNet 對 VNet 連線通常比較快速而容易。

### <a name="site-to-site-ipsec"></a>站對站 (IPsec)

如果您使用複雜的網路組態，您可能偏好使用[站對站連線](vpn-gateway-howto-site-to-site-resource-manager-portal.md)來連線 VNet。 當您遵循站對站 IPsec 步驟時，您會以手動方式建立及設定區域網路閘道。 每個 VNet 的區域網路閘道都會將其他 VNet 視為本機網站。 這些步驟可讓您為區域網路閘道指定其他位址空間，以便路由傳送流量。 如果 VNet 的位址空間變更，您必須手動更新對應的區域網路閘道。

### <a name="vnet-peering"></a>VNet 對等互連

您也可以使用 VNet 對等互連來連線 VNet。 VNet 對等互連不會使用 VPN 閘道，且具有不同的條件約束。 此外，[VNet 對等互連價格](https://azure.microsoft.com/pricing/details/virtual-network)與 [VNet 對 VNet VPN 閘道價格](https://azure.microsoft.com/pricing/details/vpn-gateway)的計算方式不同。 如需詳細資訊，請參閱 [VNet 對等互連](../virtual-network/virtual-network-peering-overview.md)。

## <a name="why-create-a-vnet-to-vnet-connection"></a>為何要建立 VNet 對 VNet 連線？

基於下列原因，建議您使用 VNet 對 VNet 連線來進行虛擬網路連線：

### <a name="cross-region-geo-redundancy-and-geo-presence"></a>跨區域的異地備援和異地目前狀態

  * 您可以使用安全連線設定自己的異地複寫或同步處理，而不用查看網際網路對向端點。
  * 您可以使用 Azure 流量管理員和 Azure Load Balancer，利用異地備援跨多個 Azure 區域設定高度可用的工作負載。 例如，您可以設定分散於多個 Azure 區域的 SQL Server Always On 可用性群組。

### <a name="regional-multi-tier-applications-with-isolation-or-administrative-boundaries"></a>具有隔離或管理界限的區域性多層式應用程式

  * 在相同區域中，您可以因為隔離或管理需求，設定將多層式應用程式與多個虛擬網路連線在一起。

您可以將 VNet 對 VNet 通訊與多站台組態結合。 這些組態可讓您建立使用內部虛擬網路連線結合跨單位連線的網路拓撲，如下圖所示：

![關於連線](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/aboutconnections.png "關於連線")

本文說明如何使用 VNet 對 VNet 連線類型來連線 VNet。 練習這些步驟時，您可以使用下列範例設定值。 在範例中，虛擬網路位於相同的訂用帳戶，但在不同的資源群組。 如果您的 Vnet 位於不同的訂用帳戶中，就無法在入口網站中建立連線。 改用 [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) 或 [CLI](vpn-gateway-howto-vnet-vnet-cli.md)。 如需 VNet 對 VNet 連線的詳細資訊，請參閱 [VNet 對 VNet 常見問題集](#vnet-to-vnet-faq)。

### <a name="example-settings"></a>範例設定

**VNet1 的值：**

- **虛擬網路設定**
    - **名稱**： VNet1
    - **位址空間**：10.1.0.0/16
    - **訂**用帳戶：選取您要使用的訂用帳戶。
    - **資源群組**： TestRG1
    - **位置**：美國東部
    - **子網路**
        - **名稱**：前端
        - **位址範圍**：10.1.0.0/24
    - **閘道子網**：
        - **名稱**： *GatewaySubnet*是自動填入
        - **位址範圍**： 10.1.255.0/27

- **虛擬網路閘道設定**
    - **名稱**： VNet1GW
    - 閘道類型****︰選取 [VPN]****。
    - **VPN 類型**：選取 [路由型]****。
    - **SKU**：選取您想要使用的閘道 SKU。
    - **公用 IP 位址名稱**： VNet1GWpip
    - **[連接]**
       - **名稱**： VNet1toVNet4
       - **共用金鑰**：您可以自行建立共用金鑰。 當您建立 VNet 之間的連線時，值必須相符。 針對此練習，請使用 abc123。

**VNet4 的值：**

- **虛擬網路設定**
   - **名稱**： VNet4
   - **位址空間**： 10.41.0.0/16
   - **訂**用帳戶：選取您要使用的訂用帳戶。
   - **資源群組**： TestRG4
   - **位置**：美國西部
   - **子網路** 
      - **名稱**：前端
      - **位址範圍**： 10.41.0.0/24
   - **GatewaySubnet** 
      - **名稱**： *GatewaySubnet*是自動填入
      - **位址範圍**： 10.41.255.0/27

- **虛擬網路閘道設定** 
    - **名稱**： VNet4GW
    - 閘道類型****︰選取 [VPN]****。
    - **VPN 類型**：選取 [路由型]****。
    - **SKU**：選取您想要使用的閘道 SKU。
    - **公用 IP 位址名稱**： VNet4GWpip
    - **[連接]** 
       - **名稱**： VNet4toVNet1
       - **共用金鑰**：您可以自行建立共用金鑰。 當您建立 VNet 之間的連線時，值必須相符。 針對此練習，請使用 abc123。

## <a name="create-and-configure-vnet1"></a>建立及設定 VNet1
如果您已經有 VNet，請驗證設定是否與您的 VPN 閘道設計相容。 請特別注意任何可能與其他網路重疊的子網路。 如果有重疊的子網路，您的連線便無法正常運作。

### <a name="to-create-a-virtual-network"></a>建立虛擬網路
[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="create-the-vnet1-gateway"></a>建立 VNet1 閘道
此步驟將帶您建立 VNet 的虛擬網路閘道。 建立閘道通常可能需要 45 分鐘或更久，視選取的閘道 SKU 而定。 如果您要練習建立此組態，請參閱[範例設定](#example-settings)。

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

### <a name="to-create-a-virtual-network-gateway"></a>建立虛擬網路閘道
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="create-and-configure-vnet4"></a>建立及設定 VNet4
設定 VNet1 之後，請重複先前的步驟，並將值取代為 VNet4 值，以建立 VNet4 和 VNet4 閘道。 您不需要等到 VNet1 的虛擬網路閘道建立完成後，再設定 VNet4。 如果您使用自己的值，請確定位址空間沒有與任何您想要連線的 VNet 重疊。

## <a name="configure-the-vnet1-gateway-connection"></a>設定 VNet1 閘道連線
當 VNet1 和 VNet4 的虛擬網路閘道完成時，您可以建立虛擬網路閘道連線。 在本節中，您要建立從 VNet1 到 VNet4 的連線。 這些步驟只適用於相同的訂用帳戶中的 VNet。 如果您的 Vnet 位於不同的訂用帳戶中，您必須使用[PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)來進行連線。 不過，如果 Vnet 位於相同訂用帳戶中的不同資源群組，您可以使用入口網站將它們連線。

1. 在 Azure 入口網站中，選取 [所有資源]****，在搜尋方塊中輸入「虛擬網路閘道」**，然後瀏覽至您 VNet 的虛擬網路閘道。 例如， **VNet1GW**。 選取閘道以開啟 [**虛擬網路閘道**] 頁面。 在 [設定]  下，選取 [連線]  。

   ![連接頁面](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connections.png "連接頁面")
2. 選取 [ **+ 新增**] 以開啟 [**新增連接**] 頁面。

   ![加入連接](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/vnet1-vnet4-connection.png "新增連線")
3. 在 [新增連線]**** 頁面上，填入您的連線值：

   - **名稱**：輸入連接的名稱。 例如， *VNet1toVNet4*。

   - **連線類型**：從下拉式選單選取 [ **vnet 對 vnet** ]。

   - **第一個虛擬網路閘道**：此域值會自動填入，因為您是從指定的虛擬網路閘道建立此連線。

   - **第二個虛擬網路閘道**：此欄位是您想要建立連接之 VNet 的虛擬網路閘道。 選取 [選擇另一個虛擬網路閘道]****，以開啟 [選擇虛擬網路閘道]**** 頁面。

     - 檢視此頁面上列出的虛擬網路閘道。 請注意，只會列出您的訂用帳戶中的虛擬網路閘道。 如果想要連線的虛擬網路閘道不在您的訂用帳戶中，請使用 [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)。

     - 選取您想要連線的虛擬網路閘道。

     - **共用金鑰（PSK）**：在此欄位中，輸入連接的共用金鑰。 您可以產生此金鑰，或自行建立此金鑰。 在站對站連線中，您用於內部部署裝置與虛擬網路閘道連線的金鑰完全相同。 此處的概念類似，差別在於是連線到另一個虛擬網路閘道，而不是連線到 VPN 裝置。
    
4. 選取 [ **確定** ] 以儲存變更。

## <a name="configure-the-vnet4-gateway-connection"></a>設定 VNet4 閘道連線
接下來，建立從 VNet4 到 VNet1 的連接。 在入口網站中，找出與 VNet4 相關聯的虛擬網路閘道。 請遵循上一節中的步驟，將值取代為建立從 VNet4 到 VNet1 的連接。 請確定您使用相同的共用金鑰。

## <a name="verify-your-connections"></a>確認您的連線

1. 在 Azure 入口網站中找出虛擬網路閘道。 
2. 在 [虛擬網路閘道]**** 頁面上，選取 [連線]****，以檢視虛擬網路閘道的 [連線]**** 頁面。 建立連線之後，您會看到 [**狀態**] 值變更為 [**已連接**]。

   ![驗證連接](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/view-connections.png "驗證連接")
3. 在 [**名稱**] 資料行底下，選取其中一個連接以查看詳細資訊。 當資料開始流動時，您會看到 [資料輸入]**** 和 [資料輸出]**** 的值。

   ![狀態](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/status.png "狀態")

## <a name="add-additional-connections"></a>新增其他連線

如果您需要新增其他連線，請瀏覽至您想建立連線的虛擬網路閘道，然後選取 [連線]****。 您可以建立另一個 VNet 對 VNet 連線，或建立 IPsec 站對站連線到內部部署位置。 請務必調整 [連線類型]**** 來符合您需要建立的連線類型。 建立其他連線之前，請確認您虛擬網路的位址空間與任何您需要連線的位址空間不重疊。 如需建立站對站連線的步驟，請參閱[建立站對站連線](vpn-gateway-howto-site-to-site-resource-manager-portal.md)。

## <a name="vnet-to-vnet-faq"></a>VNet 對 VNet 常見問題集
檢視常見問題集詳細資料以取得 VNet 對 VNet 連線的其他資訊。

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>後續步驟

如需如何在虛擬網路中限制資源網路流量的資訊，請參閱[網路安全性](../virtual-network/security-overview.md)。

如需 Azure 如何在 Azure、內部部署和網際網路資源間路由流量的資訊，請參閱[虛擬網路流量路由](../virtual-network/virtual-networks-udr-overview.md)。
