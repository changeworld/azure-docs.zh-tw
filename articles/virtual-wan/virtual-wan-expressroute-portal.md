---
title: 教學課程：使用 Azure 虛擬 WAN 建立 ExpressRoute 連線
description: 在本教學課程中，深入了解如何使用 Azure 虛擬 WAN 建立與 Azure 和內部部署環境的 ExpressRoute 連線。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 10/07/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my corporate on-premises network(s) to my VNets using Virtual WAN and ExpressRoute.
ms.openlocfilehash: 7d880be6cbc37b273258075e6efc7a98d3478384
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92054809"
---
# <a name="tutorial-create-an-expressroute-association-using-azure-virtual-wan"></a>教學課程：使用 Azure 虛擬 WAN 來建立 ExpressRoute 關聯

本教學課程會示範如何使用虛擬 WAN，透過 ExpressRoute 線路以連線到 Azure 中的資源。 如需有關虛擬 WAN 和虛擬 WAN 資源的詳細資訊，請參閱[虛擬 WAN 概觀](virtual-wan-about.md)。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立虛擬 WAN
> * 建立中樞和閘道
> * 將 VNet 連線至中樞
> * 將線路連線至中樞閘道
> * 測試連線能力
> * 變更閘道大小
> * 公告預設路由

## <a name="prerequisites"></a>必要條件

在開始設定之前，請確認您已符合下列條件：

* 您有一個要連線的虛擬網路。 驗證沒有任何內部部署網路的子網路與您要連線的虛擬網路子網路重疊。 若要在 Azure 入口網站中建立虛擬網路，請參閱[快速入門](../virtual-network/quick-create-portal.md)。

* 您的虛擬網路沒有任何虛擬網路閘道。 如果您的虛擬網路有閘道 (VPN 或 ExpressRoute)，則必須移除所有閘道。 此設定需要將虛擬網路改為連線到虛擬 WAN 中樞閘道。

* 取得中樞區域的 IP 位址範圍。 中樞是虛擬 WAN 建立和使用的虛擬網路。 您為中樞區域指定的位址範圍不能與任何連線的現有虛擬網路重疊。 也不能與連線至內部部署的位址範圍重疊。 如果您不熟悉位於內部部署網路設定的 IP 位址範圍，請與能夠提供那些詳細資料的人員協調。

* ExpressRoute 線路必須是進階或標準線路，才能連線至中樞閘道。

* 如果您沒有 Azure 訂用帳戶，請建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-a-virtual-wan"></a><a name="openvwan"></a>建立虛擬 WAN

透過瀏覽器瀏覽至 [Azure 入口網站](https://portal.azure.com) ，並使用您的 Azure 帳戶登入。

1. 瀏覽至 [虛擬 WAN] 頁面。 在入口網站中，按一下 [+建立資源]  。 在搜尋方塊中鍵入**虛擬 WAN** 並選取 [輸入]。
2. 從結果中選取 [虛擬 WAN]  。 在 [虛擬 WAN] 頁面中，按一下 [建立]  ，以開啟 [建立 WAN] 頁面。
3. 在 [建立 WAN]  頁面的 [基本]  索引標籤中，填寫下列欄位：

   ![建立 WAN](./media/virtual-wan-expressroute-portal/createwan.png)

   * **訂用帳戶** - 選取您要使用的訂用帳戶。
   * **資源群組** - 建立新的或使用現有的資源群組。
   * **資源群組位置** - 從下拉式清單中選擇資源位置。 WAN 是全域資源，並不會在特定區域存留。 不過，您必須選取一個區域以方便管理，以及放置所建立的 WAN 資源。
   * **名稱** - 鍵入要用來稱呼 WAN 的名稱。
   * **類型** - 選取 [標準]  。 您無法使用基本 SKU 建立 ExpressRoute 閘道。
4. 填寫完欄位之後，選取 [檢閱 + 建立]  。
5. 驗證通過後，選取 [建立]  ，以建立虛擬 WAN。

## <a name="create-a-virtual-hub-and-gateway"></a><a name="hub"></a>建立虛擬中樞與閘道

虛擬中樞是虛擬 WAN 建立和使用的虛擬網路。 它可以包含各種閘道，例如 VPN 和 ExpressRoute。 在本節中，您將為虛擬中樞建立 ExpressRoute 閘道。 您可以在[建立新的虛擬中樞](#newhub)時建立閘道，也可以編輯[現有中樞](#existinghub)建立閘道。 

ExpressRoute 閘道以 2 Gbps 為單位進行佈建。 1 個縮放單位 = 2 Gbps，最多支援 10 個縮放單位 = 20 Gbps。 建立完整的虛擬中樞和閘道需要大約 30 分鐘。

### <a name="to-create-a-new-virtual-hub-and-a-gateway"></a><a name="newhub"></a>建立新的虛擬中樞和閘道

建立新的虛擬中樞。 建立中樞後，您需要支付中樞費用，即使您未連結任何網站也是如此。

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-er-hub-include.md)]

### <a name="to-create-a-gateway-in-an-existing-hub"></a><a name="existinghub"></a>在現有的中樞內建立閘道

您也可以編輯現有的中樞來建立閘道。

1. 導覽至您要編輯的虛擬中樞，並選取該虛擬中樞。
2. 在 [編輯虛擬中樞]  頁面中，選取核取方塊 [包含 ExpressRoute 閘道]  。
3. 選取 [確認]  ，以確認變更。 建立完整的中樞和中樞資源需要大約 30 分鐘。

   ![現有中樞](./media/virtual-wan-expressroute-portal/edithub.png "編輯中樞")

### <a name="to-view-a-gateway"></a>若要檢視閘道

建立 ExpressRoute 閘道之後，您可以檢視閘道詳細資料。 導覽至中樞，選取 [ExpressRoute]  ，並檢視閘道。

![檢視閘道](./media/virtual-wan-expressroute-portal/viewgw.png "檢視閘道")

## <a name="connect-your-vnet-to-the-hub"></a><a name="connectvnet"></a>將 VNet 連線至中樞

在本節中，您會在中樞和 VNet 之間建立同儕節點連線。 為您想要連線的每個 VNet 重複這些步驟。

1. 在您的虛擬 WAN 頁面上，按一下 [虛擬網路連線]  。
2. 在 [虛擬網路連線] 頁面上，按一下 [+ 新增連線]  。
3. 在 [新增連線]  頁面上，填寫下列欄位︰

    * **名稱** - 為您的連線命名。
    * **中樞** - 選取要與此連線產生關聯的中樞。
    * **訂用帳戶** - 請確認訂用帳戶。
    * **虛擬網路** - 選取要與此中樞連線的虛擬網路。 虛擬網路不能有現有的虛擬網路閘道 (VPN 和 ExpressRoute 皆無法)。

## <a name="connect-your-circuit-to-the-hub-gateway"></a><a name="connectcircuit"></a>將您的線路連線至中樞閘道

建立閘道之後，即可將 [ExpressRoute 線路](../expressroute/expressroute-howto-circuit-portal-resource-manager.md)連線到該閘道。 ExpressRoute 標準或進階線路若位於 ExpressRoute Global Reach 支援的位置，則可以連線至虛擬 WAN ExpressRoute 閘道，並享用所有虛擬 WAN 傳輸功能 (VPN 至 VPN、VPN 與 ExpressRoute transit)。 ExpressRoute 標準或進階線路若位於非 Global Reach 位置，則可以連線至 Azure 資源，但將無法使用虛擬 WAN 傳輸功能。 Azure 虛擬 WAN 不支援 ExpressRoute 本機。

### <a name="to-connect-the-circuit-to-the-hub-gateway"></a>將線路連線到中樞閘道

在入口網站中，移至 [虛擬中樞] -> [連線] -> [ExpressRoute]  頁面。 如果您的訂閱包含 ExpressRoute 線路的存取權，您會在線路清單中看到要使用的線路。 如果您沒看見任何線路，但已獲得授權金鑰和同儕節點線路 URI，則可以兌換並連線線路。 請參閱[兌換授權金鑰以以進行連線](#authkey)。

1. 選取線路。
2. 選取 [連線線路]  。

   ![連線線路](./media/virtual-wan-expressroute-portal/cktconnect.png "連線線路")

### <a name="to-connect-by-redeeming-an-authorization-key"></a><a name="authkey"></a>兌換授權金鑰以進行連線

使用提供的授權金鑰和線路 URI 以進行連線。

1. 在 [ExpressRoute] 頁面上，按一下 [+ 兌換授權金鑰] 

   ![螢幕擷取畫面顯示已選取兌換授權金鑰虛擬中樞的 ExpressRoute。](./media/virtual-wan-expressroute-portal/redeem.png "兌換")
2. 在 [兌換授權金鑰] 頁面上，填入值。

   ![兌換索引鍵值](./media/virtual-wan-expressroute-portal/redeemkey2.png "兌換索引鍵值")
3. 選取 [新增]  以新增金鑰。
4. 檢視線路。 兌換線路僅顯示名稱 (不含類型、提供者與其他資訊)，因為它與使用者處於不同的訂閱中。

## <a name="to-test-connectivity"></a>若要測試連線能力

建立線路連線之後，中樞連線狀態會指出「此中樞」，表示已與中樞 ExpressRoute 閘道建立連線。 等待大約 5 分鐘，然後再測試 ExpressRoute 線路後面的用戶端連線，例如，先前在 VNet 中建立的 VM。

如果將網站連線到與 ExpressRoute 閘道位於相同中樞的虛擬 WAN VPN 閘道，即可在 VPN 與 ExpressRoute 端點之間進行雙向連線。 支援動態路由 (BGP)。 中樞的閘道 ASN 是固定的，目前無法編輯。

## <a name="to-change-the-size-of-a-gateway"></a>變更閘道的大小

如果您想要變更 ExpressRoute 閘道的大小，請在中樞內找出 ExpressRoute 閘道，並從下拉式清單選取縮放單位。 儲存變更。 更新中樞閘道需要大約 30 分鐘。

![變更閘道大小](./media/virtual-wan-expressroute-portal/changescale.png "變更閘道大小")

## <a name="to-advertise-default-route-00000-to-endpoints"></a>將預設路由 0.0.0.0/0 公告至端點

如果希望 Azure 虛擬中樞將預設路由 0.0.0.0/0 公告至 ExpressRoute 端點，您需要啟用「傳播預設路由」。

1. 選取 [線路] ->…-> [編輯連線]  。

   ![編輯連線](./media/virtual-wan-expressroute-portal/defaultroute1.png "編輯連線")

2. 選取 [啟用]  以傳播預設路由。

   ![傳播預設路由](./media/virtual-wan-expressroute-portal/defaultroute2.png "傳播預設路由")

## <a name="clean-up-resources"></a><a name="cleanup"></a>清除資源

您可以使用 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) 來移除不再需要的資源群組，以及其所包含的所有資源。 將 "myResourceGroup" 取代為您的資源群組名稱，然後執行下列 PowerShell 命令：

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>後續步驟

接著，若要深入了解虛擬 WAN，請參閱：

> [!div class="nextstepaction"]
> * [虛擬 WAN 常見問題集](virtual-wan-faq.md)
