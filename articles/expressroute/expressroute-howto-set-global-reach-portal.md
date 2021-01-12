---
title: Azure ExpressRoute：使用 Azure 入口網站設定全球存取範圍
description: 本文可協助您將 ExpressRoute 線路連結在一起，以在內部部署網路之間建立私人網路，並使用 Azure 入口網站實現全球存取範圍。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 01/11/2021
ms.author: duau
ms.openlocfilehash: 9e943be3544da38788d16e54011e09e1ac3426cc
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98122003"
---
# <a name="configure-expressroute-global-reach-using-the-azure-portal"></a>使用 Azure 入口網站設定 ExpressRoute 全球存取範圍

本文可協助您使用 PowerShell 設定 ExpressRoute Global Reach。 如需詳細資訊，請參閱 [ExpressRouteRoute Global Reach](expressroute-global-reach.md)。

 ## <a name="before-you-begin"></a>開始之前

開始設定之前，請確認下列準則：

* 您瞭解 ExpressRoute 線路布建 [工作流程](expressroute-workflows.md)。
* 您的 ExpressRoute 線路處於已布建狀態。
* Azure 私用對等互連是在您的 ExpressRoute 線路上設定的。
* 如果您想要在本機執行 PowerShell，請確認您的電腦上已安裝最新版本的 Azure PowerShell。

## <a name="identify-circuits"></a>識別線路

1. 透過瀏覽器瀏覽至 [Azure 入口網站](https://portal.azure.com) ，並使用您的 Azure 帳戶登入。

2. 識別您想要使用的 ExpressRoute 線路。 您可以在任何兩個 ExpressRoute 線路的私人對等互連之間啟用 ExpressRoute 全域連線，只要它們位於支援的國家/地區。 線路必須建立在不同的對等互連位置。 

   * 如果兩個線路皆為您的訂用帳戶所擁有，則您可以選擇使用任一線路來執行下列各節中的設定。
   * 如果兩個線路在不同的 Azure 訂用帳戶中，您便需要從一個 Azure 訂用帳戶取得授權。 接著，您需在於另一個 Azure 訂用帳戶中執行設定命令時，傳入該授權金鑰。

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/expressroute-circuit-global-reach-list.png" alt-text="ExpressRoute 線路清單":::

## <a name="enable-connectivity"></a>啟用連線能力

啟用內部部署網路之間的連線能力。 相同 Azure 訂用帳戶中的線路有不同的指令組，以及不同訂用帳戶的線路。

### <a name="expressroute-circuits-in-the-same-azure-subscription"></a>相同 Azure 訂用帳戶中的 ExpressRoute 線路

1. 選取 **Azure 私** 用對等互連設定。 

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/expressroute-circuit-private-peering.png" alt-text="ExpressRoute 對等互連總覽":::

1. 選取 [ **啟用全球存取範圍** ] 核取方塊，然後選取 [ **新增全球** 存取範圍] 以開啟 [ *新增全球存取範圍* 設定] 頁面。

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/private-peering-enable-global-reach.png" alt-text="從私人對等互連啟用全球存取範圍":::

1. 在 [ *新增全球存取範圍* 設定] 頁面上，提供此設定的名稱。 選取您要連接此線路的 *ExpressRoute 線路*，然後針對「全球觸達」*子網* 輸入 **/29 IPv4** 。 我們會使用此子網路中的 IP 位址，在兩個 ExpressRoute 線路之間建立連線。 請勿在您的 Azure 虛擬網路或內部部署網路中使用此子網中的位址。 選取 [ **新增** ]，將電路新增至私用對等互連設定。

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/add-global-reach-configuration.png" alt-text="全球存取範圍設定頁面":::

1. 選取 [ **儲存** ] 以完成「全球接觸」設定。 當作業完成時，您可以透過兩個 ExpressRoute 線路，在兩個內部部署網路之間建立連線。

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/save-private-peering-configuration.png" alt-text="正在儲存私人對等互連設定":::

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>在不同 Azure 訂用帳戶中的 ExpressRoute 線路

如果這兩個線路不在相同的 Azure 訂用帳戶中，您將需要授權。 在下列設定中，會從線路2的訂用帳戶產生授權。 授權金鑰接著會傳遞至線路1。

1. 產生授權金鑰。

   :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/create-authorization-expressroute-circuit.png" alt-text="產生授權金鑰"::: 

   記下線路2的私用對等互連識別碼和授權金鑰。

1. 選取 **Azure 私** 用對等互連設定。 

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/expressroute-circuit-private-peering.png" alt-text="線路1對等互連總覽":::

1. 選取 [ **啟用全球存取範圍** ] 核取方塊，然後選取 [ **新增全球** 存取範圍] 以開啟 [ *新增全球存取範圍* 設定] 頁面。

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/private-peering-enable-global-reach.png" alt-text="從線路1啟用全球接觸":::

1. 在 [ *新增全球存取範圍* 設定] 頁面上，提供此設定的名稱。 檢查 **兌換授權** 方塊。 輸入在步驟1中產生並取得的 **授權金鑰** 和 **ExpressRoute 線路識別碼** 。 然後，為「*全球接觸」子網* 提供 **/29 IPv4** 。 我們會使用此子網路中的 IP 位址，在兩個 ExpressRoute 線路之間建立連線。 請勿在您的 Azure 虛擬網路或內部部署網路中使用此子網中的位址。 選取 [ **新增** ]，將電路新增至私用對等互連設定。

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/add-global-reach-configuration-with-authorization.png" alt-text="使用授權金鑰加入全球存取範圍":::

1. 選取 [ **儲存** ] 以完成「全球接觸」設定。 當作業完成時，您可以透過兩個 ExpressRoute 線路，在兩個內部部署網路之間建立連線。

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/save-private-peering-configuration.png" alt-text="線上路1上儲存私人對等互連設定":::

## <a name="verify-the-configuration"></a>驗證設定

在 ExpressRoute 線路設定下選取 *私人對等互連* ，以確認全球觸達設定。 正確設定之後，您的設定看起來應該如下所示：

:::image type="content" source="./media/expressroute-howto-set-global-reach-portal/verify-global-reach-configuration.png" alt-text="確認全球接觸範圍設定":::

## <a name="disable-connectivity"></a>停用連線

停用全球接觸時，有兩個選項。 若要停用所有線路之間的連線，請取消核取 [ **啟用全球存取範圍** ]，以停用所有線路間 若要停用個別線路之間的連線，請選取 [全域觸達 *名稱* ] 旁的 [刪除] 按鈕，以移除兩者之間的連線。 然後選取 [ **儲存** ] 以完成作業。

:::image type="content" source="./media/expressroute-howto-set-global-reach-portal/disable-global-reach-configuration.png" alt-text="停用全球存取範圍設定":::

作業完成之後，您的內部部署網路之間便無法再透過 ExpressRoute 線路進行連線。

## <a name="next-steps"></a>後續步驟
1. [深入了解 ExpressRoute 觸角擴及全球](expressroute-global-reach.md)
2. [確認 ExpressRoute 連線能力](expressroute-troubleshooting-expressroute-overview.md)
3. [將 ExpressRoute 線路連結到 Azure 虛擬網路](expressroute-howto-linkvnet-arm.md)
