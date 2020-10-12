---
title: 將服務匯流排命名空間移至另一個區域 |Microsoft Docs
description: 本文說明如何將 Azure 服務匯流排命名空間從目前區域移至另一個區域。
ms.topic: how-to
ms.date: 06/23/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 573ea96437cf6cb76854ffa417fd3ad3fb86138b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88860796"
---
# <a name="move-an-azure-service-bus-namespace-to-another-region"></a>將 Azure 服務匯流排命名空間移至另一個區域
在各種情況下，您會想要將現有的服務匯流排命名空間從某個區域移至另一個區域。 例如，您可能會想要使用相同的設定來建立命名空間來進行測試。 您也可能想要在另一個區域中建立次要命名空間，作為嚴重損壞 [修復計畫](service-bus-geo-dr.md)的一部分。

高階步驟如下：

1. 將目前區域中的服務匯流排命名空間匯出至 Azure Resource Manager 範本。 
1. 更新範本中資源的位置。 此外，請從範本移除預設訂用帳戶篩選準則，因為您無法建立預設規則，因為系統會自動為您建立預設規則。 
1. 使用範本將服務匯流排命名空間部署至目的地區域。 
1. 確認部署，以確保所有主題的命名空間、佇列、主題和訂用帳戶都已在目的地區域中建立。 
1. 在處理所有訊息之後，從來源區域刪除命名空間來完成移動。 

## <a name="prerequisites"></a>Prerequisites
確定目的地區域中支援您的帳戶所使用的 Azure 服務匯流排和功能。
 
## <a name="prepare"></a>準備
若要開始使用，請匯出 Resource Manager 範本。 此範本包含描述您的服務匯流排命名空間的設定。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取 [ **所有資源** ]，然後選取您的服務匯流排命名空間。
3. 選取 >**設定**  >  **匯出範本**。
4. 選擇 [**匯出範本**] 頁面中的 [**下載**]。

    :::image type="content" source="./media/move-across-regions/download-template.png" alt-text="下載 Resource Manager 範本":::
5. 找出您從入口網站下載的 .zip 檔案，並將該檔案解壓縮至您選擇的資料夾。 此 zip 檔案包含範本和參數 JSON 檔案。 
1. 在解壓縮的資料夾中開啟檔案 template.js。 
1. 搜尋 `location` ，並將屬性的值取代為區域或位置的新名稱。 若要取得位置代碼，請參閱 [Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。 區域的程式碼是沒有空格的區功能變數名稱稱，例如， `West US` 等於 `westus` 。
1. 移除類型的資源定義： `Microsoft.ServiceBus/namespaces/topics/subscriptions/rules` 。 別忘了移除 `,` 此區段前面的逗號 () 字元，以保持 JSON 有效。  

    > [!NOTE]
    > 您無法使用 Resource Manager 範本建立訂用帳戶的預設規則。 在目的地區域中建立訂用帳戶時，會自動建立預設規則。 

## <a name="move"></a>移動
部署範本，在目的地區域中建立服務匯流排命名空間。 

1. 在 [Azure 入口網站中，選取 [ **建立資源**]。
2. 在 **[搜尋 Marketplace**] 中，輸入 [搜尋文字的 **範本部署** ]，選取 [ **使用自訂範本部署範本部署 (]) **，然後按 **enter**。

    :::image type="content" source="./media/move-across-regions/new-template-deployment.png" alt-text="下載 Resource Manager 範本":::    
1. 在 [ **範本部署** ] 頁面上，選取 [ **建立**]。

    :::image type="content" source="./media/move-across-regions/template-deployment-create-button.png" alt-text="下載 Resource Manager 範本":::        
1. 在 [ **自訂部署** ] 頁面上，選取 **編輯器中的 [建立您自己的範本**]。

    :::image type="content" source="./media/move-across-regions/build-template-link.png" alt-text="下載 Resource Manager 範本":::            
1. 在 [ **編輯範本** ] 頁面上，選取工具列上的 [ **載入** 檔案]，然後依照指示載入您在上一節中下載的 **template.js** 檔案。

    :::image type="content" source="./media/move-across-regions/select-template.png" alt-text="下載 Resource Manager 範本":::                
1. 選取 [ **儲存** ] 以儲存範本。 

    :::image type="content" source="./media/move-across-regions/save-template.png" alt-text="下載 Resource Manager 範本":::                    
1. 在 [ **自訂部署** ] 頁面上，依照下列步驟執行： 
    1. 選取 Azure **訂**用帳戶。 
    2. 選取現有的**資源群組**，或建立一個群組。 
    3. 選取目標 **位置** 或區域。 如果您選取現有的資源群組，這項設定是唯讀的。 
    4. 輸入 **命名空間**的新名稱。
    1. 選取 [檢閱 + 建立]****。 

        :::image type="content" source="./media/move-across-regions/deploy-template.png" alt-text="下載 Resource Manager 範本":::
    1. 在 [ **審核 + 建立** ] 頁面上，選取頁面底部的 [ **建立** ]。 
    
## <a name="verify"></a>Verify
1. 部署成功後，請選取 [ **移至資源群組**]。

    :::image type="content" source="./media/move-across-regions/resource-group-navigation-link.png" alt-text="下載 Resource Manager 範本":::    
1. 在 [ **資源群組** ] 頁面上，選取 [服務匯流排命名空間]。 

    :::image type="content" source="./media/move-across-regions/select-namespace.png" alt-text="下載 Resource Manager 範本":::    
1. 在 [ **服務匯流排命名空間** ] 頁面上，確認您看到來自來源區域的佇列、主題和訂閱。 
    1. 您會在右窗格底部的命名空間中看到 **佇列** 。         
    
        :::image type="content" source="./media/move-across-regions/queue-namespace.png" alt-text="下載 Resource Manager 範本":::
    2. 切換至 [ **主題** ] 索引標籤，以查看命名空間中的主題
    
        :::image type="content" source="./media/move-across-regions/topics-namespace.png" alt-text="下載 Resource Manager 範本":::
    3. 選取主題以確認已建立訂閱。 

        :::image type="content" source="./media/move-across-regions/topic-subscriptions.png" alt-text="下載 Resource Manager 範本":::      
    
    

## <a name="discard-or-clean-up"></a>捨棄或清除
部署之後，如果您想要從頭開始，您可以刪除 **目標服務匯流排命名空間**，並重複本文的「 [準備](#prepare) 和 [移動](#move) 」章節中所述的步驟。

若要認可變更並完成服務匯流排命名空間的移動，請刪除 **來源服務匯流排命名空間**。 在刪除命名空間之前，請確定您已處理所有訊息。 

使用 Azure 入口網站來刪除服務匯流排命名空間 (來源或目標) ：

1. 在 Azure 入口網站頂端的 [搜尋] 視窗中，輸入 **服務匯流排**，然後從搜尋結果中選取 [ **服務匯流排** ]。 您會在清單中看到服務匯流排命名空間。
2. 選取要刪除的目標命名空間，然後從工具列中選取 [ **刪除** ]。 

    ![刪除命名空間-按鈕](./media/move-across-regions/delete-namespace-button.png)
3. 在 [ **刪除資源** ] 頁面上，確認選取的資源，並輸入 [ **是]** 來確認刪除，然後選取 [ **刪除**]。 

    另一個選項是刪除具有服務匯流排命名空間的資源群組。 在 [ **資源群組** ] 頁面上，選取工具列上的 [ **刪除資源群組** ]，然後確認刪除。 

## <a name="next-steps"></a>接下來的步驟

在本教學課程中，您已將 Azure 服務匯流排命名空間從某個區域移至另一個區域，並清除來源資源。  若要深入了解如何在 Azure 中的區域之間移動資源和災害復原，請參閱：

- [將資源移至新的資源群組或訂用帳戶](../azure-resource-manager/management/move-resource-group-and-subscription.md)
