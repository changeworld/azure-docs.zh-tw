---
title: 教學課程：開始使用 Log Analytics 查詢
description: 從本教學課程中了解如何在 Azure 入口網站中使用 Log Analytics 來撰寫和管理 Azure 監視器記錄查詢。
ms.subservice: logs
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 03/17/2020
ms.openlocfilehash: bfed93a4ed13878448d21b95d265e49bf0260742
ms.sourcegitcommit: a989fb89cc5172ddd825556e45359bac15893ab7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/01/2020
ms.locfileid: "85798250"
---
# <a name="tutorial-get-started-with-log-analytics-queries"></a>教學課程：開始使用 Log Analytics 查詢

本教學課程說明如何在 Azure 入口網站中使用 Log Analytics 來撰寫、執行和管理 Azure 監視器記錄查詢。 您可以使用 Log Analytics 查詢來搜尋字詞、識別趨勢、分析模式，以及從您的資料提供許多其他見解。 

在本教學課程中，您將了解如何使用 Log Analytics 執行下列動作：

> [!div class="checklist"]
> * 了解記錄資料結構描述
> * 撰寫和執行簡單的查詢，以及修改查詢的時間範圍
> * 對查詢結果進行篩選、排序和分組
> * 檢視、修改和共用查詢結果的視覺效果
> * 儲存、載入、匯出及複製查詢和結果

如需記錄查詢的詳細資訊，請參閱 [Azure 監視器中的記錄查詢概觀](log-query-overview.md)。<br/>
如需撰寫記錄查詢的詳細教學課程，請參閱[開始使用 Azure 監視器中的記錄查詢](get-started-queries.md)。

## <a name="open-log-analytics"></a>開啟 Log Analytics
若要使用 Log Analytics，您必須登入 Azure 帳戶。 如果您沒有 Azure 帳戶，請[建立一個免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

若要完成本教學課程中大部分的步驟，您可以使用[此示範環境](https://portal.loganalytics.io/demo)，其中包含許多範例資料。 在示範環境中，您將無法儲存查詢，或將結果釘選到儀表板。

如果您要使用 Azure 監視器收集至少一項 Azure 資源的記錄資料，您也可以使用自己的環境。 若要開啟 Log Analytics 工作區，請在 Azure 監視器的左側導覽中選取 [記錄]  。 

## <a name="understand-the-schema"></a>了解結構描述
 
*結構描述*是分組於邏輯類別下的資料表集合。 示範結構描述有數個來自監視解決方案的類別。 例如，LogManagement  類別包含 Windows 和 Syslog 事件、效能資料與代理程式活動訊號。

結構描述資料表會出現在 Log Analytics 工作區的 [資料表]  索引標籤上。 這些資料表包含資料行，並且分別以其資料行名稱旁的圖示顯示資料類型。 例如，**事件**資料表包含文字資料行 (例如 **Computer**) 和數值資料行 (例如 **EventCategory**)。

![結構描述](media/get-started-portal/schema.png)

## <a name="write-and-run-basic-queries"></a>撰寫和執行基本查詢

Log Analytics 會在**查詢編輯器**中以新的空白查詢開啟。

![Log Analytics](media/get-started-portal/homepage.png)

### <a name="write-a-query"></a>撰寫查詢

Azure 監視器記錄查詢會使用 Kusto 查詢語言的版本。 查詢可以透過資料表名稱或 [search](/azure/kusto/query/searchoperator) 命令開始進行。 

下列查詢會從**事件**資料表中擷取所有記錄：

```Kusto
Event
```

縱線 (|) 字元會分隔命令，因此第一個命令的輸出就是下一個命令的輸入。 您可以在單一查詢中新增任意數目的命令。 下列查詢會從**事件**資料表中擷取記錄，然後在其中搜尋任何屬性中的字詞**錯誤**：

```Kusto
Event 
| search "error"
```

單一分行符號可讓查詢更容易閱讀。 多個分行符號會將查詢分割成不同的查詢。

另一種撰寫相同查詢的方式為：

```Kusto
search in (Event) "error"
```

在第二個範例中，**search** 命令只會搜尋**事件**資料表中的記錄，以取得字詞**錯誤**。

根據預設，Log Analytics 會將查詢限制為過去 24 小時的時間範圍。 若要設定不同的時間範圍，您可以將明確的 **TimeGenerated** 篩選條件新增至查詢，或使用**時間範圍**控制項。

### <a name="use-the-time-range-control"></a>使用時間範圍控制項
若要使用**時間範圍**控制項，請在頂端列中加以選取，然後從下拉式清單中選取一個值，或選取 [自訂]  以建立自訂時間範圍。

![時間選擇器](media/get-started-portal/time-picker.png)

- 時間範圍值採用 UTC 格式，這可能與您當地的時區不同。
- 如果查詢明確設定了 **TimeGenerated** 的篩選條件，則時間選擇器控制項會顯示「在查詢中設定」  ，且會停用以避免發生衝突。

### <a name="run-a-query"></a>執行查詢
若要執行查詢，請將游標放在查詢內的某處，並選取位於頂端列的 [執行]  ，或按 **Shift**+**Enter**。 查詢會持續執行，直到找到空白行為止。

## <a name="filter-results"></a>篩選結果
Log Analytics 會將結果限制為最多 10,000 筆記錄。 `Event` 之類的一般查詢會傳回太多的結果而難以使用。 您可以藉由限制查詢中的資料表元素，或明確地將篩選條件新增至結果，來篩選查詢結果。 透過資料表元素進行篩選會傳回新的結果集，而明確的篩選條件則會套用至現有的結果集。

### <a name="filter-by-restricting-table-elements"></a>藉由限制資料表元素進行篩選
若要藉由限制查詢中的資料表元素將 `Event` 查詢結果篩選為**錯誤**事件：

1. 在查詢結果中，選取在 [EventLevelName]  資料行中具有**錯誤**的任何記錄旁的下拉式箭號。 
   
1. 在展開的詳細資料中，將滑鼠停留在上方，並選取 [EventLevelName]  旁的 [...]  ，然後選取 [包含「錯誤」]  。 
   
   ![將篩選新增至查詢](media/get-started-portal/add-filter.png)
   
1. 請注意，**查詢編輯器**中的查詢現在已變更為：
   
   ```Kusto
   Event
   | where EventLevelName == "Error"
   ```
   
1. 選取 [執行]  以執行新的查詢。

### <a name="filter-by-explicitly-filtering-results"></a>藉由明確篩選結果來進行篩選
若要藉由篩選查詢結果將 `Event` 查詢結果篩選為**錯誤**事件：

1. 在查詢結果中，選取資料行標題 **EventLevelName** 旁邊的 [篩選]  圖示。 
   
1. 在快顯視窗的第一個欄位中選取 [等於]  ，然後在下一個欄位中輸入*錯誤*。 
   
1. 選取 [篩選]  。
   
   ![Filter](media/get-started-portal/filter.png)

## <a name="sort-group-and-select-columns"></a>排序、分群和選取資料行
若要依特定資料行排序查詢結果 (例如 **TimeGenerated [UTC]** )，請選取資料行標題。 再次選取標題，可在遞增和遞減順序之間切換。

![排序資料行](media/get-started-portal/sort-column.png)

群組是另一種組織結果的方式。 若要依特定資料行將結果分組，請將資料行標頭拖曳至標示為「將資料行標頭拖放到此處可依據該資料行分組」  的結果資料表上方。 若要建立子群組，請將其他資料行拖曳到上方列。 您可以在該列中重新排列群組與子群組的階層和排序。

![群組](media/get-started-portal/groups.png)

若要隱藏或顯示結果中的資料行，請選取資料表上方的 [資料行]  ，然後從下拉式清單中選取或取消選取您要的資料行。

![Select columns](media/get-started-portal/select-columns.png)

## <a name="view-and-modify-charts"></a>檢視和修改圖表
您也可以用視覺化格式查看查詢結果。 請輸入下列查詢作為範例：

```Kusto
Event 
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1d) 
| summarize count() by Source 
```

根據預設，結果會顯示在資料表中。 選取資料表上方的 [圖表]  ，以在圖形檢視中查看結果。

![長條圖](media/get-started-portal/bar-chart.png)

結果會顯示在堆疊長條圖中。 選取其他選項 (例如 [堆疊資料行]  或 [圓形圖]  )，以顯示結果的其他檢視。

![圓形圖](media/get-started-portal/pie-chart.png)

您可以從控制列手動變更檢視的屬性，例如 x 和 y 軸，或分組及分割喜好設定。

您也可以使用 [render](/azure/kusto/query/renderoperator) 運算子對查詢本身設定慣用檢視。

## <a name="pin-results-to-a-dashboard"></a>將結果釘選到儀表板

若要將結果資料表或圖表從 Log Analytics 釘選到共用的 Azure 儀表板，請選取頂端列上的 [釘選到儀表板]  。 

![釘選到儀表板](media/get-started-portal/pin-dashboard.png)

在 [釘選到另一個儀表板]  窗格中，選取或建立要釘選到的共用儀表板，然後選取 [套用]  。 資料表或圖表會出現在選取的 Azure 儀表板上。

![已釘選到儀表板的圖表](media/get-started-portal/pin-dashboard2.png)

您釘選到共用儀表板的資料表或圖表有下列簡化： 

- 資料限制為過去 14 天。
- 資料表最多只會顯示四個資料行和前七個資料列。
- 具有許多不同類別的圖表，會自動將較少填入的類別分組到單一的**其他**資料箱中。

## <a name="save-load-or-export-queries"></a>儲存、載入或匯出查詢

建立查詢之後，您可以儲存查詢或結果，或將其與其他人共用。 

### <a name="save-queries"></a>儲存查詢

若要儲存查詢：

1. 選取頂端列上的 [儲存]  。
   
1. 在 [儲存]  對話方塊中，使用字元 a-z、A-Z、0-9、空格、連字號、底線、句號、括弧或縱線字元，為查詢提供**名稱**。 
   
1. 選取要將查詢儲存為**查詢**還是**函式**。 函式是可供其他查詢參考的查詢。 
   
   若要將查詢儲存為函式，請提供**函式別名**，這是讓其他查詢用來呼叫此查詢的簡短名稱。
   
1. 提供**查詢總管**的**類別**，以用於查詢。
   
1. 選取 [儲存]  。
   
   ![儲存函式](media/get-started-portal/save-function.png)

### <a name="load-queries"></a>載入查詢
若要載入已儲存的查詢，請選取右上方的 [查詢總管]  。 [查詢總管]  窗格隨即開啟，並依類別列出所有查詢。 展開類別或在搜尋列中輸入查詢名稱，然後選取查詢，以將其載入至 [查詢編輯器]  。 您可以選取查詢名稱旁邊的星號，以將查詢標示為**我的最愛**。

![查詢總管](media/get-started-portal/query-explorer.png)

### <a name="export-and-share-queries"></a>匯出及共用查詢
若要匯出查詢，請選取頂端列上的 [匯出]  ，然後從下拉式清單中選取 [匯出至 CSV - 所有資料行]  、[匯出至 CSV - 顯示的資料行]  或 [匯出至 Power BI (M 查詢)]  。

若要共用查詢的連結，請選取頂端列上的 [複製連結]  ，然後選取 [複製連結以查詢]  、[複製查詢文字]  或 [複製查詢結果]  ，以複製到剪貼簿。 您可以將查詢連結傳送給有權存取相同工作區的其他人。

## <a name="next-steps"></a>後續步驟

請前往下一個教學課程，以深入了解如何撰寫 Azure 監視器記錄查詢。
> [!div class="nextstepaction"]
> [撰寫 Azure 監視器記錄查詢](get-started-queries.md)
