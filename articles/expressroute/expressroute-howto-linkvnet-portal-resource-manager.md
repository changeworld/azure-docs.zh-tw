---
title: 教學課程：將 VNet 連結到 ExpressRoute 線路 - Azure 入口網站
description: 本教學課程示範如何使用 Azure 入口網站建立連線，將虛擬網路連結到 Azure ExpressRoute 線路。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/15/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 0ffc9c2ee17862497d3fd986da8e003f7a497056
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2020
ms.locfileid: "92107278"
---
# <a name="tutorial-connect-a-virtual-network-to-an-expressroute-circuit-using-the-portal"></a>教學課程：使用入口網站將虛擬網路連線到 ExpressRoute 線路

> [!div class="op_single_selector"]
> * [Azure 入口網站](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [影片 - Azure 入口網站](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (傳統)](expressroute-howto-linkvnet-classic.md)
> 

本教學課程可協助您使用 Azure 入口網站建立連線，將擬網路連結到 Azure ExpressRoute 線路。 連線至 Azure ExpressRoute 線路的虛擬網路可以位於相同的訂用帳戶中，或屬於另一個訂用帳戶。

在本教學課程中，您會了解如何：
> [!div class="checklist"]
> - 將虛擬網路連線到相同訂用帳戶中的線路。
> - 將虛擬網路連線到不同訂用帳戶中的線路。
> - 刪除虛擬網路與 ExpressRoute 線路之間的連結。

## <a name="prerequisites"></a>必要條件

* 開始設定之前，請先檢閱[必要條件](expressroute-prerequisites.md)、[路由需求](expressroute-routing.md)及[工作流程](expressroute-workflows.md)。

* 您必須擁有作用中的 ExpressRoute 線路。
  * 遵循指示來 [建立 ExpressRoute 線路](expressroute-howto-circuit-portal-resource-manager.md) ，並由您的連線提供者來啟用該線路。
  * 確定您已針對循環設定了 Azure 私用對等。 如需對等互連和路由指示，請參閱[建立和修改 ExpressRoute 線路的對等互連](expressroute-howto-routing-portal-resource-manager.md)一文。
  * 請確定已設定 Azure 私人對等互連，且已建立您的網路與 Microsoft 之間的 BGP 對等互連，以便進行端對端連線。
  * 請確定您有已建立且完整佈建的虛擬網路和虛擬網路閘道。 請依照指示[為 ExpressRoute 建立虛擬網路閘道](expressroute-howto-add-gateway-resource-manager.md)。 ExpressRoute 的虛擬網路閘道會使用 GatewayType 'ExpressRoute'，而不是 VPN。

* 您最多可以將 10 個虛擬網路連結至標準 ExpressRoute 電路。 在使用標準 ExpressRoute 電路時，所有虛擬網路都必須位於相同的地理政治區域內。

* 單一 VNet 最多可連結到四個 ExpressRoute 線路。 使用下列程序來建立您要連線每個 ExpressRoute 線路的新連線物件。 ExpressRoute 線路可以位於相同的訂用帳戶、不同的訂用帳戶或兩者的混合。

* 如果您已啟用 ExpressRoute 進階附加元件，則可連結 ExpressRoute 線路的地理政治區域以外的虛擬網路。 進階附加元件也可讓您將 10 個以上的虛擬網路連線到 ExpressRoute 線路，取決於選擇的頻寬。 如需高階附加元件的詳細資訊，請參閱 [常見問題集](expressroute-faqs.md) 。

* 您可以在開始前先[觀看影片](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)來進一步了解步驟。

## <a name="connect-a-vnet-to-a-circuit---same-subscription"></a>將 VNet 連線到線路 - 相同訂用帳戶

> [!NOTE]
> 如果您的對等互連是由第 3 層提供者設定，就不會顯示 BGP 組態資訊。 如果線路處於佈建狀態，您應該能夠建立連線。
>

### <a name="to-create-a-connection"></a>建立連線

1. 確認正確設定您的 ExpressRoute 電路和 Azure 私人對等互連。 請遵循[建立 ExpressRoute 線路](expressroute-howto-circuit-arm.md)和[建立和修改 ExpressRoute 線路的對等互連](expressroute-howto-routing-arm.md)中的指示。 ExpressRoute 線路看起來應該像下圖：

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/express-route-circuit.png" alt-text="刪除 ExpressRoute 線路螢幕擷取畫面":::

1. 您現在可以開始佈建將虛擬網路閘道連結至 ExpressRoute 線路的連線。 選取 [連線] > [新增] 以開啟 [新增連線] 頁面。

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/add-connection.png" alt-text="刪除 ExpressRoute 線路螢幕擷取畫面":::

1. 輸入連線的名稱，然後選取 [下一步:設定 >]。

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/create-connection-basic.png" alt-text="刪除 ExpressRoute 線路螢幕擷取畫面":::

1. 選取屬於虛擬網路的閘道，您想要將此虛擬網路連結至線路，然後選取 [檢閱 + 建立]。 驗證完成後，請選取 [建立]。

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/create-connection-settings.png" alt-text="刪除 ExpressRoute 線路螢幕擷取畫面":::

1. 順利設定連線後，您的連線物件就會顯示連接資訊。

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-object.png" alt-text="刪除 ExpressRoute 線路螢幕擷取畫面":::

## <a name="connect-a-vnet-to-a-circuit---different-subscription"></a>將 VNet 連線到線路 - 不同訂用帳戶

您可以讓多個訂用帳戶共用 ExpressRoute 線路。 下圖顯示簡單的圖解，示範多個訂用帳戶共用 ExpressRoute 線路的方式。

:::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/cross-subscription.png" alt-text="刪除 ExpressRoute 線路螢幕擷取畫面":::

大型雲端內的每個較小型雲端，會用來代表屬於組織內不同部門的訂用帳戶。 組織內的每個部門都可以使用自己的訂用帳戶來部署其服務，但可共用單一 ExpressRoute 線路，以連線回內部部署網路。 單一部門 (在此範例中：IT) 可以擁有 ExpressRoute 循環。 組織內的其他訂用帳戶可以使用 ExpressRoute 線路。

  > [!NOTE]
  > ExpressRoute 循環擁有者需支付專用循環的連線和頻寬費用。 所有虛擬網路都會共用相同的頻寬。
  >

### <a name="administration---about-circuit-owners-and-circuit-users"></a>系統管理 - 關於線路擁有者和線路使用者

「線路擁有者」是 ExpressRoute 線路資源的已授權「進階使用者」。 電路擁有者能夠建立可由「電路使用者」兌換的授權。 線路使用者是虛擬網路閘道的擁有者，與 ExpressRoute 線路位於不同的訂用帳戶內。 電路使用者可以兌換授權 (每個虛擬網路一個授權)。

電路擁有者能夠隨時修改及撤銷授權。 如果撤銷授權，則在存取權遭撤銷的訂用帳戶中，所有連結連線均會被刪除。

### <a name="circuit-owner-operations"></a>循環擁有者作業

**建立連線授權**

線路擁有者會建立授權，這會建立授權金鑰讓線路使用者可用以將其虛擬網路閘道連線到 ExpressRoute 線路。 一個授權僅適用於一個連線。

> [!NOTE]
> 每個連線都需要個別的授權。
>

1. 在 [ExpressRoute] 頁面中，選取 [授權]，然後輸入授權的**名稱**並選取 [儲存]。

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/authorization.png" alt-text="刪除 ExpressRoute 線路螢幕擷取畫面":::

2. 儲存組態之後，複製**資源識別碼**和**授權金鑰**。

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/authorization-key.png" alt-text="刪除 ExpressRoute 線路螢幕擷取畫面":::

**刪除連線授權**

您可以針對連線的授權金鑰選取 [刪除] 圖示來刪除連線。

:::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/delete-authorization-key.png" alt-text="刪除 ExpressRoute 線路螢幕擷取畫面":::

如果您想要刪除連線，但仍要保留授權金鑰，則可以從線路的連線頁面中刪除該連線。

:::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/delete-connection-owning-circuit.png" alt-text="刪除 ExpressRoute 線路螢幕擷取畫面":::

### <a name="circuit-user-operations"></a>循環使用者作業

線路使用者需要具備資源識別碼，以及線路擁有者所提供的授權金鑰。

**兌換連線授權**

1. 選取 [+ 建立資源] 按鈕。 搜尋 [連線]，然後選取 [建立]。

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/create-new-resources.png" alt-text="刪除 ExpressRoute 線路螢幕擷取畫面":::

1. 確定 [連線類型] 設定為 [ExpressRoute]。 選取 [資源群組] 和 [位置]，然後在 [基本] 頁面中選取 [確定]。

    > [!NOTE]
    > 位置「必須」 符合您為其建立連線的虛擬網路閘道位置。

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-basics.png" alt-text="刪除 ExpressRoute 線路螢幕擷取畫面":::

1. 在 [設定]**** 頁面中選取 [虛擬網路閘道]**，並選取 [兌換授權]**** 核取方塊。 輸入*授權金鑰*和*對等線路 URI*，並提供連線名稱。 選取 [確定]  。 
 
    > [!NOTE]
    > *對等互連線路 URI* 是 ExpressRoute 線路的資源識別碼 (您可以在 ExpressRoute 線路的 [屬性設定] 窗格底下找到)。

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-settings.png" alt-text="刪除 ExpressRoute 線路螢幕擷取畫面":::

1. 在 [摘要] 頁面中檢閱資訊，然後選取 [確定]。

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-summary.png" alt-text="刪除 ExpressRoute 線路螢幕擷取畫面":::

## <a name="clean-up-resources"></a>清除資源

您可以選取連線頁面上的 [刪除]**** 圖示，來刪除連線並取消 VNet 與 ExpressRoute 線路的連結。

:::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/delete-connection.png" alt-text="刪除 ExpressRoute 線路螢幕擷取畫面":::

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何將虛擬網路連線至相同訂用帳戶和不同訂用帳戶中的線路。 如需 ExpressRoute 閘道的詳細資訊，請參閱： 

> [!div class="nextstepaction"]
> [關於 ExpressRoute 虛擬網路閘道](expressroute-about-virtual-network-gateways.md)
