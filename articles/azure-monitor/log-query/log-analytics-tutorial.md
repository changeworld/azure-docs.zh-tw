---
title: Log Analytics 教學課程
description: 從本教學課程中了解如何在 Azure 監視器中使用 Log Analytics 的功能來建立執行記錄查詢，並在 Azure 入口網站中分析結果。
ms.subservice: logs
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 10/07/2020
ms.openlocfilehash: 16292999bf8c01615a9125ffaa9f93fc4b2a8ec2
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95756208"
---
# <a name="log-analytics-tutorial"></a>Log Analytics 教學課程
Log Analytics 是 Azure 入口網站中的一項工具，可從 Azure 監視器記錄所收集的資料中編輯和執行記錄查詢，並以互動方式分析結果。 您可以使用 Log Analytics 查詢取出符合特定準則的記錄、識別趨勢、分析模式，以及提供資料的各種深入解析。 

本教學課程將逐步引導您了解 Log Analytics 介面並開始使用一些基本查詢，也會說明如何解讀結果。 您將了解下列內容：

> [!div class="checklist"]
> * 了解記錄資料結構描述
> * 撰寫和執行簡單的查詢，以及修改查詢的時間範圍
> * 對查詢結果進行篩選、排序和分組
> * 檢視、修改和共用查詢結果的視覺效果
> * 載入、匯出及複製查詢和結果

> [!IMPORTANT]
> 本教學課程使用 Log Analytics 的功能來建立和執行查詢，而不是使用查詢本身的功能。 您將利用 Log Analytics 功能建立一個查詢，並使用另一個範例查詢。 在準備好了解查詢的語法且開始直接編輯查詢本身時，請瀏覽 [Kusto 查詢語言教學課程](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor)。 該教學課程會逐步解說幾個可以在 Log Analytics 中編輯並執行的查詢範例，並運用您將在本教學課程中學習到的幾項功能。


## <a name="prerequisites"></a>必要條件
本教學課程使用 [Log Analytics 示範環境](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade)，其中包含許多支援範例查詢的範例資料。 您也可以使用自己的 Azure 訂用帳戶，但相同的資料表中可能不會有資料。

## <a name="open-log-analytics"></a>開啟 Log Analytics
開啟 [Log Analytics 示範環境](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade)或從訂用帳戶的 Azure 監視器功能表中選取 [記錄] 。 這會將初始範圍設定為 Log Analytics 工作區，表示系統會從該工作區中的所有資料中選取您的查詢。 如果您從 Azure 資源的功能表中選取 [記錄]，則範圍會設定為僅限來自該資源的記錄。 如需範圍的詳細資訊，請參閱[記錄查詢範圍](scope.md)。

您可以在畫面的左上角檢視範圍。 如果您使用自己的環境，則會看到可選取不同範圍的選項，但在示範環境中無法使用此選項。

[![查詢範圍](media/log-analytics-tutorial/scope.png)](media/log-analytics-tutorial/scope.png#lightbox)

## <a name="table-schema"></a>資料表結構描述
畫面的左側包含 **資料表** 索引標籤，可讓您檢查目前範圍中可用的資料表。 根據預設，這些項目會依 **解決方案** 分組，但您會變更其群組或加以篩選。 

展開 **記錄管理** 解決方案，並找出 **AzureActivity** 資料表。 您可以展開資料表以檢視結構描述，或將滑鼠停留在名稱上方，以顯示其他的相關資訊。 

[![資料表檢視](media/log-analytics-tutorial/table-details.png)](media/log-analytics-tutorial/table-details.png#lightbox)

按一下 [深入了解] 移至記錄每個資料表和其資料行的資料表參考。 按一下 [預覽資料]，即可快速查看資料表中的幾筆最近記錄。 此舉有助於確保資料是在實際執行查詢之前所預期的。

[![範例資料](media/log-analytics-tutorial/sample-data.png)](media/log-analytics-tutorial/sample-data.png#lightbox)

## <a name="write-a-query"></a>撰寫查詢
讓我們繼續使用 **AzureActivity** 資料表來撰寫查詢。 按兩下名稱，將其加入查詢視窗。 您也可以直接在視窗中輸入，甚至取得可協助完成目前範圍中的資料表名稱和 KQL 命令的 Intellisense。

這是我們可以撰寫的最簡單查詢， 只會傳回資料表中的所有記錄。 按一下 [執行] 按鈕，或按 Shift + Enter，將游標放在查詢文字中的任何位置，以執行此程式。

[![查詢結果](media/log-analytics-tutorial/query-results.png)](media/log-analytics-tutorial/query-results.png#lightbox)

您可以看到系統跑出了一些結果。 查詢所傳回的記錄數目會顯示在右下角。 

## <a name="filter"></a>Filter

讓我們在查詢中新增篩選，以減少傳回的記錄數目。 在左側窗格中選取 **篩選** 索引標籤。 這會在查詢結果中顯示不同的資料行，讓您用來篩選結果。 這些資料行中的前幾個值會顯示具有該值的記錄數目。 按一下 **類別值** 底下的 [管理]，然後按一下 [套用與執行]。 

[![[查詢] 窗格](media/log-analytics-tutorial/query-pane.png)](media/log-analytics-tutorial/query-pane.png#lightbox)

**where** 陳述式會以您選取的值加入至查詢。 結果現在只會包含具有該值的記錄，因此您可以看到記錄計數減少。

[![已篩選的查詢結果](media/log-analytics-tutorial/query-results-filter-01.png)](media/log-analytics-tutorial/query-results-filter-01.png#lightbox)


## <a name="time-range"></a>時間範圍
Log Analytics 工作區中的所有資料表都有一個稱為 **TimeGenerated** 的資料行，這是記錄的建立時間。 所有查詢都有時間範圍，會將結果限制為在該範圍內具有 **TimeGenerated** 值的記錄。 時間範圍可以在查詢中設定，或使用畫面頂端的選取器來設定。

根據預設，查詢會傳回過去 24 小時內的記錄。 選取 **時間範圍** 下拉式清單，並變更為 **7 天**。 按一下 [再次執行] 以傳回結果。 您可以看到系統傳回了一些結果，但出現了一則訊息，指出我們並未看到所有結果。 這是因為 Log Analytics 最多只能傳回 10000 筆記錄，而我們的查詢會傳回比這更多的記錄。 

[![時間範圍](media/log-analytics-tutorial/query-results-max.png)](media/log-analytics-tutorial/query-results-max.png#lightbox)


## <a name="multiple-query-conditions"></a>多個查詢條件
讓我們藉由新增另一個篩選準則，進一步縮減結果。 查詢可以包含任意數目的篩選準則，完全以您理想的記錄集為目標。 在 **ActivityStatusValue** 下選取 [成功]，然後按一下 [套用與執行]。 

[![多個篩選查詢結果](media/log-analytics-tutorial/query-results-filter-02.png)](media/log-analytics-tutorial/query-results-filter-02.png#lightbox)


## <a name="analyze-results"></a>分析結果
除了協助您撰寫和執行查詢以外，Log Analytics 還提供解讀結果的功能。 首先，展開記錄以檢視所有資料行的值。

[![展開記錄](media/log-analytics-tutorial/expand-record.png)](media/log-analytics-tutorial/expand-record.png#lightbox)

按一下任何資料行的名稱，依據該資料行來排序結果。 按一下旁邊的篩選圖示，以提供篩選條件。 這類似於將篩選準則加入查詢本身，但如果再次執行查詢，系統會清除此篩選。 如果要快速分析一組記錄做=作為互動式分析的一部分，請使用這個方法。

例如，在 **CallerIpAddress** 資料行上設定篩選，以將記錄限制為單一呼叫者。 

[![篩選查詢結果](media/log-analytics-tutorial/query-results-filter.png)](media/log-analytics-tutorial/query-results-filter.png#lightbox)

您可以根據特定資料行將記錄分組，而不用篩選結果。 清除您剛建立的篩選，然後開啟 **群組資料行** 滑桿。 

[![群組資料行](media/log-analytics-tutorial/query-results-group-columns.png)](media/log-analytics-tutorial/query-results-group-columns.png#lightbox)

現在將 **CallerIpAddress** 資料行拖曳至群組資料列。 結果現在會依據該資料行組織，您可以摺疊每個群組，以協助您分析。

[![分組的查詢結果](media/log-analytics-tutorial/query-results-grouped.png)](media/log-analytics-tutorial/query-results-grouped.png#lightbox)

## <a name="work-with-charts"></a>使用圖表
讓我們看一下使用可在圖表中檢視之數值資料的查詢。 我們不會建立查詢，而是選取範例查詢。

在左側窗格中，按一下 [查詢]。 此窗格包含您可以新增至查詢視窗的範例查詢。 如果您使用自己的工作區，應該在多個類別中有各種查詢；但如果使用的是示範環境，則可能只會看到單一 **Log Analytics 工作區** 類別目錄。 展開以檢視類別中的查詢。

按一下名為 **ResponseCode 的查詢計數** 的查詢。 此舉會在查詢視窗新增下列查詢。 請注意，新的查詢會以空白行分隔。 KQL 中的查詢會在遇到空白行時結束，因此系統會將其視為個別查詢。 

[![新增查詢](media/log-analytics-tutorial/example-query.png)](media/log-analytics-tutorial/example-query.png#lightbox)

游標所在位置表示目前的查詢。 您可以看到第一個查詢已反白顯示，表示這是目前的查詢。 按一下新查詢中的任何位置以選取，然後按一下 [執行] 按鈕執行。

[![查詢結果圖表](media/log-analytics-tutorial/example-query-output-chart.png)](media/log-analytics-tutorial/example-query-output-chart.png#lightbox)

請注意，此輸出是圖表而不是資料表 (例如最後一個查詢)。 這是因為範例查詢會在結尾使用 [render](/azure/data-explorer/kusto/query/renderoperator?pivots=azuremonitor) 命令。 請注意有各種選項可用來處理圖表，例如變更為另一種類型。

請嘗試選取 **結果**，將查詢的輸出當作資料表查看。 

[![查詢結果資料表](media/log-analytics-tutorial/example-query-output-table.png)](media/log-analytics-tutorial/example-query-output-table.png#lightbox)



## <a name="next-steps"></a>後續步驟

現在您已了解如何使用 Log Analytics，以完成使用記錄查詢的教學課程。
> [!div class="nextstepaction"]
> [撰寫 Azure 監視器記錄查詢](get-started-queries.md)
