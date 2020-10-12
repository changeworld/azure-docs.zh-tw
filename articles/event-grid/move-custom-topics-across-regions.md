---
title: 將 Azure 事件方格自訂主題移至另一個區域
description: 本文說明如何將 Azure 事件方格自訂主題從一個區域移至另一個區域。
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/28/2020
ms.openlocfilehash: d0656a4f6ec1c7431cf7111f786b0f1d779166e3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89145329"
---
# <a name="move-azure-event-grid-custom-topics-to-another-region"></a>將 Azure 事件方格自訂主題移至另一個區域
您可能會因為許多原因而想要將資源移到另一個區域。 例如，若要利用新的 Azure 區域，以符合內部原則和治理需求，或為了回應容量規劃需求。 

以下是本文所涵蓋的高階步驟： 

- 將**自訂主題資源匯出**至 Azure Resource Manager 範本。 

    > [!IMPORTANT]
    > 只會將自訂主題匯出至範本。 本主題的任何訂閱都不會匯出。
- **使用範本將自訂主題部署** 至目的地區域。 
- 在目的地區域中**手動建立訂閱**。 當您將自訂主題匯出至目前區域中的範本時，只會匯出該主題。 訂用帳戶不包含在範本中，因此請在目的地區域中建立自訂主題之後，手動建立訂閱。 
- **確認部署**。 確認已在目的地區域中建立自訂主題。 
- 若要 **完成移動**，請從來源區域刪除自訂主題。 

## <a name="prerequisites"></a>必要條件
- 完成快速入門：在來源區域中 [將自訂事件路由至 web 端點](custom-event-quickstart-portal.md) 。 執行此步驟，讓您可以測試本文中的步驟。 
- 確定事件方格服務可在目的地區域中使用。 請參閱[依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all)。

## <a name="prepare"></a>準備
若要開始使用，請匯出自訂主題的 Resource Manager 範本。 

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在搜尋列中，輸入 **事件方格主題**，然後從結果清單中選取 **事件方格主題** 。 

    :::image type="content" source="./media/move-custom-topics-across-regions/search-topics.png" alt-text="搜尋並選取事件方格主題":::
3. 選取您要匯出至 Resource Manager 範本的 **主題** 。 

    :::image type="content" source="./media/move-custom-topics-across-regions/select-custom-topic.png" alt-text="搜尋並選取事件方格主題":::   
4. 在**事件方格主題**頁面上，選取左側功能表的 **[設定**] 底下的 [**匯出範本**]，然後在工具列上選取 [**下載**]。 

    :::image type="content" source="./media/move-custom-topics-across-regions/export-template-download.png" alt-text="搜尋並選取事件方格主題"
    ```
1. **儲存** 範本。 

## <a name="recreate"></a>重建 
部署範本，在目的地區域中建立自訂主題。 

1. 在 [Azure 入口網站中，選取 [ **建立資源**]。
2. 在 **[搜尋 Marketplace**] 中，輸入 **範本部署**，然後按 **enter**。
3. 選取 **範本部署**。
4. 選取 [建立]****。
5. 選取 [在編輯器中組建您自己的範本]。
6. 選取 [ **載入**檔案]，然後依照指示載入您在上一節中下載的 **template.js** 檔案。
7. 選取 [ **儲存** ] 以儲存範本。 
8. 在 [ **自訂部署** ] 頁面上，依照下列步驟執行： 
    1. 選取 Azure **訂**用帳戶。 
    1. 選取目的地區域中的現有 **資源群組** ，或建立一個。 
    1. 針對 [ **區域**]，選取目的地區域。 如果您選取現有的資源群組，這項設定是唯讀的。 
    1. 針對 [ **主題名稱**]，輸入主題的新名稱。 
    1. 選取頁面底部的 [檢閱 + 建立]。 
    
        :::image type="content" source="./media/move-custom-topics-across-regions/deploy-template.png" alt-text="搜尋並選取事件方格主題":::
    1. 在 [ **審核 + 建立** ] 頁面上，檢查 [設定]，然後選取 [ **建立**]。 

## <a name="verify"></a>Verify

1. 部署成功之後，選取 [移至資源]****。 

    :::image type="content" source="./media/move-custom-topics-across-regions/navigate-custom-topic.png" alt-text="搜尋並選取事件方格主題":::
1. 確認您看到自訂主題的 **事件方格主題** 頁面。   
1. 依照將 [自訂事件路由至 web 端點](custom-event-quickstart-portal.md#send-an-event-to-your-topic) 的步驟，將事件傳送至主題。 確認已叫用 webhook 事件處理常式。 

## <a name="discard-or-clean-up"></a>捨棄或清除
若要完成移動，請刪除來源區域中的自訂主題。  

如果您想要重新開始，請刪除目的地區域中的主題，並重複本文的「 [準備](#prepare) 和 [重新](#recreate) 建立」各節中的步驟。

若要使用 Azure 入口網站刪除自訂主題：

1. 在 Azure 入口網站頂端的 [搜尋] 視窗中，輸入 **事件方格主題**，然後從搜尋結果中選取 **事件方格主題** 。 
2. 選取要刪除的主題，然後從工具列中選取 [ **刪除** ]。 
3. 在 [確認] 頁面上，輸入資源群組的名稱，然後選取 [ **刪除**]。  

若要使用 Azure 入口網站來刪除包含自訂主題的資源群組：

1. 在 Azure 入口網站頂端的 [搜尋] 視窗中，輸入 **資源群組**，然後從搜尋結果中選取 **資源群組** 。 
2. 選取要刪除的資源群組，然後從工具列中選取 [ **刪除** ]。 
3. 在 [確認] 頁面上，輸入資源群組的名稱，然後選取 [ **刪除**]。  

## <a name="next-steps"></a>後續步驟
您已瞭解如何將事件方格自訂主題從一個區域移至另一個區域。 請參閱下列文章，以瞭解如何跨區域移動系統主題、網域及夥伴命名空間。

- [跨區域移動系統主題](move-system-topics-across-regions.md)。 
- [跨區域移動網域](move-domains-across-regions.md)。 
- [跨區域移動夥伴命名空間](move-partner-namespaces-across-regions.md)。

若要深入瞭解如何在 Azure 中的區域與災難復原之間移動資源，請參閱下列文章： [將資源移至新的資源群組或訂](../azure-resource-manager/management/move-resource-group-and-subscription.md)用帳戶。