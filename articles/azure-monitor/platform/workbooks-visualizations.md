---
title: Azure 監視器活頁簿視覺化效果
description: 瞭解所有 Azure 監視器活頁簿視覺化元件，包括 - 文本、圖表、網格、樹和圖形。
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: d05902f47dff3dd2f8a63ae240c0b8825a5c441f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658025"
---
# <a name="azure-monitor-workbook-visualizations"></a>Azure 監視器活頁簿視覺化效果

Azure 監視器活頁簿支援多種不同樣式的視覺化，以滿足您的報告需求。 本文提供了每種視覺化類型的示例。

## <a name="text"></a>Text

活頁簿允許作者在其活頁簿中包含文字區塊。 文本可以是遙測的人工分析、説明使用者解釋資料的資訊、節標題等。

![Apdex 文本表的螢幕截圖](./media/workbooks-visualizations/apdex.png)

文本通過標記控制項添加，該控制項提供完整的格式控制。

![生成呈現表的原始標記的螢幕截圖](./media/workbooks-visualizations/markdown.png)

### <a name="add-a-text-control"></a>添加文本控制項

1. 通過按一下 **"編輯**"工具列項，將活頁簿切換到編輯模式。
2. 使用 **"添加文本"** 連結將文本控制項添加到活頁簿。
3. 將標記添加到控制項。
4. 按一下"**完成編輯"** 按鈕以查看格式化的文本。

> [!TIP]
> 使用此[標記備忘單](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet)瞭解不同的格式選項。

## <a name="charts"></a>圖表

活頁簿允許將監視資料作為圖表顯示。 支援的圖表類型包括線、橫條圖、條形線、區域、散佈圖、圓形圖和時間。 作者可以選擇自訂圖表的高度、寬度、調色板、圖例、標題、無資料消息等。

活頁簿支援日誌和指標資料來源的圖表。 

### <a name="adding-a-log-chart"></a>添加日誌圖表

1. 通過按一下 **"編輯**"工具列項，將活頁簿切換到編輯模式。
2. 使用 **"添加"查詢**連結將日誌查詢控制項添加到活頁簿。
3. 選取查詢類型作為**日誌**、資源類型（例如，應用程式見解）和目標資源。
4. 使用查詢編輯器輸入[KQL](https://docs.microsoft.com/azure/kusto/query/)進行分析（例如，請求趨勢）。
5. 將視覺化設置為：**區域**、**條形**圖、**條形（分類）、****線**、**圓形圖**、**散點**或**時間**。
6. 根據需要設置其他參數 - 如時間範圍、視覺化效果、大小、調色板和圖例。

![編輯模式下日誌圖表的螢幕截圖](./media/workbooks-visualizations/log-chart.png)

#### <a name="log-chart-parameters"></a>日誌圖表參數

| 參數 | 說明 | 範例 |
| ------------- |:-------------|:-------------|
| `Query Type` | 要使用的查詢類型 | 日誌、Azure 資源圖等 |
| `Resource Type` | 要定位的資源類型 | 應用程式見解、日誌分析或 Azure 優先 |
| `Resources` | 一組資源，用於從 | MyApp1 |
| `Time Range` | 查看日誌圖表的時間視窗 | 最後一小時，最後24小時等 |
| `Visualization` | 要使用的視覺化效果 | 區域， 條形， 線， 圓形圖， 散點， 時間， 條分類 |
| `Size` | 控制項的垂直大小 | 小、中、大或滿 |
| `Color palette` | 要在圖表中使用的調色板。 在多度量或分段模式下忽略。 | 藍色、綠色、紅色等 |
| `Legend` | 用於圖例的彙總函式 | 值或最大值的總和或平均值，最小值，第一個值，最後值 |
| `Query` | 以圖表視覺化預期格式返回資料的任何 KQL 查詢 | _請求\|使序列 請求 = 計數（） 預設值 = 從之前（1d）到現在（） 步驟 1h 的時間戳記上的 0_ |

### <a name="adding-a-metric-chart"></a>添加指標圖表

1. 通過按一下 **"編輯**"工具列項，將活頁簿切換到編輯模式。
2. 使用 **"添加指標"** 連結將指標控制項添加到活頁簿。
3. 選擇資源類型（例如，存儲帳戶）、要定位的資源、指標命名空間和名稱以及要使用的聚合。
4. 根據需要設置其他參數 - 如時間範圍、拆分、視覺化、大小和調色板。

![編輯模式下指標圖表的螢幕截圖](./media/workbooks-visualizations/metric-chart.png)

#### <a name="metric-chart-parameters"></a>指標圖表參數

| 參數 | 說明 | 範例 |
| ------------- |:-------------|:-------------|
| `Resource Type` | 要定位的資源類型 | 存儲或虛擬機器。 |
| `Resources` | 一組資源，用於從 | 我的存儲1 |
| `Namespace` | 帶有指標的命名空間 | 存儲> Blob |
| `Metric` | 要視覺化的指標 | 存儲> blob >事務 |
| `Aggregation` | 要應用於指標的彙總函式 | 總和、計數、平均值等 |
| `Time Range` | 查看指標的時間視窗 | 最後一小時，最後24小時等 |
| `Visualization` | 要使用的視覺化效果 | 面積， 條形， 線， 散點， 網格 |
| `Split By` | 可以選擇拆分維度上的指標 | 按地理位置類型的交易記錄 |
| `Size` | 控制項的垂直大小 | 小、中或大 |
| `Color palette` | 要在圖表中使用的調色板。 如果使用參數，`Split by`則忽略 | 藍色、綠色、紅色等 |

## <a name="grids"></a>方格

網格或表是向使用者顯示資料的常見方法。 活頁簿允許使用者單獨設置網格列的樣式，為其報表提供豐富的 UI。

下面的示例顯示了一個網格，該網格結合了圖示、熱圖和火花柱來顯示覆雜的資訊。 活頁簿還提供排序、搜索框和轉到分析按鈕。

![基於日誌的網格螢幕截圖](./media/workbooks-visualizations/grid.png)

### <a name="adding-a-log-based-grid"></a>添加基於日誌的網格

1. 通過按一下 **"編輯**"工具列項，將活頁簿切換到編輯模式。
2. 使用 **"添加"查詢**連結將日誌查詢控制項添加到活頁簿。
3. 選取查詢類型作為**日誌**、資源類型（例如，應用程式見解）和目標資源。
4. 使用查詢編輯器輸入 KQL 進行分析（例如，記憶體低於閾值的 VM）
5. 將視覺化設置為**網格**
6. 如果需要，設置其他參數 - 如時間範圍、大小、調色板和圖例。

![基於日誌的網格查詢螢幕截圖](./media/workbooks-visualizations/grid-query.png)

## <a name="tiles"></a>圖格

磁貼是在活頁簿中顯示摘要資料的非常有用的方法。 下圖顯示了磁貼的常見用例 - 詳細網格之上的應用級別摘要。

![磁貼摘要視圖的螢幕截圖](./media/workbooks-visualizations/tiles-summary.png)

活頁簿磁貼支援顯示標題、字幕、大文本、圖示、基於指標的漸變、火花線/條形、頁腳等。

### <a name="adding-a-tile"></a>添加磁貼

1. 通過按一下 _"編輯_"工具列項，將活頁簿切換到編輯模式。
2. 使用 **"添加"查詢**連結將日誌查詢控制項添加到活頁簿。 
3. 選取查詢類型作為**日誌**、資源類型（例如，應用程式見解）和目標資源。
4. 使用查詢編輯器輸入 KQL 進行分析
    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | top 7 by Requests desc
    ```
5. 將大小設置為 **"完整"**
6. 將視覺化設置為**磁貼**
7. 按一下"**磁貼設置"** 按鈕以打開"設置"窗格
8. 在**磁貼欄位**中 ，設置：
    * 標題：`name`
    * 左： `Requests`、 渲`Big Number`染器： `Green to Red`、 調色板： 、 最小值：`0`
    * 底部：`appName`
9. 按一下窗格底部的 **"保存和關閉**"按鈕。

![磁貼摘要視圖的螢幕截圖](./media/workbooks-visualizations/tile-settings.png)

這是切片在讀取模式下的外觀：

![磁貼摘要視圖的螢幕截圖](./media/workbooks-visualizations/tiles-read-mode.png)

## <a name="trees"></a>樹木

活頁簿通過樹網格支援分層視圖。 樹允許某些行可擴展到下一個級別，以便進行向下切入體驗。

下面的示例顯示了視覺化為樹網格的容器運行狀況指標（工作集大小）。 此處的頂級節點是 Azure Kubernetes 服務 （AKS） 節點，下一級是 pod，最終級別是容器。 請注意，您仍然可以像網格一樣設置列的格式（熱圖、圖示、連結）。 在這種情況下，基礎資料來源是具有 AKS 日誌的日誌分析工作區。

![磁貼摘要視圖的螢幕截圖](./media/workbooks-visualizations/trees.png)

### <a name="adding-a-tree-grid"></a>添加樹網格
1. 通過按一下 _"編輯_"工具列項，將活頁簿切換到編輯模式。
2. 使用 **"添加"查詢**連結將日誌查詢控制項添加到活頁簿。 
3. 選取查詢類型作為**日誌**、資源類型（例如，應用程式見解）和目標資源。
4. 使用查詢編輯器輸入 KQL 進行分析
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
5. 將視覺化設置為**網格**
6. 按一下"**列設置"** 按鈕以打開設置窗格
7. 在底部的 **"樹/分組按設置"** 部分中，設置：
    * 樹類型：`Parent/Child`
    * ID 欄位：`Id`
    * 父 ID 欄位：`ParentId`
    * 在以下方面顯示擴展器：`Name`
    * 展開樹的頂層：`checked`
8. 在頂部的 _"列"_ 部分中，設置：
    * _Id_ - 列渲染器：`Hidden`
    * _父 Id_ - 列呈現器：`Hidden`
    * _請求_- 列渲`Bar`染器： `Blue`， 顏色： ， 最小值：`0`
9. 按一下窗格底部的 _"保存和關閉_"按鈕。    

![磁貼摘要視圖的螢幕截圖](./media/workbooks-visualizations/tree-settings.png)

### <a name="tree-settings"></a>樹設置

| 設定 | 說明 |
|:------------- |:-------------|
| `Id Field` | 網格中每行的唯一 ID |
| `Parent Id Field` | 當前行的父項的 ID |
| `Show the expander on` | 要顯示樹擴展器的列。 樹網格通常隱藏其 ID 和父 ID 欄位，因為它們不太可讀。 相反，擴展程式出現在具有更可讀值的欄位中 - 類似于實體的名稱 |
| `Expand the top level of the tree` | 如果選中，樹網格將在頂層展開。 預設情況下，如果要顯示更多資訊，則非常有用 |

## <a name="graphs"></a>圖表

活頁簿支援基於日誌資料視覺化任意圖形，以顯示監視實體之間的關係。

下圖顯示了通過各種埠進出外部電腦的電腦進出的電腦的資料。 它按類型著色（電腦與埠與外部 IP），邊緣大小對應于之間流動的資料量。 基礎資料來自針對 VM 連接的 KQL 查詢。

![磁貼摘要視圖的螢幕截圖](./media/workbooks-visualizations/graph.png)

### <a name="adding-a-graph"></a>添加圖形
1. 通過按一下 _"編輯_"工具列項，將活頁簿切換到編輯模式。
2. 使用 **"添加"查詢**連結將日誌查詢控制項添加到活頁簿。 
3. 選取查詢類型作為**日誌**、資源類型（例如，應用程式見解）和目標資源。
4. 使用查詢編輯器輸入 KQL 進行分析
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
7. 將視覺化設置為**圖形**
8. 按一下"**圖形設置"** 按鈕以打開設置窗格
9. 在底部的_佈局欄位中_，設置：
    * `Node Id`: `Id`
    * `Source Id`: `SourceId`
    * `Target Id`: `TargetId`
    * `Edge Label`: `None`
    * `Edge Size`: `Calls`
    * `Node Size`: `None`
    * `Coloring Type`: `Categorical`
    * `Node Color Field`: `Kind`
    * `Color palette`: `Pastel`
10. 在頂部的_節點格式設置_中，設置：
    * _熱門內容_- 使用`Name`列： 、 列呈現器：`Text`
    * _中心內容_- 使用`Calls`列： 、`Big Number`列渲染器： 、 調色板：`None`
    * _底部內容_- 使用`Kind`列： ， 列呈現器：`Text`
10. 按一下窗格底部的 _"保存和關閉_"按鈕。

![磁貼摘要視圖的螢幕截圖](./media/workbooks-visualizations/graph-settings.png)

## <a name="next-steps"></a>後續步驟

* [使用](workbooks-automate.md)Azure 資源管理器部署活頁簿。
* [控制和](workbooks-access-control.md)共用對活頁簿資源的存取權限。
