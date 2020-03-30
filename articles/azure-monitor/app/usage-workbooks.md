---
title: 使用 Azure 監視器活頁簿建立互動式報表 | Microsoft Docs
description: 使用預先建立及使用自訂參數之活頁簿來簡化複雜的報告
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 09/19/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 15543f7f761c707e8eff8e0cc0a0e4532475ddf8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670996"
---
# <a name="create-interactive-reports-with-azure-monitor-workbooks"></a>使用 Azure 監視器活頁簿建立互動式報表

活頁簿結合文字、 [Analytics 查詢](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)、Azure 計量以及參數，來建立豐富的互動式報表。 活頁簿可以由具有相同 Azure 資源存取權的其他小組成員編輯。

活頁簿對於下列案例很有用：

* 當您事先不知道感興趣的指標時，探索應用的使用方式：使用者數量、保留率、轉化率等。與其他使用方式分析工具不同，活頁簿允許您組合多種視覺化和分析，使其成為這種自由形式的探索的絕佳去。
* 向小組說明新發行的功能如何執行，方法是顯示關鍵互動和其他計量的使用者計數。
* 與小組的其他成員分享應用程式中 A/B 實驗的結果。 您可以使用文字說明實驗的目標，然後顯示用來評估實驗的每個使用方式計量和分析查詢，以及每個計量是高於或低於目標的清楚圖說文字。
* 報告應用程式使用方式中斷的影響，合併資料、文字說明和下一步的討論，以防止未來的中斷。

## <a name="starting-with-a-template-or-saved-workbook"></a>以範本或是既有活頁簿為基礎

活頁簿由獨立可編輯的圖表、表格、文字及輸入控制項之區段組成。 要了解活頁簿，最好的作法就是開啟一個實際例子。 

在您應用程式的 Application Insights 體驗中，從左側功能表選取 [活頁簿]****。

![瀏覽至活頁簿的螢幕擷取畫面](./media/usage-workbooks/001-workbooks.png)

這個步驟將會開啟一個活頁簿資源庫，其中有預先建立好的活頁簿來幫助您上手。

![活頁簿資源庫螢幕擷取畫面](./media/usage-workbooks/002-workbook-gallery.png)

我們先使用**預設範本**，就在**快速入門**標題之下。

![活頁簿資源庫螢幕擷取畫面](./media/usage-workbooks/003-default-template.png)

## <a name="editing-rearranging-cloning-and-deleting-workbook-sections"></a>編輯、重新排列、複製和刪除活頁簿區段

活頁簿有兩種模式：**編輯模式**和**閱讀模式**。 預設活頁簿一開始啟動時，會開啟為**編輯模式**。 此模式下將顯示活頁簿的所有內容，包含其他模式下隱藏之所有步驟與參數。 **閱讀模式**呈現簡化的報告樣式檢視畫面。 這樣讓您無需考慮建立報表所需之複雜元素，但在需要修改時只要簡單點擊幾下，仍能使用基本機制。

![Application Insights 活頁簿區段編輯控制項](./media/usage-workbooks/editing-controls-new.png)

1. 當您完成編輯區段時，按一下區段左下角的 [完成編輯]****。

2. 若要建立區段的複本，按一下 [複製此區段]**** 圖示。 建立重複的區段是逐一查看查詢而不會遺失先前反覆項目的絕佳方式。

3. 若要在活頁簿中將區段上移，按一下 [上移]**** 或 [下移]**** 圖示。

4. 若要永久移除區段，按一下 [移除]**** 圖示。

## <a name="adding-text-and-markdown-sections"></a>新增文字和 Markdown 區段

將標題、說明及註解新增至您的活頁簿，可協助將一組資料表和圖表變成敘述。 活頁簿中的文字區段支援 [Markdown 語法](https://daringfireball.net/projects/markdown/)以進行文字格式設定，例如標題、粗體、斜體和項目符號清單。

若要將文字區段新增至您的活頁簿，請使用活頁部底端或任何區段底端的 [新增文字]**** 按鈕。

## <a name="adding-query-sections"></a>加入查詢區段

![活頁簿中的查詢區段](./media/usage-workbooks/analytics-section-new.png)

若要將查詢區段新增至您的活頁簿，請使用活頁部底端或任何區段底端的 [新增查詢]**** 按鈕。

查詢區段十分有彈性，可以用以回答下述問題：

* 在與拒絕使用方式相同的時間週期期間，您的網站擲回多少例外狀況？
* 檢視某些分頁之使用者的分頁載入時間散發是多久？
* 有多少使用者檢視您的網站上的某些分頁集合，但是未檢視其他分頁集合？ 如果您有叢集使用者使用網站功能的不同子集 (使用 `join` 運算子搭配 [Kusto 查詢語言](/azure/kusto/query/)的 `kind=leftanti` 修飾詞)，這項功能相當有用。

您也不只能查詢與您開啟活頁簿之應用程式有關的問題。 只要您擁有資源的存取權，您即可跨越多個 Application Insights 監視之應用程式及 Log Analytics 工作區進行查詢。

要從外部的 Application Insights 資源進行查詢，須使用 **app** 識別碼。

```
union app('app01').requests, app('app02').requests, requests
| summarize count() by bin(timestamp, 1h)
```  

此查詢結合了來自三個不同應用程式的要求。 名為 app01 的應用程式、名為 app02 的應用程式，和來自本機 Application Insights 資源的要求。

要從外部 Log Analytics 工作區提取資料，須使用 **workspace** 識別碼。

若要深入了解跨資源的查詢，請參閱[官方指引](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search)。

### <a name="advanced-analytic-query-settings"></a>進階分析查詢設定

每個區段都有自己的進階設定，可以透過 [新增參數]**** 按鈕右側的設定圖示 ![Application Insights 活頁簿區段編輯控制項](./media/usage-workbooks/005-settings.png) 存取。

![Application Insights 活頁簿區段編輯控制項](./media/usage-workbooks/0006-settings-expanded.png)

   |         |          |
   | ---------------- |:-----|
   | **自訂寬度**    | 設定寬度來自定項目大小，最大化您在一行內可以容納的項目數量，讓您能更好地組織您的圖表及表格，建立出豐富的互動式報表。  |
   | **依條件顯示** | 使用此項目來根據參數值於閱讀模式中隱藏步驟。 |
   | **匯出參數**| 讓格線或圖表中指定的資料列能影響後面的步驟，變更步驟的值或是讓步驟變為可見。  |
   | **在未編輯時顯示查詢** | 在閱讀模式中於圖表或表格上方顯示查詢。
   | **在未進行編輯時顯示 [在分析中開啟] 按鈕** | 在圖表右側角落新增藍色 Analytics 圖示，讓您能一鍵存取。|

大部分的設定項目都很直覺，但要了解**匯出參數**，最好檢視使用此一功能之活頁簿。

其中一個預設活頁簿提供關於作用中使用者的資訊。

活頁簿的第一個區段以分析查詢資料為基礎：

![Application Insights 活頁簿區段編輯控制項](./media/usage-workbooks/003-active-users.png)

第二個區段也是根據分析的查詢資料建立，但在第一個表格中選取一個資料列，將會自動更新圖表的內容：

![Application Insights 活頁簿區段編輯控制項](./media/usage-workbooks/004-active-users-trend.png)

 要達到這點，要在表格 Analytics 查詢之進階設定中勾選**在項目被選取時匯出參數**。

![Application Insights 活頁簿區段編輯控制項](./media/usage-workbooks/007-settings-export.png)

第二個分析查詢會使用資料列被選取時匯出的值。 若沒有任何資料列被選取，預設使用代表整體值的資料列。 

```
let start = startofday(ago({TimeRange} + {Metric}));
union customEvents, pageViews
| where timestamp >= start
| where name in ({Activities}) or '*' in ({Activities}) or ('%' in ({Activities}) and itemType == 'pageView') or ('#' in ({Activities}) and itemType == 'customEvent')
{OtherFilters}
| where '{Filter}' == '' or '{Filter}' == '🔸 Overall' or {AnalyzeBy} == replace('🔹 ', '', '{Filter}')
| evaluate activity_engagement(user_Id, timestamp, start, now(), 1d, {Metric})
| where timestamp >= startofday(ago({TimeRange}))
| project timestamp, ["Active User"] = dcount_activities_outer
| render timechart 
```

## <a name="adding-metrics-sections"></a>新增計量區段

計量區段讓您能存取所有 Azure 監視器計量資料，納入您的互動式報表中。 許多預設的活頁簿包含了分析查詢資料及計量資料，讓您能在單一活頁簿中充分利用兩種功能的好處。 您也可以從任何可存取之訂閱的資源，提取計量資料。

以下範例將虛擬機器資料提取至活頁簿，以格線視覺化 CPU 的效能：

![Application Insights 活頁簿區段編輯控制項](./media/usage-workbooks/008-metrics-grid.png)

## <a name="adding-parameter-sections"></a>新增參數區段

活頁簿參數讓您不需要手動編輯查詢或文字區段，便能改變活頁簿的值。  這樣便不需要了解其中分析查詢的語言，大幅拓展以活頁簿為基礎之報表潛在的觀眾。

將參數的名稱放在括弧中，像是 ``{parameterName}``，來取代查詢、文字、或其他參數區段中參數的值。  參數名稱受到類似於 JavaScript 識別碼規則的限制，基本上開頭為英文字母或底線，加上英文字母或數字或底線。 舉例來說，**a1** 是可用的名稱，但 **1a** 則不被允許。

參數是線性的，從活頁簿最頂端往後面的步驟移動。  活頁簿後面宣告的參數可以覆寫前面宣告的參數。  這也代表使用詢問知參數可以存取前面參數定義的值。  在單一參數步驟中，參數也是線性的，順序從左到右，右邊的參數可以仰賴同一步驟中已經宣告的參數。
 
目前支援四種不同的參數：

  |         |          |
   | ---------------- |:-----|
   | **Text**    | 使用者會編輯文字方塊，您可以選擇提供詢問來填寫預設值。 |
   | **下拉式清單** | 使用者會從一組值之中選擇。 |
   | **時間範圍選擇器**| 使用者會從預先定義之一組時間範圍值中選擇，或是從自訂之時間範圍中選擇。|
   | **資源選取器** | 使用者會從選取供活頁簿使用之資源中選擇。|

### <a name="using-a-text-parameter"></a>使用文字參數

使用者於文字方塊中輸入的值將在查詢中直接取代，不含任何跳脫字元或引號。 如果您需要的值為字串，查詢中必須在參數前後加上引號 (像是 **'{parameter}'**)。

這樣文字方塊中的值即可在任何地方使用。 可以用於表格名稱、資料行名稱、函數名稱、運算子等等。

文字參數類型中可以設定 [從分析查詢取得預設值]****，讓活頁簿作者使用查詢來填入該文字方塊的預設值。

使用分析查詢作為預設值時，只有第一列的第一個值 (第 0 列，第 0 行) 會被使用。 因此建議您將查詢限制為只傳回一行一列。 查詢所傳回的任何其他資料會被忽略。 

查詢傳回的任何值都將會直接被取代為不含逃脫字元或引號的版本。 若查詢未傳回任何資料列，參數之結果將為空白字串 (若參數並非必須) 或未定義 (若參數為必須)。

### <a name="using-a-dropdown"></a>使用下拉式清單

下拉式清單中的參數型別讓您可建立一個下拉式控制項，得選擇一個或多個值。

下拉式清單內容將由分析查詢填入。 若查詢傳回一個資料行，資料行中的值同時為下拉式控制項的**值**與**標籤**。 若查詢傳回兩個資料行，第一個資料行為**值**，而第二個則為下拉式清單顯示之**標籤**。  若查詢回傳三個資料行，第三個資料行將用來指定下拉式清單預設之選取項目。  此一資料行可以為任何型別，最簡單的方式是使用布林或數字型別，0 為 false，1 為 true。

 若資料行為字串型別，Null/空白字串將被視為 false，其他任何值都視為 true。 在單選之下拉式清單中，第一個值為 true 的項目將成為預設選取項目。  在多選之下拉式清單中，所有值為 true 的項目都將成為預設選取項目。 下拉式清單之項目顯示的順序將依照查詢回傳的資料列順序。 

讓我們看看這個作用中使用者報告中的參數。 按一下 [時間範圍]**** 旁邊的編輯符號。

![Application Insights 活頁簿區段編輯控制項](./media/usage-workbooks/009-time-range.png)

這會開啟 [編輯參數] 功能表項目：

![Application Insights 活頁簿區段編輯控制項](./media/usage-workbooks/010-time-range-edit.png)

此查詢使用 **datatable** 這個分析查詢語言中的功能，讓您立即隨機產生填滿值的表格。 舉例來說，下面的分析查詢：

```
datatable( column1:string, column2:string )
[
 "row 1 column 1", "row 1 column 2",
"row 2 column 1", "row 2 column 2"
]
```

產生結果：

![Application Insights 活頁簿區段編輯控制項](./media/usage-workbooks/011-data-table.png)

更適用的示例是使用下拉清單按名稱從一組國家/地區中選取：

```
customEvents
| where timestamp >= ago(14d)
| summarize count() by client_CountryOrRegion
| top 100 by count_
| project  client_CountryOrRegion 
| order by client_CountryOrRegion asc
```

查詢如下顯示結果：

![國家/地區下拉式清單](./media/usage-workbooks/012-country-dropdown.png)

在自訂及建立互動式報表時，下拉式清單為非常強大的工具。

### <a name="time-range-parameters"></a>時間範圍參數

雖然您可以使用下拉式參數型別來建立您自己自訂的時間範圍參數，若您不需要自訂參數所提供的彈性，您也可以使用已經建立好的時間範圍參數型別。 

時間範圍參數型別有 15 種預設範圍，短至五分鐘，長至 90 天。 您還可以選擇允許自訂時間範圍選取，讓報表的操作者能指定時間範圍的起始值及終止值。

### <a name="resource-picker"></a>資源選擇器

資源選擇器參數型別讓您能根據特定資源類型設定報表範圍。 其中一個利用資源選擇器的範例為 **Failure Insights** 活頁簿。

![國家/地區下拉式清單](./media/usage-workbooks/013-resource-picker.png)

## <a name="saving-and-sharing-workbooks-with-your-team"></a>儲存活頁簿並且與小組共用

活頁簿會儲存在 Application Insights 資源中，儲存在您私人使用的 [我的報表]**** 區段中，或是可供具有 Application Insights 資源存取權的每個人存取的 [共用報表]**** 區段中。 若要檢視資源中的所有活頁簿，請按一下動作列中的 [開啟]**** 按鈕。

若要共用目前在 [我的報表]**** 的活頁簿：

1. 按一下動作列中的 [開啟]****
2. 按一下您想要共用之活頁簿旁邊的 [...] 按鈕
3. 按一下 [移至共用報表]****。

若要使用連結或透過電子郵件共用活頁簿，請按一下動作列中的 [共用]****。 請注意，連結的收件者需要在 Azure 入口網站中存取此資源的存取權，才能檢視活頁簿。 若要進行編輯，收件者至少需要資源的參與者權限。

若要將活頁簿的連結釘選到 Azure 儀表板：

1. 按一下動作列中的 [開啟]****
2. 按一下您想要釘選之活頁簿旁邊的 [...] 按鈕
3. 按一下 [釘選到儀表板]****。

## <a name="contributing-workbook-templates"></a>提供活頁簿範本

您是否建立了很棒的活頁簿範本，想要跟整個社群分享？ 若要深入了解，請至我們的 [GitHub repo](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md)。

## <a name="next-steps"></a>後續步驟
- 若要啟用使用體驗，請開始傳送[「自訂事件」](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) 或 [「頁面檢視」](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views)。
- 如果您已傳送自訂事件或頁面檢視，請探索「使用量工具」，以了解使用者如何使用您的服務。
    - [使用者、工作階段、事件](../../azure-monitor/app/usage-segmentation.md)
    - [漏斗圖](../../azure-monitor/app/usage-funnels.md)
    - [保留](../../azure-monitor/app/usage-retention.md)
    - [使用者流程](../../azure-monitor/app/usage-flows.md)
    - [新增使用者內容](../../azure-monitor/app/usage-send-user-context.md)
