---
title: 將 Azure 事件方格網域移至另一個區域
description: 本文說明如何將 Azure 事件方格網域從某個區域移至另一個區域。
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/20/2020
ms.openlocfilehash: fff8638a819511f84f15c52ad0695cdd5759f971
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89083633"
---
# <a name="move-azure-event-grid-domains-to-another-region"></a>將 Azure 事件方格網域移至另一個區域
您可能會因為許多原因而想要將資源移到另一個區域。 例如，若要利用新的 Azure 區域，以符合內部原則和治理需求，或為了回應容量規劃需求。 

以下是本文所涵蓋的高階步驟： 

- 將**網域資源匯出**至 Azure Resource Manager 範本。 

    > [!IMPORTANT]
    > 網域中的網域資源和主題會匯出到範本中。 未匯出網域主題的訂閱。 
- **使用範本將網域部署** 至目的地區域。 
- 在目的地區域中**手動建立網域主題的訂閱**。 當您將網域匯出至目前區域中的範本時，不會匯出網域主題的訂閱。 因此，請在目的地區域中建立網域和網域主題之後建立它們。 
- **確認部署**。 將事件傳送至網域中的網域主題，並確認已叫用與訂用帳戶相關聯的事件處理常式。 
- 若要 **完成移動**，請從來源區域刪除網域。 

## <a name="prerequisites"></a>必要條件
- 確定事件方格服務可在目的地區域中使用。 請參閱[依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all)。

## <a name="prepare"></a>準備
若要開始使用，請匯出網域的 Resource Manager 範本。 

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在搜尋列中，輸入 **事件方格網域**，然後從結果清單中選取 **事件方格網域** 。 

    :::image type="content" source="./media/move-domains-across-regions/search-domains.png" alt-text="搜尋並選取事件方格網域":::
3. 選取您要匯出至 Resource Manager 範本的 **網域** 。 

    :::image type="content" source="./media/move-domains-across-regions/select-domain.png" alt-text="選取網域":::   
4. 在 [**事件方格網域**] 頁面上，選取左側功能表的 [**設定**] 底下的 [**匯出範本**]，然後在工具列上選取 [**下載**]。 

    :::image type="content" source="./media/move-domains-across-regions/export-template-download.png" alt-text="匯出範本-> 下載" lightbox="./media/move-domains-across-regions/export-template-download.png":::   

    > [!IMPORTANT]
    > 匯出網域和網域主題。 不會匯出網域主題的訂閱。 因此，您必須在移動網域主題之後，建立網域主題的訂閱。 
5. 找出您從入口網站下載的 **.zip** 檔案，並將該檔案解壓縮至您選擇的資料夾。 此 zip 檔案包含範本和參數的 JSON 檔案。 
1. 在您選擇的編輯器中開啟 **template.js** 。 
8. 將 `location` **網域** 資源更新為目的地區域或位置。 若要取得位置代碼，請參閱 [Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。 區域的程式碼是沒有空格的區功能變數名稱稱，例如， `West US` 等於 `westus` 。

    ```json
    "type": "Microsoft.EventGrid/domains",
    "apiVersion": "2020-06-01",
    "name": "[parameters('domains_spegriddomain_name')]",
    "location": "westus",
    ```
1. **儲存** 範本。 

## <a name="recreate"></a>重建 
部署範本，在目的地區域中建立網域和網域主題。 

1. 在 [Azure 入口網站中，選取 [ **建立資源**]。
2. 在 **[搜尋 Marketplace**] 中，輸入 **範本部署**，然後按 **enter**。
3. 選取 **範本部署**。
4. 選取 [建立]。
5. 選取 [在編輯器中組建您自己的範本]。
6. 選取 [ **載入**檔案]，然後依照指示載入您在上一節中下載的 **template.js** 檔案。
7. 選取 [ **儲存** ] 以儲存範本。 
8. 在 [ **自訂部署** ] 頁面上，依照下列步驟執行：
    1. 選取 Azure **訂**用帳戶。 
    1. 選取目的地區域中的現有 **資源群組** ，或建立一個。 
    1. 針對 [ **區域**]，選取目的地區域。 如果您選取現有的資源群組，這項設定是唯讀的。 
    1. 在 [ **功能變數名稱**] 中，輸入網域的新名稱。 
    1. 選取 [檢閱 + 建立]。 
    
        :::image type="content" source="./media/move-domains-across-regions/deploy-template.png" alt-text="部署範本":::        
    1. 成功驗證範本之後，請選取頁面底部的 [ **建立** ] 以部署資源。 
    1. 部署成功後，請選取 [ **移至資源群組** ] 以流覽至 [資源群組] 頁面。 確認資源群組中有一個網域。 選取網域。 確認網域中有網域主題。 

## <a name="discard-or-clean-up"></a>捨棄或清除
若要完成移動，請刪除來源區域中的網域。  

如果您想要重新開始，請刪除目的地區域中的網域，然後重複本文的「 [準備](#prepare) 和 [重新](#recreate) 建立」各節中的步驟。

若要使用 Azure 入口網站刪除網域：

1. 在 Azure 入口網站頂端的 [搜尋] 視窗中，輸入 **事件方格網域**，然後從搜尋結果中選取 **事件方格網域** 。 
2. 選取要刪除的網域，然後從工具列中選取 [ **刪除** ]。 
3. 在 [確認] 頁面上，輸入資源群組的名稱，然後選取 [ **刪除**]。  

使用 Azure 入口網站來刪除包含網域的資源群組：

1. 在 Azure 入口網站頂端的 [搜尋] 視窗中，輸入 **資源群組**，然後從搜尋結果中選取 **資源群組** 。 
2. 選取要刪除的資源群組，然後從工具列中選取 [ **刪除** ]。 
3. 在 [確認] 頁面上，輸入資源群組的名稱，然後選取 [ **刪除**]。  

## <a name="next-steps"></a>後續步驟
您已瞭解如何將事件方格網域從某個區域移至另一個區域。 請參閱下列文章，以瞭解如何跨區域移動系統主題、自訂主題和夥伴命名空間。

- [跨區域移動系統主題](move-system-topics-across-regions.md)。 
- [跨區域移動自訂主題](move-custom-topics-across-regions.md)。 
- [跨區域移動夥伴命名空間](move-partner-namespaces-across-regions.md)。

若要深入瞭解如何在 Azure 中的區域與災難復原之間移動資源，請參閱下列文章： [將資源移至新的資源群組或訂](../azure-resource-manager/management/move-resource-group-and-subscription.md)用帳戶。