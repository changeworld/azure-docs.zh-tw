---
title: Azure 監視器活頁簿視覺效果
description: 瞭解所有 Azure 監視器活頁簿視覺效果元件，包括文字、圖表、格線、樹狀結構和圖形。
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: d05902f47dff3dd2f8a63ae240c0b8825a5c441f
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77658025"
---
# <a name="azure-monitor-workbook-visualizations"></a>Azure 監視器活頁簿視覺效果

Azure 監視器活頁簿支援多種不同的視覺效果樣式，以符合您的報告需求。 本文提供每種視覺效果類型的範例。

## <a name="text"></a>Text

活頁簿可讓作者在其活頁簿中包含文字區塊。 此文字可以是遙測的人工分析，這項資訊可協助使用者解讀您的資料、章節標題等等。

![Apdex 文字資料表的螢幕擷取畫面](./media/workbooks-visualizations/apdex.png)

文字是透過提供完整格式控制項的 Markdown 控制項來加入。

![原始 markdown 的螢幕擷取畫面，其會建立呈現的資料表](./media/workbooks-visualizations/markdown.png)

### <a name="add-a-text-control"></a>加入文字控制項

1. 按一下 [**編輯**] 工具列專案，將活頁簿切換至編輯模式。
2. 使用 [**加入文字**] 連結，將文字控制項加入至活頁簿。
3. 將 Markdown 新增至控制項。
4. 按一下 [**完成編輯**] 按鈕，以查看格式化的文字。

> [!TIP]
> 使用這[份 Markdown](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet)功能提要來瞭解不同的格式化選項。

## <a name="charts"></a>圖表

活頁簿可讓監視資料呈現為圖表。 支援的圖表類型包括折線圖、橫條圖、橫條圖分類、區域、散佈圖、圓形圖和時間。 作者可以選擇自訂圖表的高度、寬度、色彩調色板、圖例、標題、無資料訊息等等。

活頁簿支援記錄和度量資料來源的圖表。 

### <a name="adding-a-log-chart"></a>加入記錄圖表

1. 按一下 [**編輯**] 工具列專案，將活頁簿切換至編輯模式。
2. 使用 [**加入查詢**] 連結，將記錄查詢控制項加入至活頁簿。
3. 選取 [**記錄**]、[資源類型] （例如 Application Insights）和 [目標資源] 做為 [查詢類型]。
4. 使用 [查詢編輯器] 輸入分析的[KQL](https://docs.microsoft.com/azure/kusto/query/) （例如，要求的趨勢）。
5. 將視覺效果設定為 [**區域**]、[**條形**圖]、 **[橫條圖（類別）** ]、[**折線圖**]、[**圓形圖**]、[**散佈圖** **] 或**
6. 如有需要，請設定其他參數，例如時間範圍、視覺效果、大小、色彩調色板和圖例。

![編輯模式中的記錄圖表螢幕擷取畫面](./media/workbooks-visualizations/log-chart.png)

#### <a name="log-chart-parameters"></a>記錄圖表參數

| 參數 | 說明 | 範例 |
| ------------- |:-------------|:-------------|
| `Query Type` | 要使用的查詢類型 | 記錄、Azure Resource Graph 等等。 |
| `Resource Type` | 要設為目標的資源類型 | Application Insights、Log Analytics 或 Azure 優先 |
| `Resources` | 要從中取得計量值的一組資源 | MyApp1 |
| `Time Range` | 要查看記錄圖表的時間範圍 | 過去一小時、過去24小時等。 |
| `Visualization` | 要使用的視覺效果 | 區域、橫條圖、折線圖、圓形圖、散佈圖、時間、橫條圖類別 |
| `Size` | 控制項的垂直大小 | 小型、中型、大型或完整 |
| `Color palette` | 要在圖表中使用的色彩色板。 在多重度量或分割模式中略過。 | 藍色、綠色、紅色等等。 |
| `Legend` | 要用於圖例的彙總函式 | 值的總和或平均值，或最大、最小、第一個、最後一個值 |
| `Query` | 以圖表視覺效果所預期的格式傳回資料的任何 KQL 查詢 | _要求 \| 進行 a 系列要求 = count （）預設值 = 0，時間戳記從前（1d）到 now （）步驟1h_ |

### <a name="adding-a-metric-chart"></a>新增度量圖表

1. 按一下 [**編輯**] 工具列專案，將活頁簿切換至編輯模式。
2. 使用 [**新增度量**] 連結，將計量控制項加入至活頁簿。
3. 選取資源類型（例如，儲存體帳戶）、要設為目標的資源、度量命名空間和名稱，以及要使用的匯總。
4. 如有需要，請設定其他參數，例如時間範圍、分割依據、視覺效果、大小和色彩色板。

![編輯模式中度量圖表的螢幕擷取畫面](./media/workbooks-visualizations/metric-chart.png)

#### <a name="metric-chart-parameters"></a>度量圖表參數

| 參數 | 說明 | 範例 |
| ------------- |:-------------|:-------------|
| `Resource Type` | 要設為目標的資源類型 | 儲存體或虛擬機器。 |
| `Resources` | 要從中取得計量值的一組資源 | MyStorage1 |
| `Namespace` | 具有度量的命名空間 | 儲存體 > Blob |
| `Metric` | 要視覺化的度量 | 儲存體 > Blob > 交易 |
| `Aggregation` | 要套用至度量的彙總函式 | 加總、計數、平均等等。 |
| `Time Range` | 要在其中查看度量的時間範圍 | 過去一小時、過去24小時等。 |
| `Visualization` | 要使用的視覺效果 | 區域、橫條圖、折線圖、散佈圖、方格 |
| `Split By` | 選擇性地分割維度上的度量 | 依地理類型的交易 |
| `Size` | 控制項的垂直大小 | 小型、中型或大型 |
| `Color palette` | 要在圖表中使用的色彩色板。 如果使用 `Split by` 參數，則忽略 | 藍色、綠色、紅色等等。 |

## <a name="grids"></a>方格

格線或資料表是向使用者呈現資料的常見方式。 活頁簿可讓使用者個別將方格的資料行樣式，為其報表提供豐富的 UI。

下列範例顯示的方格結合了圖示、熱度圖和 spark 橫條來呈現複雜的資訊。 活頁簿也提供排序、搜尋方塊和 [移至分析] 按鈕。

![以記錄為基礎之方格的螢幕擷取畫面](./media/workbooks-visualizations/grid.png)

### <a name="adding-a-log-based-grid"></a>加入以記錄為基礎的方格

1. 按一下 [**編輯**] 工具列專案，將活頁簿切換至編輯模式。
2. 使用 [**加入查詢**] 連結，將記錄查詢控制項加入至活頁簿。
3. 選取 [**記錄**]、[資源類型] （例如 Application Insights）和 [目標資源] 做為 [查詢類型]。
4. 使用查詢編輯器來輸入分析的 KQL （例如，記憶體低於閾值的 Vm）
5. 將視覺效果設定為**方格**
6. 如有需要，請設定其他參數，例如時間範圍、大小、色彩調色板和圖例。

![以記錄為基礎之方格查詢的螢幕擷取畫面](./media/workbooks-visualizations/grid-query.png)

## <a name="tiles"></a>圖格

磚是在活頁簿中呈現摘要資料非常有用的方式。 下圖顯示磚的常見使用案例-應用層級摘要位於詳細方格的頂端。

![磚摘要視圖的螢幕擷取畫面](./media/workbooks-visualizations/tiles-summary.png)

活頁簿磚支援顯示標題、副標題、大型文字、圖示、以度量為基礎的漸層、spark 線條/橫條圖、頁尾等等。

### <a name="adding-a-tile"></a>新增磚

1. 按一下 [_編輯_] 工具列專案，將活頁簿切換至編輯模式。
2. 使用 [**加入查詢**] 連結，將記錄查詢控制項加入至活頁簿。 
3. 選取 [**記錄**]、[資源類型] （例如 Application Insights）和 [目標資源] 做為 [查詢類型]。
4. 使用查詢編輯器來輸入分析的 KQL
    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | top 7 by Requests desc
    ```
5. 將大小設定為**Full**
6. 將視覺效果設為**磚**
7. 按一下 [**磚設定**] 按鈕以開啟 [設定] 窗格
8. 在 [**磚欄位**] 中，設定：
    * 標題： `name`
    * Left： `Requests`，轉譯器： `Big Number`，色調色板： `Green to Red`，最小值： `0`
    * 下： `appName`
9. 按一下窗格底部的 [**儲存並關閉**] 按鈕。

![磚摘要視圖的螢幕擷取畫面](./media/workbooks-visualizations/tile-settings.png)

這是磚在讀取模式中的外觀：

![磚摘要視圖的螢幕擷取畫面](./media/workbooks-visualizations/tiles-read-mode.png)

## <a name="trees"></a>樹木

活頁簿支援透過樹狀格線的階層式視圖。 樹狀結構可讓一些資料列可擴充到下一個層級，以提供向下切入體驗。

下列範例顯示以樹狀方格呈現的容器健康情況計量（工作集大小）。 這裡的最上層節點是 Azure Kubernetes Service （AKS）節點，下一個層級是 pod，而最後一個層級是容器。 請注意，您仍然可以在方格中格式化資料行（例如，熱度圖、圖示、連結）。 在此情況下，基礎資料來源是具有 AKS 記錄的 Log Analytics 工作區。

![磚摘要視圖的螢幕擷取畫面](./media/workbooks-visualizations/trees.png)

### <a name="adding-a-tree-grid"></a>加入樹狀格線
1. 按一下 [_編輯_] 工具列專案，將活頁簿切換至編輯模式。
2. 使用 [**加入查詢**] 連結，將記錄查詢控制項加入至活頁簿。 
3. 選取 [**記錄**]、[資源類型] （例如 Application Insights）和 [目標資源] 做為 [查詢類型]。
4. 使用查詢編輯器來輸入分析的 KQL
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
5. 將視覺效果設定為**方格**
6. 按一下 [資料**行設定**] 按鈕以開啟 [設定] 窗格
7. 在底部的 [**樹狀結構/群組依據設定**] 區段中，設定：
    * 樹狀結構類型： `Parent/Child`
    * 識別碼欄位： `Id`
    * 父識別碼欄位： `ParentId`
    * 在上顯示擴充器： `Name`
    * 展開樹狀結構的最上層： `checked`
8. 在頂端的 [資料_行_] 區段中，設定：
    * _識別碼_-資料行轉譯器： `Hidden`
    * _父識別碼_-資料行轉譯器： `Hidden`
    * _Requests_ -Column 轉譯器： `Bar`，Color： `Blue`，最小值： `0`
9. 按一下窗格底部的 [_儲存並關閉_] 按鈕。    

![磚摘要視圖的螢幕擷取畫面](./media/workbooks-visualizations/tree-settings.png)

### <a name="tree-settings"></a>樹狀結構設定

| 設定 | 說明 |
|:------------- |:-------------|
| `Id Field` | 方格中每個資料列的唯一識別碼 |
| `Parent Id Field` | 目前資料列的父系識別碼 |
| `Show the expander on` | 要在其上顯示樹狀結構展開器的資料行。 樹狀結構方格通常會隱藏其 [識別碼] 和 [父系識別碼] 欄位，因為它們並不容易閱讀。 相反地，展開器會出現在具有更容易閱讀值的欄位上（例如實體的名稱） |
| `Expand the top level of the tree` | 若選取此選項，樹狀目錄方格將會在最上層展開。 如果您想要依預設顯示詳細資訊，則很有用 |

## <a name="graphs"></a>圖形

活頁簿支援根據記錄中的資料視覺化任意圖形，以顯示監視實體之間的關聯性。

下圖顯示透過各種埠進出外部電腦來流入/流出電腦的資料。 它會依類型（電腦與埠與外部 IP）來著色，而邊緣大小會對應到介於之間流動的資料量。 基礎資料來自 KQL 以 VM 連接為目標的查詢。

![磚摘要視圖的螢幕擷取畫面](./media/workbooks-visualizations/graph.png)

### <a name="adding-a-graph"></a>加入圖形
1. 按一下 [_編輯_] 工具列專案，將活頁簿切換至編輯模式。
2. 使用 [**加入查詢**] 連結，將記錄查詢控制項加入至活頁簿。 
3. 選取 [**記錄**]、[資源類型] （例如 Application Insights）和 [目標資源] 做為 [查詢類型]。
4. 使用查詢編輯器來輸入分析的 KQL
    ```kusto
    let data = dependencies
    | summarize Calls = count() by App = appName, Request = operation_Name, Dependency = name
    | extend RequestId = strcat(App, '::', Request);
    let links = data
    | summarize Calls = sum(Calls) by App, RequestId
    | project SourceId = App, TargetId = RequestId, Calls, Kind = 'App -> Request'
    | union (data
        | project SourceId = RequestId, TargetId = Dependency, Calls, Kind = 'Request -> Dependency');
    let nodes = data
    | summarize Calls = sum(Calls) by App
    | project Id = App, Name = App, Calls, Kind = 'App'
    | union (data
        | summarize Calls = sum(Calls) by RequestId, Request
        | project Id = RequestId, Name = Request, Calls, Kind = 'Request')
    | union (data
        | summarize Calls = sum(Calls) by Dependency
        | project Id = Dependency, Name = Dependency, Calls, Kind = 'Dependency');
    nodes
    | union (links)
    ```
7. 將視覺效果設定為**圖形**
8. 按一下 [**圖形設定**] 按鈕以開啟 [設定] 窗格
9. 在底部的 [_版面配置欄位_] 中，設定：
    * `Node Id`: `Id`
    * `Source Id`: `SourceId`
    * `Target Id`: `TargetId`
    * `Edge Label`: `None`
    * `Edge Size`: `Calls`
    * `Node Size`: `None`
    * `Coloring Type`: `Categorical`
    * `Node Color Field`: `Kind`
    * `Color palette`: `Pastel`
10. 在頂端的 [_節點格式_設定] 中，設定：
    * _最上層內容_-使用資料行： `Name`、資料行轉譯器： `Text`
    * _置中內容_-使用資料行： `Calls`、資料行轉譯器： `Big Number`、色調色板： `None`
    * _底端內容_-使用資料行： `Kind`、資料行轉譯器： `Text`
10. 按一下窗格底部的 [_儲存並關閉_] 按鈕。

![磚摘要視圖的螢幕擷取畫面](./media/workbooks-visualizations/graph-settings.png)

## <a name="next-steps"></a>後續步驟

* 使用 Azure Resource Manager[部署](workbooks-automate.md)活頁簿。
* [控制](workbooks-access-control.md)和共用您的活頁簿資源的存取權。
