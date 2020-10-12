---
title: 將 Azure 事件中樞專用叢集移至另一個區域 |Microsoft Docs
description: 本文說明如何將 Azure 事件中樞專用叢集從目前區域移至另一個區域。
ms.topic: how-to
ms.date: 09/01/2020
ms.openlocfilehash: 94be44ee8f9442a3a65e899d7a58524b2570f194
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89380640"
---
# <a name="move-an-azure-event-hubs-dedicated-cluster-to-another-region"></a>將 Azure 事件中樞專用叢集移至另一個區域
本文說明如何匯出現有事件中樞專用叢集的 Azure Resource Manager 範本，然後使用範本在另一個區域中建立具有相同設定的叢集。 

如果您在包含事件中樞叢集的 Azure 資源群組中有其他資源（例如命名空間和事件中樞），您可能會想要在資源群組層級匯出範本，以便在一個步驟中將所有相關資源移至新的區域。 本文中的步驟示範如何將 **事件中樞** 叢集匯出至範本。 將 **資源群組** 匯出至範本的步驟很類似。 

## <a name="prerequisites"></a>必要條件
確定可以在目的地區域中建立專用叢集。 要找出的最簡單方式是使用 Azure 入口網站嘗試 [建立事件中樞專用](event-hubs-dedicated-cluster-create-portal.md)叢集。 您會在該時間點看到建立叢集所支援的區域清單。 

## <a name="prepare"></a>準備
若要開始使用，請匯出 Resource Manager 範本。 此範本包含描述事件中樞專用叢集的設定。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取 [ **所有資源** ]，然後選取您的事件中樞專用叢集。
3. 選取 >**設定**  >  **匯出範本**。
4. 選擇 [**匯出範本**] 頁面中的 [**下載**]。

    :::image type="content" source="./media/move-cluster-across-regions/download-template.png" alt-text="下載 Resource Manager 範本" lightbox="./media/move-cluster-across-regions/download-template.png":::
5. 找出您從入口網站下載的 .zip 檔案，並將該檔案解壓縮至您選擇的資料夾。

   此 zip 檔案包含包含範本的 json 檔案，以及用來部署範本的腳本。


## <a name="move"></a>移動

部署範本，在目的地區域中建立事件中樞專用叢集。 


1. 在 [Azure 入口網站中，選取 [ **建立資源**]。
2. 在 **[搜尋 Marketplace**] 中，輸入 **範本部署**，然後選取 [ **使用自訂範本部署 (範本部署]) **。
5. 選取 [在編輯器中組建您自己的範本]。
6. 選取 [ **載入**檔案]，然後依照指示載入您在上一節中下載的 **template.js** 檔案。
1. 將屬性的值更新 `location` 為指向新的區域。 若要取得位置代碼，請參閱 [Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。 區域的程式碼是沒有空格的區功能變數名稱稱，例如， `West US` 等於 `westus` 。
1. 選取 [ **儲存** ] 以儲存範本。 
1. 在 [ **自訂部署** ] 頁面上，依照下列步驟執行： 
    1. 選取 Azure **訂**用帳戶。 
    2. 選取現有的**資源群組**，或建立一個群組。 
    3. 選取目標 **位置** 或區域。 如果您選取現有的資源群組，這項設定是唯讀的。 
    4. 在 [ **設定** ] 區段中，執行下列步驟：    
        1. 輸入新的叢集 **名稱**。 

            :::image type="content" source="./media/move-cluster-across-regions/deploy-template.png" alt-text="下載 Resource Manager 範本":::
    5. 選取頁面底部的 [檢閱 + 建立]。 
    1. 在 [ **審核 + 建立** ] 頁面上，檢查 [設定]，然後選取 [ **建立**]。  

## <a name="discard-or-clean-up"></a>捨棄或清除
部署之後，如果您想要從頭開始，您可以刪除 **目標事件中樞專用**叢集，並重複本文的「 [準備](#prepare) 和 [移動](#move) 」章節中所述的步驟。

若要認可變更並完成事件中樞叢集的移動，請刪除原始區域中的 **事件中樞** 叢集。 

使用 Azure 入口網站來刪除事件中樞叢集 (來源或目標) ：

1. 在 Azure 入口網站頂端的 [搜尋] 視窗中，輸入 **事件中樞**叢集，然後從搜尋結果中選取 [ **事件中樞** 叢集]。 您會在清單中看到事件中樞叢集。
2. 選取要刪除的叢集，然後從工具列中選取 [ **刪除** ]。 
3. 在 [ **刪除** 叢集] 頁面上，輸入叢集 **名稱**來確認刪除，然後選取 [ **刪除**]。 

## <a name="next-steps"></a>後續步驟
在本教學課程中，您已瞭解如何將事件中樞專用叢集從某個區域移至另一個區域。 

請參閱「 [跨區域移動事件中樞命名空間](move-across-regions.md) 」一文，以取得將命名空間從某個區域移至另一個區域的指示。 

若要深入了解如何在 Azure 中的區域之間移動資源和災害復原，請參閱：

- [將資源移至新的資源群組或訂用帳戶](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [將 Azure VM 移至其他區域](../site-recovery/azure-to-azure-tutorial-migrate.md)
