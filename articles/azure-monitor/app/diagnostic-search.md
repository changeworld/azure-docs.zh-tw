---
title: 在 Azure Application Insights 中使用搜尋服務 | Microsoft Docs
description: 搜尋和篩選 Web 應用程式傳送的原始遙測。
ms.topic: conceptual
ms.date: 07/30/2019
ms.openlocfilehash: 8039a55784f63030f330d6c1e2061e99b8b63bbf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84695652"
---
# <a name="using-search-in-application-insights"></a>在 Application Insights 中使用搜尋

「搜尋」是 [Application Insights](../../azure-monitor/app/app-insights-overview.md) 的一項功能，可用來尋找及探索個別遙測項目，例如頁面檢視、例外狀況或 Web 要求。 而您可以檢視所編寫的記錄追蹤和事件。

(若要對您的資料執行更複雜的查詢，請使用[分析](../../azure-monitor/log-query/get-started-portal.md)。)

## <a name="where-do-you-see-search"></a>「搜尋」在哪裡？

### <a name="in-the-azure-portal"></a>在 Azure 入口網站

您可以從應用程式的 [Application Insights 總覽] 索引標籤（位於頂端列）或左側的 [調查] 底下開啟診斷搜尋。

![[搜尋] 索引標籤](./media/diagnostic-search/view-custom-events.png)

請移至 [事件種類] 下拉式功能表，以查看遙測專案的清單-伺服器要求、頁面流覽、您已撰寫程式碼的自訂事件等等。 在 [結果] 清單的頂端，是顯示一段時間內事件計數的摘要圖表。

按一下下拉式功能表或 [重新整理]，以取得新的事件。

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

開啟 [事件種類] 下拉式功能表，然後選擇您想要查看的事件種類。 （如果您稍後想要還原篩選，請按一下 [重設]）。

事件類型包括：

* **追蹤**  - [診斷記錄](../../azure-monitor/app/asp-net-trace-logs.md)，包括 TrackTrace、Log4Net、NLog 和呼叫。
* **要求** - 伺服器應用程式收到的 HTTP 要求，包括頁面、指令碼、影像、樣式檔案和資料。 這些事件是用來建立要求和回應概觀圖表。
* **網頁檢視**  - [由 web 用戶端傳送的遙測](../../azure-monitor/app/javascript.md)資料，用來建立頁面流覽報表。
* **自訂事件** - 如果您插入 TrackEvent() 呼叫來[監視使用量](../../azure-monitor/app/api-custom-events-metrics.md)，則可以在這裡搜尋它們。
* **例外**狀況-伺服器中未攔截到[的例外](../../azure-monitor/app/asp-net-exceptions.md)狀況，以及您使用 TrackException （）來記錄的例外狀況。
* 相依性**Dependency**  - [從您的伺服器應用程式呼叫](../../azure-monitor/app/asp-net-dependencies.md)其他服務（例如 REST api 或資料庫），以及來自[用戶端程式代碼](../../azure-monitor/app/javascript.md)的 AJAX 呼叫。
* **可用性** - [可用性測試](../../azure-monitor/app/monitor-web-app-availability.md)的結果。

## <a name="filter-on-property-values"></a>依據屬性值篩選

您可以依屬性的值篩選事件。 可用的屬性因您所選取的事件類型而定。 按一下篩選圖示 ![篩選圖示](./media/diagnostic-search/filter-icon.png) 以開始。

如果未針對特定屬性選擇任何值，則相當於選擇全部的值。 這樣會關掉該屬性的篩選功能。

篩選器值右側的計數顯示目前篩選的集合中有多少個出現次數。

## <a name="find-events-with-the-same-property"></a>尋找具有相同屬性的事件

若要尋找具有相同屬性值的所有專案，請在搜尋列中輸入，或在流覽 [篩選] 索引標籤中的屬性時按一下核取方塊。

![按一下 [篩選] 索引標籤中屬性的核取方塊](./media/diagnostic-search/filter-property.png)

## <a name="search-the-data"></a>搜尋資料

> [!NOTE]
> 若要撰寫更複雜的查詢，請從 [搜尋] 分頁頂端開啟 [[**記錄（分析）**](../../azure-monitor/log-query/get-started-portal.md) ]。
>

您可以搜尋任何屬性值中的字詞。 如果您已撰寫具有屬性值的[自訂事件](../../azure-monitor/app/api-custom-events-metrics.md)，這就很有用。

您可能想要設定時間範圍，因為對一小段時間範圍進行的搜尋較快速。

![Open diagnostic search](./media/diagnostic-search/search-property.png)

請搜尋完整單字，而不是子字串。 使用引號來括住的特殊字元。

| String | 找*不*到 | 已找到 |
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

如果您的應用程式會產生大量的遙測（而且您使用 ASP.NET SDK 版本 2.0.0-Beta3 或更新版本），調適型取樣模組會自動只傳送代表性的事件數，以減少傳送至入口網站的磁片區。 不過，同一個要求相關的事件是以整組方式來選取或取消選取，方便您切換瀏覽相關事件。

[了解取樣](../../azure-monitor/app/sampling.md)。

## <a name="create-work-item"></a>建立工作項目

您可以利用來自任何遙測項目的詳細資料，在 GitHub 或 Azure DevOps 中建立 Bug。

按一下任何遙測專案，然後選取 [**建立工作專案**]，移至端對端交易詳細資料檢視。

![按一下 [新增工作項目]、編輯欄位，然後按一下 [確定]。](./media/diagnostic-search/work-item.png)

當您第一次執行此動作時，系統會要求您設定與 Azure DevOps 組織和專案的連結。

（您也可以在 [工作專案] 索引標籤上設定連結）。

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
