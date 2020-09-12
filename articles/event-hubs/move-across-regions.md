---
title: 將 Azure 事件中樞命名空間移至另一個區域 |Microsoft Docs
description: 本文說明如何將 Azure 事件中樞命名空間從目前區域移至另一個區域。
ms.topic: how-to
ms.date: 09/01/2020
ms.openlocfilehash: b177c3916919e3d97325f9d8c6b6027c00cb476f
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89375188"
---
# <a name="move-an-azure-event-hubs-namespace-to-another-region"></a>將 Azure 事件中樞命名空間移至另一個區域
本文說明如何匯出現有事件中樞命名空間的 Azure Resource Manager 範本，然後使用範本在另一個區域中建立具有相同設定的命名空間。 不過，此進程不會移動尚未處理的事件。 您需要先處理原始命名空間的事件，然後再將其刪除。
 
如果您在包含事件中樞命名空間的 Azure 資源群組中有其他資源，您可能會想要在資源群組層級匯出範本，以便在一個步驟中將所有相關資源移至新的區域。 本文中的步驟會示範如何將 **命名空間** 匯出至範本。 將 **資源群組** 匯出至範本的步驟很類似。 

## <a name="prerequisites"></a>必要條件

- 確定目的地區域中支援您帳戶所使用的服務和功能。
- 如果您已針對命名空間中的事件中樞啟用 **capture 功能** ，請在移動事件中樞命名空間之前，移動 [Azure 儲存體或 Azure Data Lake store gen 2](../storage/common/storage-account-move.md) 或 [Azure Data Lake store gen 1](../data-lake-store/data-lake-store-migration-cross-region.md) 帳戶。 您也可以遵循這篇文章中所述的步驟，將包含儲存體和事件中樞命名空間的資源群組移至另一個區域。 
- 如果事件中樞命名空間在 **事件中樞**叢集中，請先 [將專用叢集移](move-cluster-across-regions.md) 至 **目的地區域** ，再進行本文中的步驟。 您也可以使用 [GitHub 上的快速入門範本](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-cluster-namespace-eventhub/) 來建立事件中樞叢集。 在範本中，移除 JSON 的命名空間部分，只建立叢集。 

## <a name="prepare"></a>準備
若要開始使用，請匯出 Resource Manager 範本。 此範本包含描述事件中樞命名空間的設定。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取 [ **所有資源** ]，然後選取您的事件中樞命名空間。
3. 選取 >**設定**  >  **匯出範本**。
4. 選擇 [**匯出範本**] 頁面中的 [**下載**]。

    ![下載 Resource Manager 範本](./media/move-across-regions/download-template.png)
5. 找出您從入口網站下載的 .zip 檔案，並將該檔案解壓縮至您選擇的資料夾。

   此 zip 檔案包含包含範本的 json 檔案，以及用來部署範本的腳本。


## <a name="move"></a>移動

部署範本，在目的地區域中建立事件中樞命名空間。 


1. 在 [Azure 入口網站中，選取 [ **建立資源**]。
2. 在 **[搜尋 Marketplace**] 中，輸入 **範本部署**，然後選取 [ **使用自訂範本部署 (範本部署]) **。
5. 選取 [在編輯器中組建您自己的範本]。
6. 選取 [ **載入**檔案]，然後依照指示載入您在上一節中下載的 **template.js** 檔案。
1. 將屬性的值更新 `location` 為指向新的區域。 若要取得位置代碼，請參閱 [Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。 區域的程式碼是沒有空格的區功能變數名稱稱，例如， `West US` 等於 `westus` 。
1. 選取 [ **儲存** ] 以儲存範本。 
1. 在 [ **自訂部署** ] 頁面上，依照下列步驟執行： 
    1. 選取 Azure **訂**用帳戶。 
    2. 選取現有的**資源群組**，或建立一個群組。 如果來源命名空間是在事件中樞叢集中，請在目的地區域中選取包含叢集的資源群組。 
    3. 選取目標 **位置** 或區域。 如果您選取現有的資源群組，這項設定是唯讀的。 
    4. 在 [ **設定** ] 區段中，執行下列步驟：    
        1. 輸入新的 **命名空間名稱**。 

            ![部署 Resource Manager 範本](./media/move-across-regions/deploy-template.png)
        2. 如果來源命名空間是在**事件中樞**叢集中，請在 [**外部識別碼**] 中輸入**資源群組**和**事件中樞**叢集的名稱。 

              ```
              /subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<CLUSTER'S RESOURCE GROUP>/providers/Microsoft.EventHub/clusters/<CLUSTER NAME>
              ```   
        3. 如果命名空間中的事件中樞使用儲存體帳戶來捕獲事件，請指定資源組名和欄位的儲存體帳戶 `StorageAccounts_<original storage account name>_external` 。 
            
            ```
            /subscriptions/0000000000-0000-0000-0000-0000000000000/resourceGroups/<STORAGE'S RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>
            ```    
    5. 選取頁面底部的 [檢閱 + 建立]。 
    1. 在 [ **審核 + 建立** ] 頁面上，檢查 [設定]，然後選取 [ **建立**]。   

## <a name="discard-or-clean-up"></a>捨棄或清除
部署之後，如果您想要從頭開始，您可以刪除 **目標事件中樞命名空間**，並重複本文的「 [準備](#prepare) 和 [移動](#move) 」章節中所述的步驟。

若要認可變更並完成事件中樞命名空間的移動，請刪除原始區域中的 **事件中樞命名空間** 。 在刪除命名空間之前，請確定您已處理命名空間中的所有事件。 

使用 Azure 入口網站來刪除事件中樞命名空間 (來源或目標) ：

1. 在 Azure 入口網站頂端的 [搜尋] 視窗中，輸入 **事件中樞**，然後從搜尋結果中選取 **事件中樞** 。 您會在清單中看到事件中樞命名空間。
2. 選取要刪除的目標命名空間，然後從工具列中選取 [ **刪除** ]。 

    ![刪除命名空間-按鈕](./media/move-across-regions/delete-namespace-button.png)
3. 在 [ **刪除命名空間** ] 頁面上，輸入 **命名空間名稱**來確認刪除，然後選取 [ **刪除**]。 

## <a name="next-steps"></a>接下來的步驟

在本教學課程中，您已將 Azure 事件中樞命名空間從某個區域移至另一個區域，並清除來源資源。  若要深入了解如何在 Azure 中的區域之間移動資源和災害復原，請參閱：


- [將資源移至新的資源群組或訂用帳戶](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [將 Azure VM 移至其他區域](../site-recovery/azure-to-azure-tutorial-migrate.md)
