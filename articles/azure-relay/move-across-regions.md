---
title: 將 Azure 轉送命名空間移至另一個區域
description: 本文說明如何將 Azure 轉送命名空間從目前區域移至另一個區域。
ms.topic: how-to
ms.date: 09/03/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 60a182764639341fcda159356dd9fe6c65cfabd9
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2020
ms.locfileid: "89463492"
---
# <a name="move-an-azure-relay-namespace-to-another-region"></a>將 Azure 轉送命名空間移至另一個區域
本文說明如何將 Azure 轉送命名空間從某個區域移至另一個區域。 高階步驟如下：

1. 將轉送命名空間**匯出**至 Azure Resource Manager 範本。
1. 更新範本中資源的**位置 (區域) ** 。 此外，也請刪除範本中的任何 **動態** WCF 轉送。 

    WCF 轉送有兩種模式。 在第一個模式中，會使用 Azure 入口網站或 Azure Resource Manager 範本明確建立 WCF 轉送。 在 Azure 入口網站的 [ **WCF** 轉送] 頁面上，您會看到在此模式中，轉送的 **isDynamic** 屬性設定為 **false** 。 

    在第二個模式中，當接聽項 (server) 連接到指定的端點位址時，就會自動產生 WCF 轉送。 只要接聽程式已連線到轉送，您就會在 Azure 入口網站的 WCF 轉送清單中看到轉送。 若為這種模式的轉送， **isDynamic** 屬性會設定為 **true** ，因為它是動態產生的。 當接聽程式中斷連線時，動態 WCF 轉送會消失。 
1. 使用範本將資源**部署**至目的地區域。

## <a name="prerequisites"></a>必要條件
確認 Azure 轉送服務可在目的地區域中使用。 請參閱[依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/?products=service-bus&regions=all)。 
 
## <a name="prepare"></a>準備
若要開始使用，請匯出 Resource Manager 範本。 此範本包含描述 Azure 轉送命名空間的設定。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取 [ **所有資源** ]，然後選取您的 Azure 轉送命名空間。
3. 在左側功能表的 [**設定**] 底下選取 [**匯出範本**]。
4. 選擇 [**匯出範本**] 頁面上的 [**下載**]。

    :::image type="content" source="./media/move-across-regions/download-template.png" alt-text="下載 Resource Manager 範本":::
5. 找出您從入口網站下載的 .zip 檔案，並將該檔案解壓縮至您選擇的資料夾。 此 zip 檔案包含範本和參數 JSON 檔案。 
1. 在您選擇的編輯器中，從解壓縮的資料夾開啟 **template.js** 的檔案。
1. 搜尋 `location` ，並將屬性的值取代為該區域的新名稱。 若要取得位置代碼，請參閱 [Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。 區域的程式碼是沒有空格的區功能變數名稱稱，例如， `West US` 等於 `westus` 。
1. 移除 **動態 WCF 轉送** 資源的定義 (類型： `Microsoft.Relay/namespaces/WcfRelays`) 。 動態 WCF 轉送是在 [轉送 **] 頁面上將 [** **isDynamic** ] 屬性設定為 [ **true** ] 的轉送。 在下列範例中， **echoservice** 是動態 WCF 轉送，而且應該從範本中移除其定義。 

    :::image type="content" source="./media/move-across-regions/dynamic-relays.png" alt-text="動態轉送":::

## <a name="move"></a>移動
部署範本，在目的地區域中建立轉送命名空間。 

1. 在 [Azure 入口網站中，選取 [ **建立資源**]。
2. 在 **[搜尋 Marketplace**] 中，輸入 [搜尋文字的 **範本部署** ]，選取 [ **使用自訂範本部署範本部署 (]) **，然後按 **enter**。

    :::image type="content" source="./media/move-across-regions/new-template-deployment.png" alt-text="新範本部署":::    
1. 在 [ **範本部署** ] 頁面上，選取 [ **建立**]。

    :::image type="content" source="./media/move-across-regions/template-deployment-create-button.png" alt-text="新範本部署-建立按鈕":::        
1. 在 [ **自訂部署** ] 頁面上，選取 **編輯器中的 [建立您自己的範本**]。

    :::image type="content" source="./media/move-across-regions/build-template-link.png" alt-text="在編輯器中建立您自己的範本-連結":::            
1. 在 [ **編輯範本** ] 頁面上，選取工具列上的 [ **載入** 檔案]，然後依照指示載入您在上一節中下載的 **template.js** 檔案。

    :::image type="content" source="./media/move-across-regions/select-template.png" alt-text="選取範本":::                
1. 選取 [ **儲存** ] 以儲存範本。 

    :::image type="content" source="./media/move-across-regions/save-template.png" alt-text="儲存範本":::                    
1. 在 [ **自訂部署** ] 頁面上，依照下列步驟執行： 
    1. 選取 Azure **訂**用帳戶。 
    2. 選取現有的**資源群組**，或建立一個群組。 
    3. 選取目標 **位置** 或區域。 如果您選取現有的資源群組，這項設定是唯讀的。 
    4. 輸入 **命名空間**的新名稱。
    1. 選取 [檢閱 + 建立]。 

        :::image type="content" source="./media/move-across-regions/deploy-template.png" alt-text="部署 Resource Manager 範本":::
    1. 在 [ **審核 + 建立** ] 頁面上，選取頁面底部的 [ **建立** ]。 
    
## <a name="verify"></a>驗證
1. 部署成功後，請選取 [ **移至資源群組**]。

    :::image type="content" source="./media/move-across-regions/resource-group-navigation-link.png" alt-text="移至資源群組連結":::    
1. 在 [ **資源群組** ] 頁面上，選取 Azure 轉送命名空間。 

    :::image type="content" source="./media/move-across-regions/select-namespace.png" alt-text="選取 Azure 轉送命名空間":::    
1. 在 [ **Azure 轉送命名空間** ] 頁面上，選取左側功能表上的 [ **混合式連接** ] 或 [ **wcf** 轉送]，以確認已建立混合式連線和 wcf 轉送。 如果您忘記先刪除動態 WCF 轉送的定義，然後再匯入範本，請在 [ **WCF** 轉送] 頁面上刪除它們。 當用戶端連接到轉送命名空間時，就會自動建立動態 WCF 轉送。 

## <a name="discard-or-clean-up"></a>捨棄或清除
部署之後，如果您想要從頭開始，您可以刪除 **目標 Azure 轉送命名空間**，並重複本文的「 [準備](#prepare) 和 [移動](#move) 」章節中所述的步驟。

若要認可變更並完成命名空間的移動，請刪除來源區域中的 **Azure 轉送命名空間** 。 

使用 Azure 入口網站刪除 Azure 轉送命名空間 (來源或目標) ：

1. 在 Azure 入口網站頂端的 [搜尋] 視窗中 **，輸入轉送**，然後從搜尋結果中的 [**服務** **] 選取 [** 轉送]。 您會在清單中看到所有 Azure 轉送命名空間。
2. 選取要刪除的目標命名空間，以開啟 [ **轉送** ] 頁面。 
1. 在 [ **轉送** ] 頁面上，從工具列中選取 [ **刪除** ]。 

    ![刪除命名空間-按鈕](./media/move-across-regions/delete-namespace-button.png)
3. 在 [ **刪除命名空間** ] 頁面上，輸入 Azure 轉送命名空間的名稱以確認刪除，然後選取 [ **刪除**]。 

## <a name="next-steps"></a>接下來的步驟
在本教學課程中，您已將 Azure 轉送命名空間從某個區域移至另一個區域。 若要深入了解如何在 Azure 中的區域之間移動資源和災害復原，請參閱：

- [將資源移至新的資源群組或訂用帳戶](../azure-resource-manager/management/move-resource-group-and-subscription.md)
