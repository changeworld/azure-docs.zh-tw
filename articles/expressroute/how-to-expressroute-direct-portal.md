---
title: Azure ExpressRoute：設定 ExpressRoute Direct：入口網站
description: 此頁面可協助您使用入口網站設定 ExpressRoute Direct。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 09/29/2020
ms.author: duau
ms.openlocfilehash: dac9df23209fa93b8060ad80d58fd66f7a356ecd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91651474"
---
# <a name="create-expressroute-direct-using-the-azure-portal"></a>使用 Azure 入口網站建立 ExpressRoute Direct

本文說明如何使用 Azure 入口網站建立 ExpressRoute Direct。
ExpressRoute Direct 可讓您在策略性分散于世界各地的對等互連位置，直接連接到 Microsoft 的全球網路。 如需詳細資訊，請參閱[關於 ExpressRoute Direct](expressroute-erdirect-about.md)。

## <a name="before-you-begin"></a><a name="before"></a>開始之前

確認已向您的訂用帳戶註冊 **Microsoft 網路** 資源提供者。 註冊資源提供者可將您的訂用帳戶設定為可搭配資源提供者使用。

1. 存取您的訂用帳戶設定 [，如 Azure 資源提供者和類型](../azure-resource-manager/management/resource-providers-and-types.md)中所述。
1. 在您的訂用帳戶中，針對 **資源提供者**，確認 **Microsoft** 的提供者顯示 **已註冊** 的狀態。 如果已註冊的提供者清單中沒有 Microsoft 網路資源提供者，請新增它。

## <a name="create-expressroute-direct"></a><a name="create-erdir"></a>建立 ExpressRoute Direct

1. 在 [ [Azure 入口網站](https://portal.azure.com) ] 功能表中，或從 **首頁** 選取 [ **建立資源**]。

1. 在 [ **新增** ] 頁面的 [ ***搜尋 Marketplace*** ] 欄位中，輸入 **ExpressRoute Direct**，然後選取 **Enter 鍵** 以取得搜尋結果。

1. 從結果中選取 [ **ExpressRoute Direct**]。

1. 在 **ExpressRoute direct** 頁面上，選取 [ **建立** ] 以開啟 [ **建立 ExpressRoute direct** ] 頁面。

1. 首先，請完成 [ **基本** ] 頁面上的欄位。

    :::image type="content" source="./media/how-to-expressroute-direct-portal/basics.png" alt-text="基本資料頁面":::

    * **訂**用帳戶：您要用來建立新 ExpressRoute Direct 的 Azure 訂用帳戶。 ExpressRoute Direct 資源和 ExpressRoute 線路必須位於相同的訂用帳戶中。
    * **資源群組**：將在其中建立新 ExpressRoute Direct 資源的 Azure 資源群組。 如果您沒有現有的資源群組，您可以建立一個新的資源群組。
    * **區域**：將在其中建立資源的 Azure 公用區域。
    * **名稱**：新 ExpressRoute Direct 資源的名稱。

1. 接下來，完成 [設定] **頁面上的欄位** 。

    :::image type="content" source="./media/how-to-expressroute-direct-portal/configuration.png" alt-text="基本資料頁面":::

    * 對**等互連位置**：您將連接到 ExpressRoute Direct 資源的對等互連位置。 如需對等互連位置的詳細資訊，請參閱 [ExpressRoute 位置](expressroute-locations-providers.md)。
   * **頻寬**：您要保留的埠配對頻寬。 ExpressRoute Direct 支援 10 Gb 和 100 Gb 的頻寬選項。 如果您所需的頻寬不適用於指定的對等互連位置，請 [在 Azure 入口網站中開啟支援要求](https://aka.ms/azsupt)。
   * **封裝**： ExpressRoute Direct 支援 QinQ 和 Dot1Q 封裝。
     * 如果選取 QinQ，則每個 ExpressRoute 線路都會動態獲得指派的 S-Tag，並將成為整個 ExpressRoute Direct 資源中唯一的。
     *  線路上的每個 C-Tag 必須是線路上唯一的，但不是 ExpressRoute Direct 上唯一的。
     * 如果選取 Dot1Q 封裝，您必須管理 C-Tag (VLAN) 在整個 ExpressRoute Direct 資源中的唯一性。
     >[!IMPORTANT]
     >ExpressRoute Direct 只能是一種封裝類型。 建立 ExpressRoute Direct 之後，就無法變更封裝。
     >

1. 指定任何資源標記，然後選取 [ **審核 + 建立** ] 以驗證 ExpressRoute Direct 資源設定。

    :::image type="content" source="./media/how-to-expressroute-direct-portal/validate.png" alt-text="基本資料頁面":::

1. 選取 [建立]****。 您會看到一則訊息，讓您知道您的部署正在進行中。 當建立資源時，狀態將會顯示在此頁面上。 

## <a name="generate-the-letter-of-authorization-loa"></a><a name="authorization"></a>產生 (LOA 的授權信件) 

目前無法從入口網站產生授權信件。 使用 **[Azure PowerShell](expressroute-howto-erdirect.md#authorization)** 或 **[Azure CLI](expressroute-howto-expressroute-direct-cli.md#authorization)** 來取得授權信件。

## <a name="change-admin-state-of-links"></a><a name="state"></a>變更連結的系統管理狀態

此程序應用於進行第 1 層測試，確保每個交叉連線都已在每個主要和次要路由器中正確修補。

1. 在 [ExpressRoute Direct 資源 **總覽** ] 頁面的 [ **連結** ] 區段中，選取 [ **link1**]。

    :::image type="content" source="./media/how-to-expressroute-direct-portal/link.png" alt-text="基本資料頁面" lightbox="./media/how-to-expressroute-direct-portal/link-expand.png":::

1. 將 [ **管理狀態** ] 設定切換為 [ **已啟用**]，然後選取 [ **儲存**]。

    :::image type="content" source="./media/how-to-expressroute-direct-portal/state.png" alt-text="基本資料頁面":::

    >[!IMPORTANT]
    >在任一連結上啟用管理狀態時，就會開始計費。
    >

1. 針對 **link2**重複相同的程式。

## <a name="create-a-circuit"></a><a name="circuit"></a>建立線路

根據預設，您可以在 ExpressRoute Direct 資源所在的訂用帳戶中建立 10 個線路。 此數位可透過支援增加。 您則負責追蹤已佈建和已使用的頻寬。 布建的頻寬是 ExpressRoute Direct 資源上所有線路的頻寬總和。 已使用的頻寬則是基礎實體介面的實際使用量。

* ExpressRoute Direct 上有只可用於支援以上所述案例的額外線路頻寬。 這些是： 40 Gbps 和 100 Gbps。

* SkuTier 可以是 Local、Standard 或 Premium。

* SkuFamily 必須只有 [Metereddata。 ExpressRoute Direct 不支援無限制。

下列步驟可協助您從 ExpressRoute Direct 工作流程建立 ExpressRoute 線路。 如果您想要的話，也可以使用一般的電路工作流程來建立電路，不過在使用此設定的一般電路工作流程步驟時，並沒有任何好處。 請參閱 [建立和修改 ExpressRoute 線路](expressroute-howto-circuit-portal-resource-manager.md)。

1. 在 [ExpressRoute Direct **設定** ] 區段中，選取 [ **線路**]，然後選取 [ **+ 新增**]。 

    :::image type="content" source="./media/how-to-expressroute-direct-portal/add.png" alt-text="基本資料頁面" lightbox="./media/how-to-expressroute-direct-portal/add-expand.png":::

1. 設定 [設定 **] 頁面中的設定** 。

   :::image type="content" source="./media/how-to-expressroute-direct-portal/configuration2.png" alt-text="基本資料頁面":::

1. 指定任何資源標記、選取 **審核 + 建立** ，以便在建立資源之前驗證值。

   :::image type="content" source="./media/how-to-expressroute-direct-portal/review.png" alt-text="基本資料頁面":::

1. 選取 [建立]****。 您會看到一則訊息，讓您知道您的部署正在進行中。 當建立資源時，狀態將會顯示在此頁面上。 

## <a name="next-steps"></a>後續步驟

如需 ExpressRoute Direct 的詳細資訊，請參閱 [總覽](expressroute-erdirect-about.md)。
