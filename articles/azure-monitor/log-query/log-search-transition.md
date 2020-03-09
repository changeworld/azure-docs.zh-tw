---
title: 在 Azure Log Analytics 中檢視及分析資料 | Microsoft Docs
description: Log Analytics 記錄搜尋至 Azure 監視器記錄查詢體驗的使用者協助。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/16/2019
ms.openlocfilehash: 6cc7fc94162ed101d06fbbbc4722867be46987b6
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77670129"
---
# <a name="transition-from-log-analytics-log-search-to-azure-monitor-logs"></a>從 Log Analytics 記錄搜尋轉換至 Azure 監視器記錄
最近以新的體驗取代了 Log Analytics 中的記錄搜尋，以便分析 Azure 監視器記錄。 [記錄搜尋] 頁面目前仍可在 Azure 入口網站中透過 **Log Analytics 工作區** 中的 [記錄 (傳統)] 功能表項目存取，但是該頁面將於 2019 年 2 月 15 日移除。 本文說明兩種體驗之間的差異以協助您從記錄搜尋轉換。 

## <a name="filter-results-of-a-query"></a>篩選查詢結果
在記錄搜尋中，篩選條件清單會在傳遞搜尋結果時顯示。 選取篩選條件，然後按一下 [套用] 以使用所選篩選條件執行查詢。

![記錄搜尋篩選條件](media/log-search-transition/filter-log-search.png)

在 Azure 監視器記錄中，選取 [篩選條件 (預覽)] 以顯示篩選條件。 按一下篩選圖示以顯示其他篩選條件。 選取篩選條件，然後按一下 [套用並執行] 以使用所選篩選條件執行查詢。

![記錄篩選器](media/log-search-transition/filter-logs.png)

## <a name="extract-custom-fields"></a>擷取自訂欄位 
在記錄搜尋中，您可從 [清單] 檢視中擷取[自訂欄位](../platform/custom-fields.md)，其中欄位的功能表包含「從資料表擷取欄位」動作。

![記錄搜尋擷取欄位](media/log-search-transition/extract-fields-log-search.png)

在 Azure 監視器記錄中，從資料表檢視擷取自訂欄位。 按一下左邊的箭號來展開記錄，然後按一下省略符號，即可存取「擷取欄位」動作。

![記錄擷取欄位](media/log-search-transition/extract-fields-logs.png)

## <a name="functions-and-computer-groups"></a>函式和電腦群組
若要將搜尋儲存在記錄搜尋中，請選取 [已儲存的搜尋] 和 [新增]，以提供名稱、類別和查詢文字。 新增函式別名，以建立[電腦群組](../platform/computer-groups.md)。

![儲存記錄搜尋](media/log-search-transition/save-search-log-search.png)

若要將目前的查詢儲存在 Azure 監視器記錄中，請選取 [儲存]。 將 [另存新檔] 變更為 [函式]，並提供 [函式別名] 以建立[函式](functions.md)。 選取 [將此查詢儲存為電腦群組]，以函式別名作為 [電腦群組](../platform/computer-groups.md)。

![儲存記錄查詢](media/log-search-transition/save-query-logs.png)

## <a name="saved-queries"></a>儲存的查詢
在記錄搜尋中，可透過 [已儲存的搜尋] 動作列項目取得已儲存的查詢。 在 Azure 監視器記錄中，從 [查詢總管](../log-query/get-started-portal.md#save-queries) 存取已儲存的查詢。

![查詢總管](media/log-search-transition/query-explorer.png)

## <a name="drill-down-on-summarized-rows"></a>向下鑽研摘要的資料列
在記錄搜尋中，您可以按一下摘要查詢中的資料列，以啟動另一個查詢來列出該資料列中的詳細記錄。

![記錄搜尋向下鑽研](media/log-search-transition/drilldown-search.png)

在 Azure 監視器記錄中，必須修改查詢才能傳回這些記錄。 展開結果中的一個資料列，然後按一下值旁邊的 **+** 以將其新增至查詢。 然後，註解化 **summarize** 命令，並再次執行查詢。

![Azure 監視器記錄向下鑽研](media/log-search-transition/drilldown-logs.png)

## <a name="take-action"></a>採取動作
在記錄搜尋中，您可以選取 [採取動作]，以從搜尋結果[啟動 Runbook](take-action.md)。

![採取動作](media/log-search-transition/take-action-log-search.png)

在 Azure 監視器記錄中，[從記錄查詢建立警示](../platform/alerts-log.md)。 設定動作群組，其中包含為了回應警示而將執行的一或多個動作。

![動作群組](media/log-search-transition/action-group.png)

## <a name="next-steps"></a>後續步驟

- 深入了解新的 [Azure 監視器記錄體驗](get-started-portal.md)。
