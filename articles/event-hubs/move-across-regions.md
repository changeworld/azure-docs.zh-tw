---
title: 將 Azure 事件中樞命名空間移至另一個區域 |Microsoft Docs
description: 本文說明如何將 Azure 事件中樞命名空間從目前的區域移至另一個區域。
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 04/14/2020
ms.author: spelluru
ms.reviewer: shvija
ms.openlocfilehash: 2dfc9c517605bbb48bee0b306fb275464cfebe39
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81606804"
---
# <a name="move-an-azure-event-hubs-namespace-to-another-region"></a>將 Azure 事件中樞命名空間移至另一個區域
在許多情況下，您會想要將現有的事件中樞命名空間移至另一個區域。 例如，您可能會想要使用相同的設定來建立命名空間來進行測試。 您可能也會想要在其他區域中建立次要命名空間，做為嚴重損壞[修復計畫](event-hubs-geo-dr.md#setup-and-failover-flow)的一部分。

> [!NOTE]
> 本文說明如何匯出現有事件中樞命名空間的 Azure Resource Manager 範本，然後使用範本，在另一個區域中建立具有相同設定的命名空間。 不過，此進程不會移動尚未處理的事件。 您必須先處理原始命名空間中的事件，然後再刪除它。

## <a name="prerequisites"></a>先決條件

- 確定目的地區域中支援您的帳戶所使用的服務和功能。
- 針對預覽功能，請確定您的訂用帳戶已列入目的地區域的允許清單中。
- 如果您已為命名空間中的事件中樞啟用**capture 功能**，請在移動事件中樞命名空間之前，先移動[Azure 儲存體或 Azure Data Lake 存放區 gen 2](../storage/common/storage-account-move.md)或[Azure Data Lake 存放區 gen 1](../data-lake-store/data-lake-store-migration-cross-region.md)帳戶。 您也可以遵循這篇文章中所述的步驟，將包含儲存體和事件中樞命名空間的資源群組移至另一個區域。 
- 如果事件中樞命名空間位於**事件中樞**叢集中，請在執行本文中的步驟之前，先在**目的地區域**中[建立專用的](event-hubs-dedicated-cluster-create-portal.md)叢集。 

## <a name="prepare"></a>準備
若要開始使用，請匯出 Resource Manager 範本。 此範本包含描述事件中樞命名空間的設定。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 選取 [**所有資源**]，然後選取您的事件中樞命名空間。

3. 選取 >**設定** > ] [**匯出範本**]。

4. 在 [**匯出範本**] 頁面中選擇 [**下載**]。

    ![下載 Resource Manager 範本](./media/move-across-regions/download-template.png)

5. 找出您從入口網站下載的 .zip 檔案，並將該檔案解壓縮至您選擇的資料夾。

   此 zip 檔案包含包含範本的 json 檔案，以及用來部署範本的腳本。


## <a name="move"></a>移動

部署範本，以在目的地區域中建立事件中樞命名空間。 


1. 在 [Azure 入口網站中，選取 [**建立資源**]。

2. 在 [搜尋 Marketplace]**** 中，輸入**範本部署**，然後按 **ENTER**。

3. 選取 [**範本部署**]。

4. 選取 [建立]  。

5. 選取 [在編輯器中組建您自己的範本]****。

6. 選取 [**載入**檔案]，然後依照指示載入您在上一節中下載的**範本. json**檔案。

7. 選取 [**儲存**] 以儲存範本。 

8. 在 [**自訂部署**] 頁面上，依照下列步驟執行： 

    1. 選取 Azure**訂**用帳戶。 

    2. 選取現有的**資源群組**，或建立一個群組。 如果來源命名空間在事件中樞叢集中，請選取在目的地區域中包含叢集的資源群組。 

    3. 選取目標**位置**或區域。 如果您選取現有的資源群組，則此設定為唯讀。 

    4. 在 [**設定**] 區段中，執行下列步驟：
    
        1. 輸入新的**命名空間名稱**。 

            ![部署 Resource Manager 範本](./media/move-across-regions/deploy-template.png)

        2. 如果您的來源命名空間在**事件中樞**叢集中，請輸入**資源群組**的名稱，並**事件中樞**叢集作為**外部識別碼**的一部分。 

              ```
              /subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<CLUSTER'S RESOURCE GROUP>/providers/Microsoft.EventHub/clusters/<CLUSTER NAME>
              ```   
        3. 如果命名空間中的事件中樞使用儲存體帳戶來捕捉事件，請指定資源組名和 [儲存體帳戶] `StorageAccounts_<original storage account name>_external`作為 [欄位]。 
            
            ```
            /subscriptions/0000000000-0000-0000-0000-0000000000000/resourceGroups/<STORAGE'S RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>
            ```    
    5. 選取 [我同意上方所述的條款及條件]**** 核取方塊。 
    
    6. 現在，選取 [**選取購買**] 以開始部署程式。 

## <a name="discard-or-clean-up"></a>捨棄或清除
部署之後，如果您想要從頭開始，您可以刪除**目標事件中樞命名空間**，並重複本文的[準備](#prepare)和[移動](#move)章節中所述的步驟。

若要認可變更並完成事件中樞命名空間的移動，請刪除**來源事件中樞命名空間**。 請確定您已處理命名空間中的所有事件，然後再刪除命名空間。 

若要使用 Azure 入口網站刪除事件中樞命名空間（來源或目標）：

1. 在 [Azure 入口網站頂端的 [搜尋] 視窗中，輸入**事件中樞**，然後從搜尋結果中選取 [**事件中樞**]。 您會在清單中看到事件中樞的命名空間。

2. 選取要刪除的目標命名空間，然後從工具列中選取 [**刪除**]。 

    ![刪除命名空間-按鈕](./media/move-across-regions/delete-namespace-button.png)

3. 在 [**刪除資源**] 頁面上，確認選取的資源，然後輸入 **[是]** 來確認刪除，然後選取 [**刪除**]。 

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已將 Azure 事件中樞命名空間從一個區域移至另一個區域，並清除來源資源。  若要深入瞭解如何在 Azure 中的區域和嚴重損壞修復之間移動資源，請參閱：


- [將資源移至新的資源群組或訂用帳戶](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [將 Azure VM 移動到另一個區域](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
