---
title: Azure 監視器中的 Log Analytics 總覽
description: 描述 Log Analytics，這是 Azure 入口網站中用來編輯和執行記錄查詢以分析 Azure 監視器記錄中資料的工具。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/04/2020
ms.openlocfilehash: ba27739ff9e9d992ffe6202629a1db19604b1409
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186111"
---
# <a name="overview-of-log-analytics-in-azure-monitor"></a>Azure 監視器中的 Log Analytics 總覽
Log Analytics 是 Azure 入口網站中的一項工具，可用來編輯和執行記錄查詢 Azure 監視器記錄檔中的資料。 您可以撰寫會傳回一組記錄的簡單查詢，然後使用 Log Analytics 的功能來排序、篩選和分析這些記錄。 或者，您可以撰寫更先進的查詢來執行統計分析，並將圖表中的結果視覺化以找出特定趨勢。 無論您是以互動方式處理查詢的結果，或將它們與其他 Azure 監視器功能搭配使用，例如記錄查詢警示或活頁簿，Log Analytics 都是您要用來撰寫和測試的工具。 


> [!TIP]
> 本文提供 Log Analytics 及其每項功能的說明。 如果您想要直接跳到教學課程，請參閱 [Log Analytics 教學](./log-analytics-tutorial.md)課程。



## <a name="starting-log-analytics"></a>正在啟動 Log Analytics
從 Azure 入口網站的 [ **Azure 監視器**] 功能表中的 **記錄** 啟動 Log Analytics。 您也會在大部分 Azure 資源的功能表中看到此選項。 無論您從何處開始，它將會是相同的 Log Analytics 工具。 您用來啟動 Log Analytics 的功能表會決定可使用的資料。 如果您從 [ **Azure 監視器** ] 功能表或 [ **Log Analytics 工作區** ] 功能表啟動它，就可以存取工作區中的所有記錄。 如果您選取另一種資源類型的 **記錄** ，則您的資料將會限制為該資源的記錄資料。 如需詳細資訊，請參閱 [Azure 監視器 Log Analytics 中的記錄查詢範圍和時間範圍](scope.md)。

[![啟動 Log Analytics](media/log-analytics-overview/start-log-analytics.png)](media/log-analytics-overview/start-log-analytics.png#lightbox)

當您啟動 Log Analytics 時，您會看到的第一件事是具有 [範例查詢](example-queries.md)的對話方塊。 這些都是依解決方案分類，而且您可以流覽或搜尋符合您特定需求的查詢。 您可能會發現，它會完全符合您的需求，或將一個載入編輯器，然後視需要加以修改。 流覽範例查詢其實是學習如何撰寫您自己的查詢的絕佳方法。 當然，如果您想要從空白腳本開始，然後自行撰寫，您可以關閉範例查詢。 如果您想要取回它們，只要按一下畫面頂端的 **查詢** 即可。

## <a name="log-analytics-interface"></a>Log Analytics 介面
下圖識別 Log Analytics 的不同元件。

[![Log Analytics](media/log-analytics-overview/log-analytics.png)](media/log-analytics-overview/log-analytics.png#lightbox)

### <a name="1-top-action-bar"></a>1. 頂端動作列
在查詢視窗中使用查詢的控制項。

| 選項 | 描述 |
|:---|:---|
| 影響範圍 | 指定用於查詢的資料範圍。 這可能是 Log Analytics 工作區中的所有資料，或跨多個工作區之特定資源的資料。 請參閱 [查詢範圍](scope.md)。 |
| 執行按鈕 | 按一下以在查詢視窗中執行選取的查詢。 您也可以按 shift + enter 來執行查詢。 |
| 時間選擇器 | 選取可供查詢使用之資料的時間範圍。 如果您在查詢中包含時間篩選準則，則會覆寫此項。 請參閱 [Azure 監視器 Log Analytics 中的記錄查詢範圍和時間範圍](scope.md)。 |
| [儲存] 按鈕 | 將查詢儲存至工作區的查詢瀏覽器。 |
 [複製] 按鈕 | 將查詢、查詢文字或查詢結果的連結複製到剪貼簿。 |
| [新增警示規則] 按鈕 | 使用空的查詢建立新的索引標籤。 |
| 匯出按鈕 | 將查詢結果匯出至 CSV 檔案或查詢，以 Power Query 用於 Power Bi 的公式語言格式。 |
| 釘選到儀表板按鈕 | 將查詢的結果新增至 Azure 儀表板。 |
| 格式化查詢按鈕 | 排列選取的文字以提供可讀性。 |
| 範例查詢按鈕 | 開啟您第一次開啟 Log Analytics 時所顯示的 [範例查詢] 對話方塊。 |
| 查詢瀏覽器按鈕 | 開啟 **查詢 Explorer** ，以存取工作區中已儲存的查詢。 |


### <a name="2-sidebar"></a>2. 提要欄位
工作區中的資料表清單、查詢範例，以及目前查詢的篩選選項。

| 索引標籤 | 描述 |
|:---|:---|
| 資料表 | 列出屬於所選範圍的資料表。 選取 [ **群組依據** ] 以變更資料表的群組。 將滑鼠停留在資料表名稱上方以顯示對話方塊，其中包含資料表的描述，以及用來查看其檔和預覽其資料的選項。 展開資料表以查看其資料行。 按兩下資料表或資料行名稱，將它加入至查詢。 |
| 查詢 | 您可以在 [查詢] 視窗中開啟的範例查詢清單。 這是您開啟 Log Analytics 時所顯示的相同清單。 選取 [ **群組依據** ] 來變更查詢的群組。 按兩下查詢將其新增至查詢視窗，或將滑鼠停留在其他選項上。 |
| 篩選 | 根據查詢的結果建立篩選選項。 執行查詢之後，資料行會顯示結果中具有不同值的資料行。 選取一或多個值，然後按一下 [套用 **& 執行** ]，將 **where** 命令新增至查詢，然後再次執行。 |

### <a name="3-query-window"></a>3. 查詢視窗
您可以在查詢視窗中編輯查詢。 這包括適用于 KQL 命令的 intellisense，以及用來增強可讀性的色彩編碼。 按一下 **+** 視窗頂端的，開啟另一個索引標籤。

單一視窗可以包含多個查詢。 查詢不能包含任何空白行，因此您可以在視窗中以一或多個空白行來分隔多個查詢。 目前的查詢是資料指標位於其中任何位置的查詢。

若要執行目前的查詢，請按一下 [ **執行** ] 按鈕或按下 Shift + Enter。

### <a name="4-results-window"></a>4. 結果視窗
查詢的結果會顯示在 [結果] 視窗中。 根據預設，結果會顯示為數據表。 若要顯示為圖表，請在 [結果] 視窗中選取 [**圖表**]，或在查詢中加入 **轉譯命令。**

#### <a name="results-view"></a>Results view (結果檢視)
以資料行和資料列的方式，在資料表中顯示查詢結果。 按一下資料列的左邊以展開其值。 按一下 [資料 **行** ] 下拉式清單，即可變更資料行的清單。 按一下資料行名稱來排序結果。 按一下資料行名稱旁邊的漏斗圖，以篩選結果。 清除篩選，然後再次執行查詢來重設排序。

選取 [ **群組資料行** ]，即可在查詢結果上方顯示群組列。 將結果拖曳至橫條，以依任何資料行將結果分組。 藉由新增額外的資料行，在結果中建立嵌套群組。 

#### <a name="chart-view"></a>圖表檢視
將結果顯示為多個可用圖表類型的其中一個。 您可以在查詢中指定 **轉譯命令的** 圖表類型，或從 [ **視覺效果類型** ] 下拉式清單中選取圖表類型。

| 選項 | 描述 |
|:---|:---|
| **視覺效果類型** | 要顯示的圖表類型。 |
| **X 軸** | 要用於 X 軸之結果中的資料行 
| **Y 軸** | 要用於 Y 軸之結果中的資料行。 這通常會是數值資料行。 |
| **分割依據：** | 結果中的資料行，定義圖表中的數列。 會為數據行中的每個值建立一個數列。 |
| **彙總** | 要對 Y 軸中的數值執行的匯總類型。 |

## <a name="relationship-to-azure-data-explorer"></a>與 Azure 資料總管的關聯性
如果您已經熟悉 Azure 資料總管 Web UI，則 Log Analytics 應該看起來很熟悉。 這是因為它建置於 Azure 資料總管之上，並使用相同的 Kusto 查詢語言 (KQL) 。 Log Analytics 會新增 Azure 監視器的特定功能，例如依時間範圍篩選，以及從查詢建立警示規則的能力。 這兩種工具都包含一個可讓您掃描可用資料表結構的瀏覽器，但 Azure 資料總管 Web UI 主要適用于 Azure 資料總管資料庫中的資料表，而 Log Analytics 則適用于 Log Analytics 工作區中的資料表。 

## <a name="next-steps"></a>後續步驟
- 逐步解說在 [Azure 入口網站中使用 Log Analytics 的教學](./log-analytics-tutorial.md)課程。
- 逐步解說 [撰寫查詢的教學](get-started-queries.md)課程。