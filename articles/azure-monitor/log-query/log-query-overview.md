---
title: Azure 監視器中的日誌查詢概述 |微軟文檔
description: 回答與日誌查詢相關的常見問題，並説明您開始使用它們。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/19/2019
ms.openlocfilehash: 54a6f875bc33d24d412d2424c634d1019b4af399
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670112"
---
# <a name="overview-of-log-queries-in-azure-monitor"></a>Azure 監視器中的日誌查詢概述
日誌查詢可説明您充分利用[Azure 監視器日誌](../platform/data-platform-logs.md)中收集的資料的值。 強大的查詢語言允許您從多個表聯接資料、聚合大資料集以及以最少的代碼執行複雜的操作。 只要收集了支援資料，並且您瞭解如何構造正確的查詢，幾乎可以回答和分析。

Azure 監視器中的某些功能（如[見解](../insights/insights-overview.md)和[解決方案](../insights/solutions-inventory.md)）處理日誌資料，而不會將您暴露給基礎查詢。 要充分利用 Azure 監視器的其他功能，應瞭解如何構造查詢以及如何使用它們在 Azure 監視器日誌中交互分析資料。

使用本文作為瞭解 Azure 監視器中的日誌查詢的起點。 它回答常見問題，並提供指向提供進一步詳細資訊和課程的其他文檔的連結。

## <a name="how-can-i-learn-how-to-write-queries"></a>如何瞭解如何編寫查詢？
如果要直接跳轉到內容，可以從以下教程開始：

- [開始在 Azure 監視器 中的日誌分析](get-started-portal.md)。
- [開始在 Azure 監視器 中的日誌查詢](get-started-queries.md)。

完成基礎知識後，使用您自己的資料或演示環境中的資料，從以下開始，流覽多個課程： 

- [在 Azure 監視器記錄查詢中使用字串](string-operations.md)
 
## <a name="what-language-do-log-queries-use"></a>日誌查詢使用什麼語言？
Azure 監視器日誌基於[Azure 資料資源管理器](/azure/data-explorer)，日誌查詢使用相同的 Kusto 查詢語言 （KQL） 編寫。 這是一種豐富的語言，旨在易於閱讀和創作，您應該能夠以最少的指導開始使用它。

有關 KQL 的完整文檔和有關可用不同功能的參考，請參閱[Azure 資料資源管理器 KQL 文檔](/azure/kusto/query)。<br>
有關使用 Azure 監視器日誌中的資料快速演練該語言，請參閱[Azure 監視器中的日誌查詢](get-started-queries.md)入門。
有關 Azure 監視器使用的 KQL 版本中的微小差異，請參閱[Azure 監視器日誌查詢語言差異](data-explorer-difference.md)。

## <a name="what-data-is-available-to-log-queries"></a>哪些資料可用於記錄查詢？
Azure 監視器日誌中收集的所有資料都可用於在日誌查詢中檢索和分析。 不同的資料來源會將其資料寫入不同的表，但您可以在單個查詢中包含多個表，以分析跨多個源的資料。 生成查詢時，首先確定哪些表具有要查找的資料，因此至少應基本瞭解 Azure 監視器日誌中的資料的結構。

有關填充 Azure 監視器日誌的不同資料來源的清單，請參閱[Azure 監視器日誌的源](../platform/data-platform-logs.md#sources-of-azure-monitor-logs)。<br>
有關資料結構的說明，請參閱[Azure 監視器日誌的結構](logs-structure.md)。

## <a name="what-does-a-log-query-look-like"></a>日誌查詢是什麼樣子的？
查詢可以像從該表中檢索所有記錄的單個表名稱一樣簡單：

```Kusto
Syslog
```

或者，它可以篩選特定記錄，對其進行匯總，並在圖表中視覺化結果：

```
SecurityEvent
| where TimeGenerated > ago(7d)
| where EventID == 4625
| summarize count() by Computer, bin(TimeGenerated, 1h)
| render timechart 
```

對於更複雜的分析，可以使用聯接從多個表檢索資料，以一起分析結果。

```Kusto
app("ContosoRetailWeb").requests
| summarize count() by bin(timestamp,1hr)
| join kind= inner (Perf
    | summarize avg(CounterValue) 
      by bin(TimeGenerated,1hr))
on $left.timestamp == $right.TimeGenerated
```
即使您不熟悉 KQL，您至少也應該能夠找出這些查詢正在使用的基本邏輯。 它們從表的名稱開始，然後添加多個命令來篩選和處理該資料。 查詢可以使用任意數量的命令，並且在熟悉可用的不同 KQL 命令時，可以編寫更複雜的查詢。

有關介紹語言和常見函數的日誌查詢的教程，請參閱 Azure[監視器中的日誌查詢](get-started-queries.md)入門。<br>


## <a name="what-is-log-analytics"></a>什麼是 Log Analytics？
日誌分析是 Azure 門戶中用於編寫日誌查詢和以對話模式分析其結果的主要工具。 即使記錄查詢是用於 Azure 監視器中的其他地方，您通常還是先使用 Log Analytics 撰寫並測試查詢。

可以從 Azure 門戶中的多個位置開機記錄分析。 日誌分析可用的資料範圍取決於您啟動資料的方式。 有關詳細資訊[，請參閱查詢範圍](scope.md)。

- 從 Azure**監視器**功能表或**日誌分析工作區功能表中選擇****"日誌**"。
- 從應用程式見解應用程式的 **"概述"** 頁面選擇 **"分析**"。
- 從 Azure 資源的功能表中選擇 **"日誌**"。

![Log Analytics](media/log-query-overview/log-analytics.png)

有關日誌分析的教程演練，請參閱[Azure 監視器中的日誌分析入門](get-started-portal.md)，該演練介紹了其幾個功能。

## <a name="where-else-are-log-queries-used"></a>日誌查詢還使用在哪裡？
除了在日誌分析中以對話模式處理日誌查詢及其結果外，Azure 監視器中將使用查詢的區域還包括以下內容：

- **警示規則。** [警示規則](../platform/alerts-overview.md)會主動識別您的工作區中資料的問題。  每個警示規則是根據以固定間隔自動執行的記錄搜尋。  會檢查結果來判斷是否應該建立警示。
- **儀表板。** 您可以將任何查詢的結果釘選到 [Azure 儀表板](../learn/tutorial-logs-dashboards.md)中，如此即可以視覺化方式顯示記錄和計量資料，並選擇性地將其與其他 Azure 使用者共用。
- **視圖。**  您可以使用[檢視設計工具](../platform/view-designer.md)，建立要包含在使用者儀表板中的資料視覺效果。  記錄查詢會在各個檢視中提供[圖格](../platform/view-designer-tiles.md)和[視覺效果部分](../platform/view-designer-parts.md)所使用的資料。  
- **出口。**  當您從 Azure 監視器將記錄資料匯入到 Excel 或 [Power BI](../platform/powerbi.md) 時，您會建立定義要匯出之資料的記錄查詢。
- **電源外殼。** 可以從命令列運行 PowerShell 腳本，也可以運行使用[Get-Az操作見解搜尋結果](/powershell/module/az.operationalinsights/get-azoperationalinsightssearchresult)從 Azure 監視器檢索日誌資料的 Azure 自動化運行簿。  此 Cmdlet 需要查詢來決定要擷取的資料。
- **Azure 監視器記錄 API。**  [Azure 監視器記錄 API](https://dev.loganalytics.io) 可讓任何 REST API 用戶端從工作區擷取記錄資料。  API 要求包含針對 Azure 監視器執行的查詢，來判斷要擷取的資料。


## <a name="next-steps"></a>後續步驟
- 流覽[有關在 Azure 門戶中使用日誌分析的教程](get-started-portal.md)。
- 流覽[一下有關編寫查詢的教程](get-started-queries.md)。
