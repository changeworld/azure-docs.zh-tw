---
title: 教學課程：在 Azure 入口網站中管理查詢
description: 在本教學課程中，您將建立 Resource Graph 查詢，並在 Azure 入口網站中與其他人共用新查詢。
ms.date: 10/14/2020
ms.topic: tutorial
ms.openlocfilehash: 98d4c7d0493686a45bdfef9d85823432cb637a66
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92057921"
---
# <a name="tutorial-create-and-share-an-azure-resource-graph-query-in-the-azure-portal"></a>教學課程：在 Azure 入口網站中建立和共用 Azure Resource Graph 查詢

Azure Resource Graph Explorer 可讓您直接在 Azure 入口網站中儲存 Resource Graph 查詢。 查詢分成兩種類型：_私人_和_共用_。 私人查詢會儲存在您的 Azure 入口網站設定中。 共用查詢是一種 Azure Resource Manager 資源，可以使用 Azure 角色型存取控制 (Azure RBAC) 來管理，並使用資源鎖定加以保護。 這兩種類型的查詢都會在待用時加密。

將查詢儲存在 Azure 入口網站中，可讓您省下在尋找慣用或常用查詢時可能耗費的時間。 在共用查詢時，您將協助小組成員透過重複的動作實現一致性和效率的目標。

在本教學課程中，您將完成下列工作：

> [!div class="checklist"]
> - 建立和刪除私人查詢
> - 建立共用查詢
> - 探索共用查詢
> - 刪除共用查詢

## <a name="prerequisites"></a>必要條件

若要完成此教學課程，您需要 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

## <a name="create-and-delete-a-private-query"></a>建立和刪除私人查詢

只有建立私人查詢的帳戶才可存取及檢視這些查詢。 這些查詢儲存於帳戶的 Azure 入口網站設定後，就只能在 Azure 入口網站中加以建立、使用和刪除。 私人查詢不是 Resource Manager 資源。 若要建立新的私人查詢，請執行下列步驟：

1. 從入口網站功能表中選取 [所有服務]，或使用所有頁面頂端的 Azure 搜尋方塊。 搜尋並選取 [Resource Graph Explorer]。

1. 在 [Azure Resource Graph Explorer] 頁面的 [查詢 1] 索引標籤上，輸入下列查詢：

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```

   選取 [執行查詢]，以查看底部窗格中的查詢結果。

   如需此查詢的詳細資訊，請參閱[範例 - 依作業系統類型計算虛擬機器計數](../samples/starter.md#count-os)。

1. 選取 [儲存] 或 [另存新檔]，輸入「依作業系統計算 VM 計數」作為名稱，並將類型保留為 [私人查詢]，然後在 [儲存查詢] 窗格底部選取 [儲存]。 索引標籤標題會從「查詢 1」變更為「依作業系統計算 VM 計數」。

1. 從 Azure 入口網站中的 Azure Resource Graph Explorer 移出，然後再返回該處。 請注意，已儲存的查詢已不再顯示，而會回復為 [查詢 1] 索引標籤。

1. 選取 [開啟查詢]。 請確定類型為 [私人查詢]。 此時，已儲存的名稱「依作業系統計算 VM 計數」會出現在 [查詢名稱] 清單中。 當您選取已儲存查詢的標題連結時，該查詢會載入至具有該查詢名稱的新索引標籤中。

   > [!NOTE] 
   > 當已儲存的查詢開啟，且索引標籤顯示其名稱時，若選取 [儲存] 按鈕，將會以已進行的任何變更來更新該查詢。 若要從這個開啟的查詢建立新儲存的查詢，請選取 [另存新檔] 並繼續操作，如同儲存全新的查詢一般。

1. 若要刪除已儲存的查詢，請再次選取 [開啟查詢]，並確認 [類型] 欄位設定為 [私人查詢]。 在已儲存的 `Count VMs by OS` 查詢的資料列上，選取 [刪除] (垃圾桶圖示)。 在確認對話方塊中選取 [是]，以完成查詢的刪除作業。
   然後，關閉 [開啟查詢] 窗格。

## <a name="create-a-shared-query"></a>建立共用查詢

不同於私人查詢，共用查詢是 Resource Manager 資源。 這表示查詢會儲存至資源群組、可透過 Azure RBAC 來管理和控制，甚至可使用資源鎖定加以保護。 作為資源，任何人只要具有適當的權限，即可加以檢視和使用。 若要建立新的共用查詢，請執行下列步驟：

1. 從入口網站功能表中選取 [所有服務]，或使用所有頁面頂端的 Azure 搜尋方塊，來搜尋並選取 [Resource Graph Explorer]。

1. 在 [Azure Resource Graph Explorer] 頁面的 [查詢 1] 索引標籤上，輸入下列查詢：

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```
    
   選取 [執行查詢]，以查看底部窗格中的查詢結果。

   如需此查詢的詳細資訊，請參閱[範例 - 依作業系統類型計算虛擬機器計數](../samples/starter.md#count-os)。

1. 選取 [儲存] 或 [另存新檔]。

   :::image type="content" source="../media/create-share-query/save-shared-query-buttons.png" alt-text="使用儲存按鈕來儲存新的查詢" border="false":::

1. 在 [儲存查詢] 窗格中，輸入「依作業系統計算 VM 計數」作為名稱。

1. 將類型變更為 [共用查詢]，並將 [描述] 設定為 [依作業系統類型計算虛擬機器計數]，然後設定 [訂用帳戶] 以指定查詢資源的建立位置。

1. 讓 [發佈至 resource-graph-queries 資源群組] 核取方塊保持選取狀態，並將 [資源群組位置] 設定為 [(US) 美國中西部]。

1. 選取 [儲存查詢] 窗格底部的 [儲存]。 索引標籤標題會從「查詢 1」變更為「依作業系統計算 VM 計數」。 第一次使用 **resource-graph-queries** 資源群組時，建立資源群組所需的儲存時間會比預期還要久。
   
   :::image type="content" source="../media/create-share-query/save-shared-query-window.png" alt-text="使用儲存按鈕來儲存新的查詢" border="false":::

   > [!NOTE] 
   > 如果您想要提供現有資源群組的名稱用以儲存共用查詢，您可以清除 [發佈至 resource-graph-queries 資源群組] 核取方塊。 將具有預設名稱的資源群組用於查詢，可讓共用查詢更易於探索。 此外也可讓該資源群組的用途更為明顯。 不過，基於安全考量，您可以選擇根據現有的權限來選取現有的資源群組。

1. 從 Azure 入口網站中的 Azure Resource Graph Explorer 移出，然後再返回該處。 請注意，已儲存的查詢已不再顯示，而會回復為 [查詢 1] 索引標籤。

1. 選取 [開啟查詢]。 請確認 [類型] 設定為 [共用查詢]，且 [訂用帳戶] 和 [資源群組] 的組合與您儲存查詢之處相符。 此時，已儲存的「依作業系統計算 VM 計數」項目會出現在 [查詢名稱] 清單中。 選取已儲存查詢的標題連結，可將該查詢載入至具有該查詢名稱的新索引標籤中。 該查詢為共用查詢，在索引標籤中的標題旁會顯示一個圖示，指出它是共用的。

   :::image type="content" source="../media/create-share-query/show-saved-shared-query.png" alt-text="使用儲存按鈕來儲存新的查詢" border="false":::

   > [!NOTE] 
   > 當已儲存的查詢開啟，且索引標籤顯示其名稱時，[儲存] 按鈕將會以已進行的任何變更來更新該查詢。 若要建立新儲存的查詢，請選取 [另存新檔] 並繼續操作，如同儲存全新的查詢一般。

## <a name="discover-shared-queries"></a>探索共用查詢

共用查詢是 Resource Manager 資源，因此有數種方式可尋找此類查詢：

- 從 Resource Graph 總管。 選取 [開啟查詢]，並將類型設定為 [共用查詢]。
- 從 Resource Graph 查詢入口網站頁面。
- 從共用查詢儲存所在的資源群組。
- 透過對 Resource Graph 的查詢。

### <a name="view-resource-graph-queries"></a>檢視 Resource Graph 查詢

在 Azure 入口網站中，Resource Graph 查詢頁面會顯示已登入的帳戶可存取的共用查詢。 此頁面可讓您依名稱、訂用帳戶、資源群組和 Resource Graph 查詢的其他屬性進行篩選。 您也可以使用此介面來標記、匯出和刪除 Resource Graph 查詢。

選取其中一個查詢，即會開啟 Resource Graph 查詢頁面。 就像其他 Resource Manager 資源一樣，此頁面也提供互動式概觀，以及活動記錄、存取控制和標籤。 您也可以直接從這個頁面套用資源鎖定。

從入口網站功能表中選取 [所有服務]，或使用所有頁面頂端的 Azure 搜尋方塊，即可進入 Resource Graph 查詢頁面。 搜尋並選取 [Resource Graph Explorer]。

### <a name="list-resource-groups-resources"></a>列出資源群組資源

Resource Graph 查詢會與屬於資源群組的其他資源一起列出。
選取 Resource Graph 查詢，即會開啟該查詢的頁面。 省略符號和快顯功能表選項 (按一下滑鼠右鍵即觸發) 的作用與 Resource Graph 查詢頁面上的相同。

### <a name="query-resource-graph"></a>查詢 Resource Graph

您可以透過查詢 Resource Graph 來尋找 Resource Graph 查詢。 下列 Resource Graph 查詢會受到類型 `Microsoft.ResourceGraph/queries` 的限制，然後使用 `project` 而僅列出名稱、修改時間和查詢本身：

```kusto
Resources
| where type == "microsoft.resourcegraph/queries"
| project name, properties.timeModified, properties.query
```

## <a name="run-a-shared-query"></a>執行共用查詢

您可以使用 `{{shared-query-uri}}` 語法 (預覽) 來執行 Resource Graph 的共用查詢。 如需詳細資訊，請參閱[共用查詢語法](../concepts/query-language.md#shared-query-syntax)。

## <a name="delete-a-shared-query"></a>刪除共用查詢

不再需要共用查詢時，請將其刪除。 刪除共用查詢時，會移除對應的 Resource Manager 資源。 此時，任何釘選了結果圖表的儀表板都會顯示錯誤訊息。 該錯誤訊息顯示時，請使用 [從儀表板移除] 按鈕清除您的儀表板。

您可以透過下列介面來刪除共用查詢：
- Resource Graph 查詢頁面
- Resource Graph 查詢頁面
- Resource Graph Explorer 中的 [開啟查詢] 頁面
- 資源群組頁面

## <a name="clean-up-resources"></a>清除資源

完成本教學課程後，如果不再需要您所建立的私人和共用查詢，請加以刪除。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已建立私人和共用查詢。 若要深入了解 Resource Graph 語言，請繼續前往查詢語言詳細資料頁面。

> [!div class="nextstepaction"]
> [取得有關查詢語言的詳細資訊](../concepts/query-language.md)