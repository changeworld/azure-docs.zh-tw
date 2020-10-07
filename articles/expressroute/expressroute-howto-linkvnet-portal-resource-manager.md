---
title: 教學課程：將 VNet 連結至 ExpressRoute 線路-Azure 入口網站
description: 本教學課程說明如何使用 Azure 入口網站建立連線，以將虛擬網路連結到 Azure ExpressRoute 線路。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 10/06/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: bfb358694cbdd214490fb41052e508b94d10baf4
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91773003"
---
# <a name="tutorial-connect-a-virtual-network-to-an-expressroute-circuit-using-the-portal"></a>教學課程：使用入口網站將虛擬網路連線到 ExpressRoute 線路

> [!div class="op_single_selector"]
> * [Azure 入口網站](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [影片 - Azure 入口網站](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (傳統)](expressroute-howto-linkvnet-classic.md)
> 

本教學課程可協助您使用 Azure 入口網站建立連線，以將虛擬網路連結到 Azure ExpressRoute 線路。 您連接到 Azure ExpressRoute 線路的虛擬網路可以位於相同的訂用帳戶中，或屬於另一個訂用帳戶的一部分。

在本教學課程中，您會了解如何：
> [!div class="checklist"]
> - 將虛擬網路連線到相同訂用帳戶中的線路。
> - 將虛擬網路連線至不同訂用帳戶中的線路。
> - 刪除虛擬網路與 ExpressRoute 線路之間的連結。

## <a name="prerequisites"></a>必要條件

* 開始設定之前，請先檢閱[必要條件](expressroute-prerequisites.md)、[路由需求](expressroute-routing.md)及[工作流程](expressroute-workflows.md)。

* 您必須擁有作用中的 ExpressRoute 線路。
  * 遵循指示來 [建立 ExpressRoute 線路](expressroute-howto-circuit-portal-resource-manager.md) ，並由您的連線提供者來啟用該線路。
  * 確定您已針對循環設定了 Azure 私用對等。 如需對等互連和路由指示，請參閱 [建立和修改 ExpressRoute 線路的對等互連](expressroute-howto-routing-portal-resource-manager.md) 。
  * 確定已設定 Azure 私用對等互連，並在您的網路與 Microsoft 之間建立 BGP 對等互連，以進行端對端連線。
  * 請確定您有已建立且完整佈建的虛擬網路和虛擬網路閘道。 請依照指示[為 ExpressRoute 建立虛擬網路閘道](expressroute-howto-add-gateway-resource-manager.md)。 ExpressRoute 的虛擬網路閘道會使用 GatewayType 'ExpressRoute'，而不是 VPN。

* 您最多可以將 10 個虛擬網路連結至標準 ExpressRoute 電路。 在使用標準 ExpressRoute 電路時，所有虛擬網路都必須位於相同的地理政治區域內。

* 單一 VNet 最多可連結到四個 ExpressRoute 線路。 使用下列程式，為您所連線的每個 ExpressRoute 線路建立新的連線物件。 ExpressRoute 線路可以位於相同的訂用帳戶、不同的訂用帳戶或兩者的混合。

* 如果您啟用 ExpressRoute premium 附加元件，您可以將虛擬網路連結到 ExpressRoute 線路的地緣政治區域外部。 Premium 附加元件也可讓您根據所選頻寬，將10個以上的虛擬網路連線到 ExpressRoute 線路。 如需高階附加元件的詳細資訊，請參閱 [常見問題集](expressroute-faqs.md) 。

* 您可以在開始前先[觀看影片](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)來進一步了解步驟。

## <a name="connect-a-vnet-to-a-circuit---same-subscription"></a>將 VNet 連線到線路 - 相同訂用帳戶

> [!NOTE]
> 如果第3層提供者已設定您的對等互連，則不會顯示 BGP 設定資訊。 如果線路處於佈建狀態，您應該能夠建立連線。
>

### <a name="to-create-a-connection"></a>建立連線

1. 確認正確設定您的 ExpressRoute 電路和 Azure 私人對等互連。 遵循 [建立 expressroute 線路](expressroute-howto-circuit-arm.md) 中的指示，並 [建立和修改 expressroute 線路的對等互連](expressroute-howto-routing-arm.md)。 ExpressRoute 線路看起來應該像下圖：

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/express-route-circuit.png" alt-text="刪除 ExpressRoute 線路螢幕擷取畫面":::

2. 您現在可以開始佈建將虛擬網路閘道連結至 ExpressRoute 線路的連線。 選取 [**連接**  >  **新增**] 以開啟 [**加入連接**] 頁面，然後設定這些值。

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/add-connection.png" alt-text="刪除 ExpressRoute 線路螢幕擷取畫面":::

3. 順利設定連線後，您的連線物件就會顯示連接資訊。

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-object.png" alt-text="刪除 ExpressRoute 線路螢幕擷取畫面":::

## <a name="connect-a-vnet-to-a-circuit---different-subscription"></a>將 VNet 連線到線路 - 不同訂用帳戶

您可以讓多個訂用帳戶共用 ExpressRoute 線路。 下圖顯示簡單的圖解，示範多個訂用帳戶共用 ExpressRoute 線路的方式。

:::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/cross-subscription.png" alt-text="刪除 ExpressRoute 線路螢幕擷取畫面":::

大型雲端內的每個較小型雲端，會用來代表屬於組織內不同部門的訂用帳戶。 組織內的每個部門都會使用自己的訂用帳戶來部署其服務，但他們可以共用單一 ExpressRoute 線路，以連回您的內部部署網路。 單一部門 (在此範例中：IT) 可以擁有 ExpressRoute 循環。 組織內的其他訂用帳戶可以使用 ExpressRoute 線路。

  > [!NOTE]
  > ExpressRoute 循環擁有者需支付專用循環的連線和頻寬費用。 所有虛擬網路都會共用相同的頻寬。
  >

### <a name="administration---about-circuit-owners-and-circuit-users"></a>系統管理 - 關於線路擁有者和線路使用者

「線路擁有者」是 ExpressRoute 線路資源的已授權「進階使用者」。 電路擁有者能夠建立可由「電路使用者」兌換的授權。 線路使用者是虛擬網路閘道的擁有者，與 ExpressRoute 線路位於相同的訂用帳戶內。 電路使用者可以兌換授權 (每個虛擬網路一個授權)。

電路擁有者能夠隨時修改及撤銷授權。 如果撤銷授權，則在存取權遭撤銷的訂用帳戶中，所有連結連線均會被刪除。

### <a name="circuit-owner-operations"></a>循環擁有者作業

**建立連線授權**

線路擁有者會建立授權，以建立線路使用者用來將其虛擬網路閘道連接到 ExpressRoute 線路的授權金鑰。 一個授權僅適用於一個連線。

> [!NOTE]
> 每個連線都需要個別的授權。
>

1. 在 [ExpressRoute] 頁面中 **，選取 [授權]** ，然後輸入授權的 **名稱** ，然後選取 [ **儲存**]。

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/authorization.png" alt-text="刪除 ExpressRoute 線路螢幕擷取畫面":::

2. 儲存組態之後，複製**資源識別碼**和**授權金鑰**。

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/authorization-key.png" alt-text="刪除 ExpressRoute 線路螢幕擷取畫面":::

**刪除連線授權**

您可以選取連線頁面上的 [刪除]**** 圖示來刪除連線。

### <a name="circuit-user-operations"></a>循環使用者作業

線路使用者需要具備資源識別碼，以及線路擁有者所提供的授權金鑰。

**兌換連線授權**

1. 選取 [ **+ 新增** ] 按鈕。

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/create-new-resources.png" alt-text="刪除 ExpressRoute 線路螢幕擷取畫面":::

2. 在 Marketplace 中搜尋「連線」、選取它，然後選取 [**建立** **]** 。

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/search-connection.png" alt-text="刪除 ExpressRoute 線路螢幕擷取畫面":::

3. 確定 [連線類型]**** 設定為 [ExpressRoute]。
4. 填入詳細資料，然後在 [基本] 頁面中選取 **[確定]** 。

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-basics.png" alt-text="刪除 ExpressRoute 線路螢幕擷取畫面":::

5. 在 [設定]**** 頁面中選取 [虛擬網路閘道]****，並選取 [兌換授權]**** 核取方塊。
6. 輸入**授權金鑰**和**對等線路 URI**，並提供連線名稱。 選取 [確定]。 **對等電路 URI**是 expressroute 線路 (的資源識別碼，您可以在 expressroute 線路) 的 [屬性] 設定窗格中找到此識別碼。

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-settings.png" alt-text="刪除 ExpressRoute 線路螢幕擷取畫面":::

7. 查看 [ **摘要** ] 頁面中的資訊，然後選取 **[確定]**。

**釋出連線授權**

您可以藉由刪除將 ExpressRoute 線路連結到虛擬網路的連線來釋出授權。

## <a name="clean-up-resources"></a>清除資源

您可以選取連線頁面上的 [刪除]**** 圖示，來刪除連線並取消 VNet 與 ExpressRoute 線路的連結。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已瞭解如何將虛擬網路連線至相同訂用帳戶中的線路和不同的訂用帳戶。 如需 ExpressRoute 閘道的詳細資訊，請參閱： 

> [!div class="nextstepaction"]
> [關於 ExpressRoute 虛擬網路閘道](expressroute-about-virtual-network-gateways.md)
