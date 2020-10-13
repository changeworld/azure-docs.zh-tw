---
title: 快速入門：使用 ExpressRoute 建立和修改線路 - Azure 入口網站
description: 在本快速入門中，您會了解如何使用 Azure 入口網站，來建立、佈建、驗證、更新、刪除和取消佈建 ExpressRoute 線路。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: quickstart
ms.date: 10/05/2020
ms.author: duau
ms.openlocfilehash: dbb28726d1c2eaf887ced818284d4b1e0298f8db
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91767817"
---
# <a name="quickstart-create-and-modify-an-expressroute-circuit"></a>快速入門：建立和修改 ExpressRoute 線路

本快速入門說明如何使用 Azure 入口網站和 Azure Resource Manager 部署模型來建立 ExpressRoute 線路。 您也可以檢查狀態、更新、刪除或取消佈建線路。

## <a name="prerequisites"></a>必要條件

* 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 開始設定之前，請檢閱[必要條件](expressroute-prerequisites.md)和[工作流程](expressroute-workflows.md)。
* 您可以在開始前先[觀看影片](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)來進一步了解步驟。

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>建立和佈建 ExpressRoute 線路

### <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

透過瀏覽器瀏覽至 [Azure 入口網站](https://portal.azure.com) ，並使用您的 Azure 帳戶登入。

### <a name="create-a-new-expressroute-circuit"></a>建立新的 ExpressRoute 線路

> [!IMPORTANT]
> ExpressRoute 線路會從發出服務金鑰時開始收費。 請確定在連線提供者準備好佈建線路之後，再執行這項作業。

您可以選取建立新資源的選項來建立 ExpressRoute 線路。 

1. 在 Azure 入口網站功能表上，選取 [建立資源]。 選取 [網路] > [ExpressRoute]，如下圖所示：

    :::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/create-expressroute-circuit-menu.png" alt-text="建立 ExpressRoute 線路":::

2. 選取 [ExpressRoute] 後，您會看到 [建立 ExpressRoute] 頁面。 提供線路的**資源群組**、**區域**和**名稱**。 然後，選取 **下一步：組態 >** 。

    :::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-create-basic.png" alt-text="建立 ExpressRoute 線路":::

3. 在此頁面中填入值時，請確認所指定的 SKU 層 (本機、標準或進階) 以及資料計量計費模型 (無限制或計量付費) 正確無誤。

    :::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-create-configuration.png" alt-text="建立 ExpressRoute 線路":::
    
    * **連接埠類型**會決定您要連線到服務提供者，還是直接在對等互連位置連線到 Microsoft 的全球網路。
    * **建立新的或從傳統匯入**會決定要建立新的線路，還是要將傳統線路遷移至 Azure Resource Manager。
    * **提供者**是網際網路服務提供者，您會向其要求提供服務的來源。
    * **對等位置**是您與 Microsoft 對等互連的實體位置。

    > [!IMPORTANT]
    > [對等位置] 表示您與 Microsoft 對等互連的[實體位置](expressroute-locations.md)。 這**不會**連結到「位置」屬性，這是指 Azure 網路資源提供者所在的地理位置。 儘管它們並無關聯，但最好還是選擇地理位置靠近線路對等位置的網路資源提供者。

    * **SKU** 會判斷系統是否已啟用 ExpressRoute 本機、ExpressRoute 標準或 ExpressRoute 進階附加元件。 您可以指定**本機**來取得本機 SKU，指定**標準**來取得標準 SKU，或指定**進階**來取得進階附加元件。 您可以變更 SKU 以啟用進階附加元件。
    > [!IMPORTANT]
    > 您無法將 SKU 從**標準/進階**變更為**本機**。
    
    * **計費模式**會決定計費類型。 您可以指定 [計量付費]**** 以採用計量付費數據傳輸方案，選取 [無限制]**** 以採用無限制的資料方案。 您可以將計費類型從 [計量付費] 變更為 [無限制]。

    > [!IMPORTANT]
    > 您無法從**無限制** 類型變更為**計量付費**。

    * **允許傳統作業**會允許傳統虛擬網路連結至線路。

### <a name="view-the-circuits-and-properties"></a>檢視線路和屬性

**檢視所有線路**

您可以選取左側功能表上的**所有服務 > 網路 > ExpressRoute 線路**來檢視您建立的所有線路。

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-menu.png" alt-text="建立 ExpressRoute 線路":::

在訂用帳戶中建立的所有 Expressroute 線路都會出現在這裡。

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-list.png" alt-text="建立 ExpressRoute 線路":::

**檢視屬性**

您可選取線路檢視其屬性。 在線路的 [概觀]**** 頁面上，服務金鑰會顯示於服務金鑰欄位。 參考線路的服務金鑰，並提供給服務提供者以完成佈建流程。 此服務金鑰專屬於您的線路。

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-overview.png" alt-text="建立 ExpressRoute 線路":::

### <a name="send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>將服務金鑰傳送給連線提供者以進行佈建

在此頁面上，[提供者狀態] 會提供您服務提供者端的目前佈建狀態。 [線路狀態] 會提供您 Microsoft 端的狀態。 如需線路佈建狀態的詳細資訊，請參閱 [工作流程](expressroute-workflows.md#expressroute-circuit-provisioning-states) 文章。

當您建立新的 ExpressRoute 線路時，線路會是下列狀態：

提供者狀態：**未佈建**<BR>
線路狀態：**已啟用**

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-overview-provisioning-state.png" alt-text="建立 ExpressRoute 線路":::

當連線提供者目前為您啟用線路時，此線路會變更為下列狀態：

提供者狀態：**佈建**<BR>
線路狀態：**已啟用**

若要使用 ExpressRoute 線路，它必須處於下列狀態：

提供者狀態：**已佈建**<BR>
線路狀態：**已啟用**

### <a name="periodically-check-the-status-and-the-state-of-the-circuit-key"></a>定期檢查線路金鑰的情況和狀態

選取感興趣的線路，即可檢視該線路的屬性。 檢查 [提供者狀態]****，確定它已變成 [已佈建]**** 再繼續。

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/provisioned.png" alt-text="建立 ExpressRoute 線路":::

### <a name="create-your-routing-configuration"></a>建立路由組態

如需逐步指示，請參閱 [ExpressRoute 線路路由組態](expressroute-howto-routing-portal-resource-manager.md)一文以建立和修改線路對等。

> [!IMPORTANT]
> 這些指示只適用於由提供第 2 層連線服務的服務提供者所建立的線路。 如果您使用的服務提供者是提供受控第 3 層服務 (通常是 IP VPN，如 MPLS)，您的連線提供者會為您設定和管理路由。

### <a name="link-a-virtual-network-to-an-expressroute-circuit"></a>將虛擬網路連結到 ExpressRoute 線路

接下來，將虛擬網路連結到 ExpressRoute 線路。 當使用 Resource Manager 部署模型時，使用[將虛擬網路連結到 ExpressRoute 線路](expressroute-howto-linkvnet-arm.md)文章。

## <a name="getting-the-status-of-an-expressroute-circuit"></a><a name="status"></a>取得 ExpressRoute 線路的狀態

您可選取線路並檢視 [概觀] 頁面，以檢視該線路的狀態。

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>修改 ExpressRoute 線路

您可以修改 ExpressRoute 線路的某些屬性，而不會影響連線。 您可以在 [組態]**** 頁面中修改頻寬、SKU、計費模型並允許傳統作業。 如需限制的相關資訊，請參閱 [ExpressRoute 常見問題集](expressroute-faqs.md)。

您可以執行下列工作，而無需中途停機：

* 啟用或停用 ExpressRoute 線路的 ExpressRoute 進階附加元件。

> [!IMPORTANT]
  > 不支援將 SKU 從**標準/進階**變更為**本機**。

* 只要連接埠有可用的容量，即增加 ExpressRoute 線路的頻寬。

  > [!IMPORTANT]
  > 不支援將線路的頻寬降級。

* 將計量方案從 [已計量資料]** 變更為 [無限制資料]**。

  > [!IMPORTANT]
  > 不支援將計量方案從**無限制資料**變更為**已計量資料**。

* 您可以啟用和停用 [允許傳統作業] **。
  > [!IMPORTANT]
  > 如果現有的連接埠上沒有足夠的容量，您可能必須重新建立 ExpressRoute 線路。 如果該位置已無額外的容量，您無法升級線路。
  >
  > 雖然您可以順暢升級頻寬，但降低 ExpressRoute 線路的頻寬時必須中斷運作。 頻寬降級需要取消佈建 ExpressRoute 線路，然後重新佈建新的 ExpressRoute 線路。
  >
  > 若您使用的資源超出標準線路所允許的數量，可能會無法停用進階附加元件作業。

若要修改 ExpressRoute 線路，請選取 [組態]。

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-configuration.png" alt-text="建立 ExpressRoute 線路":::

## <a name="deprovisioning-an-expressroute-circuit"></a><a name="delete"></a>取消佈建 ExpressRoute 線路

如果 ExpressRoute 線路服務提供者佈建狀態為 **Provisioning** 或 **Provisioned**，您就必須與服務提供者一起合作，取消佈建他們那邊的線路。 我們會繼續保留資源並向您收取費用，直到線路服務提供者完成取消佈建並通知我們。

> [!NOTE]
>* 您必須取消 ExpressRoute 線路的「所有虛擬網路」連結，才能取消佈建。 如果此操作失敗，請檢查您是否有任何虛擬網路連結至線路。
>* 若服務提供者已取消佈建線路 (服務提供者佈建狀態設定為 [未佈建])，則可以刪除線路。 這樣會停止針對線路計費。


## <a name="clean-up-resources"></a><a name="cleanup"></a>清除資源

您可以選取 [刪除]圖示，以刪除 ExpressRoute 線路。 

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-delete.png" alt-text="建立 ExpressRoute 線路":::

## <a name="next-steps"></a>後續步驟

建立線路後，繼續執行下一步：

> [!div class="nextstepaction"]
> [建立和修改 ExpressRoute 線路的路由](expressroute-howto-routing-portal-resource-manager.md)
