---
title: Azure 監視器活頁簿樹狀結構視覺效果
description: 瞭解所有 Azure 監視器活頁簿樹狀結構視覺效果。
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: a815f4dcf7c76d4c11b4f2e227802b15f9c92a54
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89663921"
---
# <a name="tree-visualizations"></a>樹狀結構視覺效果

活頁簿支援透過樹狀結構的階層式流覽。 樹狀結構允許將某些資料列展開至下一個層級，以進行向下切入體驗。

下列範例顯示容器健全狀況度量 (工作集大小) 視覺化為樹狀結構方格。 這裡的最上層節點是 Azure Kubernetes Service (AKS) 節點，下一個層級是 pod，而最後一個層級是容器。 請注意，您仍然可以將資料行格式化，例如在方格中 (熱度圖、圖示、連結) 。 此案例中的基礎資料來源是具有 AKS 記錄的 Log Analytics 工作區。

[![磚摘要視圖的螢幕擷取畫面](./media/workbooks-tree-visualizations/trees.png)](./media/workbooks-tree-visualizations/trees.png#lightbox)

## <a name="adding-a-tree-grid"></a>加入樹狀格線
1. 按一下 [ _編輯_ ] 工具列專案，將活頁簿切換至編輯模式。
2. 選取 [ **加入** ]，然後 *加入 [查詢* ]，將記錄查詢控制項加入至活頁簿。
3. 將查詢類型選取為 [ **記錄**]、[資源類型] (例如 Application Insights) ，以及要作為目標的資源。
4. 使用查詢編輯器來輸入您分析的 KQL
    ```kusto
    requests
    | summarize Requests = count() by ParentId = appName, Id = name
    | extend Kind = 'Request', Name = strcat('🌐 ', Id)
    | union (requests
    | summarize Requests = count() by Id = appName
    | extend Kind = 'Request', ParentId = '', Name = strcat('📱 ', Id))
    | project Name, Kind, Requests, Id, ParentId
    | order by Requests desc
    ```
5. 將視覺效果設定為 **方格**
6. 選取 [資料 **行設定** ] 按鈕以開啟 [設定] 窗格
7. 在底部的 [ **樹狀結構/群組依據設定** ] 區段中，設定：
    * 樹狀結構類型： `Parent/Child`
    * 識別碼欄位： `Id`
    * 父識別碼欄位： `ParentId`
    * 將展開器顯示在： `Name`
    * 選取 *[展開樹狀結構的最上層* ] 核取方塊。
8. 在頂端的 [資料 _行_ ] 區段中，設定：
    * _識別碼_ 資料行轉譯器： `Hidden`
    * _父識別碼_ -資料行轉譯器： `Hidden`
    * _要求_ -資料行轉譯器： `Bar` ，色彩： `Blue` ，最小值： `0`
9. 選取窗格底部的 [ **儲存後關閉** ] 按鈕。

[![具有上述查詢和設定之磚摘要視圖的螢幕擷取畫面。](./media/workbooks-tree-visualizations/tree-settings.png)](./media/workbooks-tree-visualizations/tree-settings.png#lightbox)

## <a name="tree-settings"></a>樹狀結構設定

| 設定 | 說明 |
|:------------- |:-------------|
| `Id Field` | 方格中每個資料列的唯一識別碼。 |
| `Parent Id Field` | 目前資料列的父系識別碼。 |
| `Show the expander on` | 要在其上顯示樹狀結構展開器的資料行。 樹狀結構方格通常會隱藏其識別碼和父識別碼欄位，因為它們並不容易閱讀。 相反地，展開器會出現在具有更容易讀取之值的欄位上，例如實體的名稱。 |
| `Expand the top level of the tree` | 若選取此選項，則會在最上層展開樹狀方格。 如果您想要依預設顯示詳細資訊，則會很有用。 |

## <a name="grouping-in-a-grid"></a>方格中的群組

群組可讓您以更簡單的查詢建立類似于上面的階層式查看。 您不會在樹狀結構的內部節點上遺失匯總，但在某些情況下可接受這種情況。 當基礎結果集無法轉換成適當的自由格式時，請使用 *Group By* 來建立樹狀檢視，例如：警示、健康情況和度量資料。

## <a name="adding-a-tree-using-grouping"></a>使用群組新增樹狀結構

1. 按一下 [ _編輯_ ] 工具列專案，將活頁簿切換至編輯模式。
2. 選取 [ **加入** ]，然後 *加入 [查詢* ]，將記錄查詢控制項加入至活頁簿。
3. 選取 [ **記錄**]、[資源類型] 作為 [查詢類型] (例如 Application Insights) 以及要作為目標的資源。
4. 使用查詢編輯器來輸入您分析的 KQL
    ```kusto
    requests
    | summarize Requests = count() by App = appName, RequestName = name
    | order by Requests desc
    ```
1. 將視覺效果設定為 *方格*。
2. 選取 [資料 **行設定] 按鈕** 以開啟 [設定] 窗格。
3. 在底部的 [ *樹狀結構/群組依據設定* ] 區段中，設定：
    * 樹狀結構類型： `Group By`
    * 群組依據： `App`
    * Then： `None`
    * 選取 *[展開樹狀結構的最上層* ] 核取方塊。
4. 在頂端的 [資料 *行* ] 區段中，設定：
    * *應用程式* 資料行轉譯器： `Hidden`
    * *要求* -資料行轉譯器： `Bar` ，色彩： `Blue` ，最小值： `0`
5. 選取窗格底部的 [ **儲存後關閉** ] 按鈕。

[![顯示在活頁簿中建立樹狀結構視覺效果的螢幕擷取畫面](./media/workbooks-tree-visualizations/tree-group-create.png)](./media/workbooks-tree-visualizations/tree-group-create.png#lightbox)

## <a name="next-steps"></a>接下來的步驟

* 瞭解如何 [在活頁簿中建立圖形](workbooks-graph-visualizations.md)。
* 瞭解如何 [在活頁簿中建立磚](workbooks-tile-visualizations.md)。
