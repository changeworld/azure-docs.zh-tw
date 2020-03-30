---
title: 在 Azure Application Insights 中使用搜尋服務 | Microsoft Docs
description: 搜尋和篩選 Web 應用程式傳送的原始遙測。
ms.topic: conceptual
ms.date: 07/30/2019
ms.openlocfilehash: 8039a55784f63030f330d6c1e2061e99b8b63bbf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275980"
---
# <a name="using-search-in-application-insights"></a>在 Application Insights 中使用搜尋

「搜尋」是 [Application Insights](../../azure-monitor/app/app-insights-overview.md) 的一項功能，可用來尋找及探索個別遙測項目，例如頁面檢視、例外狀況或 Web 要求。 而您可以檢視所編寫的記錄追蹤和事件。

(若要對您的資料執行更複雜的查詢，請使用[分析](../../azure-monitor/log-query/get-started-portal.md)。)

## <a name="where-do-you-see-search"></a>「搜尋」在哪裡？

### <a name="in-the-azure-portal"></a>在 Azure 入口網站

您可以在應用程式的"應用程式洞察概述"選項卡（位於頂部欄）或左側調查中打開診斷搜索。

![[搜尋] 索引標籤](./media/diagnostic-search/view-custom-events.png)

轉到事件種類的下拉式功能表，查看遙測項清單 - 伺服器請求、網頁檢視、已編碼的自訂事件等。 在結果清單的頂部，是顯示隨時間發生的事件計數的匯總圖表。

按一下下拉式功能表或"刷新"以獲取新事件。

### <a name="in-visual-studio"></a>在 Visual Studio 中

在 Visual Studio 中，也有 [Application Insights 搜尋] 視窗。 它最適合顯示您偵錯的應用程式所產生的遙測事件。 還可以顯示 Azure 入口網站上從您已發佈的應用程式收集的事件。

在 Visual Studio 中開啟 [搜尋] 視窗：

![Visual Studio 開啟 Application Insights 搜尋](./media/diagnostic-search/32.png)

[搜尋] 視窗有類似 Web 入口網站的功能：

![Visual Studio Application Insights 搜尋視窗](./media/diagnostic-search/34.png)

您開啟要求或頁面檢視時可以看到 [追蹤作業] 索引標籤。 「作業」是與單一要求或頁面檢視相關聯的一連串事件。 例如，相依性呼叫、例外狀況、追蹤記錄和自訂事件可能都是單一作業的一部分。 針對要求或頁面檢視，[追蹤作業] 索引標籤會以圖形顯示這些事件的時刻和持續時間。

## <a name="inspect-individual-items"></a>檢查個別項目

選取任何遙測項目來查看重要欄位和相關項目。

![個別相依性要求的螢幕擷取畫面](./media/diagnostic-search/telemetry-item.png)

這將會啟動端對端交易詳細資料檢視。

## <a name="filter-event-types"></a>篩選事件類型

打開事件種類的下拉式功能表並選擇要查看的事件種類。 （如果以後要還原篩選器，請按一下"重置"。

事件類型包括：

* **跟蹤** - [診斷日誌](../../azure-monitor/app/asp-net-trace-logs.md)，包括跟蹤跟蹤、日誌4Net、NLog 和系統.診斷.跟蹤調用。
* **要求** - 伺服器應用程式收到的 HTTP 要求，包括頁面、指令碼、影像、樣式檔案和資料。 這些事件是用來建立要求和回應概觀圖表。
* **網頁視圖** - [遙測由 Web 用戶端發送](../../azure-monitor/app/javascript.md)，用於創建網頁檢視報表。
* **自訂事件** - 如果您插入 TrackEvent() 呼叫來[監視使用量](../../azure-monitor/app/api-custom-events-metrics.md)，則可以在這裡搜尋它們。
* **異常**-[伺服器中未捕獲的異常](../../azure-monitor/app/asp-net-exceptions.md)，以及使用 TrackException（） 記錄的異常。
* **從** - [伺服器應用程式](../../azure-monitor/app/asp-net-dependencies.md)到其他服務（如 REST API 或資料庫）的依賴項調用，以及來自[用戶端代碼](../../azure-monitor/app/javascript.md)的 AJAX 調用。
* **可用性** - [可用性測試](../../azure-monitor/app/monitor-web-app-availability.md)的結果。

## <a name="filter-on-property-values"></a>依據屬性值篩選

您可以依屬性的值篩選事件。 可用的屬性因您所選取的事件類型而定。 按一下篩選器圖示 ![篩選圖示](./media/diagnostic-search/filter-icon.png) 開始。

如果未針對特定屬性選擇任何值，則相當於選擇全部的值。 這樣會關掉該屬性的篩選功能。

篩選器值右側的計數顯示目前篩選的集合中有多少個出現次數。

## <a name="find-events-with-the-same-property"></a>尋找具有相同屬性的事件

要查找具有相同屬性值的所有項，請在搜索欄中鍵入該項，或在篩選器選項卡中查看屬性時按一下核取方塊。

![按一下篩選器選項卡中屬性的核取方塊](./media/diagnostic-search/filter-property.png)

## <a name="search-the-data"></a>搜尋資料

> [!NOTE]
> 要編寫更複雜的查詢，請從搜索邊欄選項卡的頂部打開[**日誌（分析）。**](../../azure-monitor/log-query/get-started-portal.md)
>

您可以搜尋任何屬性值中的字詞。 如果您已編寫具有屬性值的[自訂事件](../../azure-monitor/app/api-custom-events-metrics.md)，則此功能非常有用。

您可能想要設定時間範圍，因為對一小段時間範圍進行的搜尋較快速。

![Open diagnostic search](./media/diagnostic-search/search-property.png)

請搜尋完整單字，而不是子字串。 使用引號來括住的特殊字元。

| String | *未*找到 | 已找到 |
| --- | --- | --- |
| HomeController.About |`home`<br/>`controller`<br/>`out` | `homecontroller`<br/>`about`<br/>`"homecontroller.about"`|
|美國|`Uni`<br/>`ted`|`united`<br/>`states`<br/>`united AND states`<br/>`"united states"`

以下是您可以使用搜尋運算式：

| 範例查詢 | 效果 |
| --- | --- |
| `apple` |在日期範圍中尋找欄位含有 "apple" 這個字的所有事件 |
| `apple AND banana` <br/>`apple banana` |尋找同時含有這兩個字的事件。 請使用大寫 "AND"，而不是 "and"。 <br/>簡短格式。 |
| `apple OR banana` |尋找含有任一單字的事件。 請使用 "OR"，而不是 "or"。 |
| `apple NOT banana` |尋找含有一個字但不含另一個字的事件。 |

## <a name="sampling"></a>取樣

如果應用生成大量遙測（並且您使用的是ASP.NET SDK 版本 2.0.0-Beta3 或更高版本），自我調整採樣模組通過僅發送具有代表性的事件部分，自動減少發送到門戶的卷。 不過，同一個要求相關的事件是以整組方式來選取或取消選取，方便您切換瀏覽相關事件。

[了解取樣](../../azure-monitor/app/sampling.md)。

## <a name="create-work-item"></a>建立工作項目

您可以利用來自任何遙測項目的詳細資料，在 GitHub 或 Azure DevOps 中建立 Bug。

通過按一下任何遙測項，然後選擇 **"創建工作項**"，轉到端到端交易記錄詳細資訊視圖。

![按一下 [新增工作項目]、編輯欄位，然後按一下 [確定]。](./media/diagnostic-search/work-item.png)

當您第一次執行此動作時，系統會要求您設定與 Azure DevOps 組織和專案的連結。

（您還可以在"工作項"選項卡上配置連結。

## <a name="send-more-telemetry-to-application-insights"></a>傳送更多遙測到 Application Insights

除了 Application Insights SDK 傳送的內建遙測，您可以：

* 在 [.NET](../../azure-monitor/app/asp-net-trace-logs.md) 或 [Java](../../azure-monitor/app/java-trace-logs.md) 中，從您最喜愛的紀錄架構擷取記錄追蹤。 這表示您可以搜尋您的記錄追蹤，並將它們與頁面檢視、例外狀況和其他事件相互關聯。
* [編寫程式碼](../../azure-monitor/app/api-custom-events-metrics.md)以傳送自訂事件、頁面檢視和例外狀況。

[了解如何將記錄和自訂遙測傳送至 Application Insights](../../azure-monitor/app/asp-net-trace-logs.md)。

## <a name="q--a"></a><a name="questions"></a>問與答

### <a name="how-much-data-is-retained"></a><a name="limits"></a>保留多少資料？

請參閱[限制摘要](../../azure-monitor/app/pricing.md#limits-summary)。

### <a name="how-can-i-see-post-data-in-my-server-requests"></a>我如何查看我的伺服器要求中的 POST 資料？

我們不會自動記錄 POST 資料，但您可以使用 [TrackTrace 或記錄呼叫](../../azure-monitor/app/asp-net-trace-logs.md)。 將 POST 資料放在訊息參數中。 您無法像篩選屬性一樣來篩選訊息，但大小限制比較長。

## <a name="next-steps"></a><a name="add"></a>後續步驟

* [在分析中撰寫複雜的查詢](../../azure-monitor/log-query/get-started-portal.md)
* [將記錄和自訂遙測傳送至 Application Insights](../../azure-monitor/app/asp-net-trace-logs.md)
* [設定可用性和回應性測試](../../azure-monitor/app/monitor-web-app-availability.md)
* [疑難排解](../../azure-monitor/app/troubleshoot-faq.md)
