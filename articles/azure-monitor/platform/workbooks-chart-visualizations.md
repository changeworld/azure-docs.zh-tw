---
title: Azure 監視器活頁簿圖表視覺效果
description: 瞭解所有 Azure 監視器活頁簿圖表視覺效果。
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: 139c8cdb1a227595d728d2acc0b09cf1eb210715
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "90006375"
---
# <a name="chart-visualizations"></a>圖表視覺效果

活頁簿可讓您將監視資料呈現為圖表。 支援的圖表類型包括線條、橫條圖、橫條圖類別、區域、散佈圖、圓形圖和時間。 作者可以選擇自訂圖表的高度、寬度、色彩調色板、圖例、標題、無資料訊息等等，以及使用圖表設定自訂軸類型和數列色彩。

活頁簿支援記錄和度量資料來源的圖表。

## <a name="log-charts"></a>記錄圖表

Azure 監視器記錄會為資源擁有者提供其應用程式和基礎結構運作方式的詳細資訊。 不同于計量，預設不會收集記錄資訊，而且需要某種類型的集合。 不過，當 [顯示記錄] 提供許多有關資源狀態的資訊以及用於診斷的資料。 活頁簿可讓您將記錄資料呈現為視覺效果圖表以進行使用者分析。

### <a name="adding-a-log-chart"></a>加入記錄圖表

下列範例顯示前幾天對應用程式提出要求的趨勢。

1. 選取 [ **編輯** ] 工具列專案，將活頁簿切換至編輯模式。
2. 使用 [ **加入查詢** ] 連結可將記錄查詢控制項加入至活頁簿。
3. 選取 [ **記錄**]、[資源類型] 作為 [查詢類型] (例如 Application Insights) 以及要作為目標的資源。
4. 您可以使用查詢編輯器來輸入分析 (的 [KQL](/azure/kusto/query/) ，例如，要求的趨勢) 。
5. 將視覺效果設定為下列其中一個： **Area**、 **bar**、 **bar (類別) **、 **折線圖**、 **圓形圖**、 **散佈圖**或 **時間**。
6. 如有需要，請設定其他參數，例如時間範圍、視覺效果、大小、調色板和圖例。

[![編輯模式中記錄圖表的螢幕擷取畫面](./media/workbooks-chart-visualizations/log-chart.png)](./media/workbooks-chart-visualizations/log-chart.png#lightbox)

### <a name="log-chart-parameters"></a>記錄圖表參數

| 參數 | 說明 | 範例 |
| ------------- |:-------------|:-------------|
| `Query Type` | 要使用的查詢類型。 | 記錄檔、Azure Resource Graph 等等。 |
| `Resource Type` | 要設為目標的資源類型。 | Application Insights、Log Analytics 或 Azure 優先 |
| `Resources` | 要從中取得計量值的一組資源。 | MyApp1 |
| `Time Range` | 用來查看記錄圖表的時間範圍。 | 過去一小時、過去24小時等等。 |
| `Visualization` | 要使用的視覺效果。 | 區域、橫條圖、折線圖、圓形圖、散佈圖、時間、橫條圖類別 |
| `Size` | 控制項的垂直大小。 | Small、medium、大型或 full |
| `Color palette` | 要在圖表中使用的色調色板。 在多重計量或分段模式中略過。 | 藍色、綠色、紅色等等。 |
| `Legend` | 用於圖例的彙總函式。 | 值的總和或平均值或最大值、最小值、第一個、最後一個值 |
| `Query` | 以圖表視覺效果預期格式傳回資料的任何 KQL 查詢。 | _\|在時間戳記上，要求 ( # A1 預設值 = 0 (1d) 至現在 ( # A5 步驟1h_ |

### <a name="time-series-charts"></a>時間序列圖表

您可以使用活頁簿中的查詢控制項，輕鬆地建立時間序列圖表，例如區域圖、橫條圖、折線圖、散佈圖和時間。 此索引鍵在結果集中具有時間和度量資訊。

#### <a name="simple-time-series"></a>簡單的時間序列

下列查詢會傳回包含兩個數據行的資料表： *timestamp* 和 *要求*。 查詢控制項會使用 X 軸的 *時間戳記* ，以及 Y 軸的 *要求* 。

```kusto
requests
| summarize Requests = count() by bin(timestamp, 1h)
```

[![簡單的時間序列記錄折線圖螢幕擷取畫面。](./media/workbooks-chart-visualizations/log-chart-line-simple.png)](./media/workbooks-chart-visualizations/log-chart-line-simple.png#lightbox)

#### <a name="time-series-with-multiple-metrics"></a>具有多個計量的時間序列

下列查詢會傳回包含三個數據行的資料表： *時間戳記*、 *要求*和 *使用者*。 查詢控制項會使用 X 軸的*時間戳記*，並*要求*  &  *使用者*在 Y 軸上作為個別數列。

```kusto
requests
| summarize Requests = count(), Users = dcount(user_Id) by bin(timestamp, 1h)
```

[![具有多個計量記錄折線圖的時間序列螢幕擷取畫面。](./media/workbooks-chart-visualizations/log-chart-line-multi-metric.png)](./media/workbooks-chart-visualizations/log-chart-line-multi-metric.png#lightbox)

#### <a name="segmented-time-series"></a>分段的時間序列

下列查詢會傳回包含三個數據行的資料表： *timestamp*、 *Requests*和 *myrequest* ，其中 *myrequest* 是具有要求名稱的類別資料行。 此處的查詢控制項會使用 X 軸的 *時間戳記* ，並為每個 *myrequest*值新增一個數列。

```
requests
| summarize Request = count() by bin(timestamp, 1h), RequestName = name
```

[![分割的時間序列記錄行圖表的螢幕擷取畫面。](./media/workbooks-chart-visualizations/log-chart-line-segmented.png)](./media/workbooks-chart-visualizations/log-chart-line-segmented.png#lightbox)

### <a name="summarize-vs-make-series"></a>總結與 make 系列

上一節中的範例會使用 `summarize` 運算子，因為它比較容易瞭解。 不過，摘要的確有一個主要的限制，因為值區中沒有任何專案時，它會省略結果資料列。 根據空白值區是否在時間範圍的正面或後端而定，它可能會產生移動圖表時間範圍的效果。

通常最好是使用 `make-series` 運算子來建立時間序列資料，此資料可提供空白值區之預設值的選項。

下列查詢會使用 `make-series` 運算子。

```kusto
requests
| make-series Requests = count() default = 0 on timestamp from ago(1d) to now() step 1h by RequestName = name
```

下列查詢顯示與運算子類似的圖表 `summarize`

```kusto
requests
| summarize Request = count() by bin(timestamp, 1h), RequestName = name
```

即使基礎結果集不同也是一樣。 使用者必須做的就是將視覺效果設定為 [區域]、[折線圖]、[橫條圖] 或 [時間]，而活頁簿將會負責處理其餘部分。

[![從「製作系列」查詢進行之記錄行圖表的螢幕擷取畫面](./media/workbooks-chart-visualizations/log-chart-line-make-series.png)](./media/workbooks-chart-visualizations/log-chart-line-make-series.png#lightbox)

### <a name="categorical-bar-chart-or-histogram"></a>類別橫條圖或長條圖

類別圖表可讓使用者在圖表的 X 軸上表示維度或資料行，這在長條圖中特別有用。 下列範例顯示要求的散發結果碼。

```kusto
requests
| summarize Requests = count() by Result = strcat('Http ', resultCode)
| order by Requests desc
```

此查詢會傳回兩個數據行： *要求* 度量和 *結果* 分類。 *結果*資料行的每個值會在圖表中取得自己的資料列，且其高度與*要求度量*成正比。

[![結果碼要求的類別目錄橫條圖螢幕擷取畫面](./media/workbooks-chart-visualizations/log-chart-categorical-bar.png)](./media/workbooks-chart-visualizations/log-chart-categorical-bar.png#lightbox)

### <a name="pie-charts"></a>圓形圖

圓形圖允許數值比例的視覺效果。 下列範例顯示其結果碼的要求比例。

```kusto
requests
| summarize Requests = count() by Result = strcat('Http ', resultCode)
| order by Requests desc
```

此查詢會傳回兩個數據行： *要求* 度量和 *結果* 分類。 *結果*資料行的每個值會在圓形圖中取得自己的配量，且大小與*要求*度量成正比。

[![圓形圖的螢幕擷取畫面，其中包含代表結果碼的配量](./media/workbooks-chart-visualizations/log-chart-pie-chart.png)](./media/workbooks-chart-visualizations/log-chart-pie-chart.png#lightbox)

## <a name="metric-charts"></a>度量圖表

大部分的 Azure 資源都會發出有關狀態和健全狀況的計量資料 (例如，CPU 使用率、儲存體可用性、資料庫交易計數、失敗的應用程式要求等 ) 。 活頁簿可將此資料視覺化為時間序列圖表。 ) 

### <a name="adding-a-metric-chart"></a>新增度量圖表

下列範例會顯示過去一小時內儲存體帳戶中的交易數目。 這可讓儲存體擁有者查看交易趨勢，並尋找行為中的異常。

1. 選取 [ **編輯** ] 工具列專案，將活頁簿切換至編輯模式。
2. 使用 [ **新增度量** ] 連結，將計量控制項新增至活頁簿。
3. 選取資源類型 (例如儲存體帳戶) 、要設為目標的資源、計量命名空間和名稱，以及要使用的匯總。
4. 如有需要，請視需要設定其他參數-例如，分割、視覺效果、大小和色彩調色板。

[![編輯模式中度量圖表的螢幕擷取畫面](./media/workbooks-chart-visualizations/metric-chart.png)](./media/workbooks-chart-visualizations/metric-chart.png#lightbox)

### <a name="metric-chart-parameters"></a>度量圖表參數

| 參數 | 說明 | 範例 |
| ------------- |:-------------|:-------------|
| `Resource Type` | 要設為目標的資源類型。 | 儲存體或虛擬機器。 |
| `Resources` | 要從中取得計量值的一組資源。 | MyStorage1 |
| `Namespace` | 具有度量的命名空間。 | 儲存體 > Blob |
| `Metric` | 要視覺化的度量。 | 儲存體 > Blob > 交易 |
| `Aggregation` | 要套用至度量的彙總函式。 | 總和、計數、平均等等。 |
| `Time Range` | 要在其中查看度量的時間範圍。 | 過去一小時、過去24小時等等。 |
| `Visualization` | 要使用的視覺效果。 | 區域圖、橫條圖、折線圖、散佈圖、格線 |
| `Split By` | 選擇性地分割維度上的度量。 | 依地理類型的交易 |
| `Size` | 控制項的垂直大小。 | Small、medium 或大型 |
| `Color palette` | 要在圖表中使用的色調色板。 如果 `Split by` 使用參數，則會忽略。 | 藍色、綠色、紅色等等。 |

### <a name="examples"></a>範例

依 API 名稱分割為折線圖的交易：

[![依 API 名稱分割之儲存體交易的度量折線圖螢幕擷取畫面](./media/workbooks-chart-visualizations/metric-chart-storage-split-line.png)](./media/workbooks-chart-visualizations/metric-chart-storage-split-line.png#lightbox)

依回應類型分割的交易是大型橫條圖：

[![依回應類型分割之儲存體交易的大型度量橫條圖螢幕擷取畫面](./media/workbooks-chart-visualizations/metric-chart-storage-bar-large.png)](./media/workbooks-chart-visualizations/metric-chart-storage-bar-large.png#lightbox)

平均延遲為散佈圖：

[![儲存體延遲的度量散佈圖螢幕擷取畫面](./media/workbooks-chart-visualizations/metric-chart-storage-scatter.png)](./media/workbooks-chart-visualizations/metric-chart-storage-scatter.png#lightbox)

## <a name="chart-settings"></a>圖表設定

作者可以使用圖表設定來自訂哪些欄位用於圖表軸、軸單位、自訂格式、範圍、群組行為、圖例和數列色彩。

### <a name="the-settings-tab"></a>[SETTINGS] \(設定\) 索引標籤

[設定] 索引標籤會控制：

- 座標軸設定，包括哪些欄位、自訂格式，可讓使用者將數位格式設定為軸值和自訂範圍。
- 群組設定，包括在建立「其他」群組之前的限制。
- 圖例設定，包括顯示 (系列名稱、色彩和數位) 底部的度量，以及/或圖例 (序列名稱和色彩) 。

![圖表設定的螢幕擷取畫面。](./media/workbooks-chart-visualizations/chart-settings.png)

#### <a name="custom-formatting"></a>自訂格式

數位格式化選項包括：

| 格式化選項             | 說明                                                                                           |
|:---------------------------- |:-------------------------------------------------------------------------------------------------------|
| `Units`                      | 適用于百分比、計數、時間、位元組、計數/時間、位元組/時間等各資料行各種選項的單位。例如，值為1234的單位可以設定為毫秒，並且會轉譯為1.234 四捨五入 s。                                  |
| `Style`                      | 以-decimal、currency、percent 格式呈現的格式。                                               |
| `Show group separator`       | 核取方塊以顯示群組分隔符號。 將1234轉譯為美國的1234。                                    |
| `Minimum integer digits`     | 要使用 (預設值 1) 的最小整數位數。                                                   |
| `Minimum fractional digits`  | 要使用 (預設值為 0) 的最小小數位數。                                                |
| `Maximum fractional digits`  | 要使用的小數位數上限。                                                            |
| `Minimum significant digits` | 使用 (預設值 1) 的有效位數下限。                                               |
| `Maximum significant digits` | 要使用之有效位數的最大數目。                                                           |

![X 軸設定的螢幕擷取畫面。](./media/workbooks-chart-visualizations/number-format-settings.png)

### <a name="the-series-tab"></a>數列索引標籤

[數列設定] 索引標籤可讓您調整圖表中的數列所顯示的標籤和色彩。

- `Series name`欄位是用來比對資料中的數列，如果相符的話，就會顯示顯示標籤和色彩。
- `Comment`欄位對範本作者很有用，因為翻譯人員可能會使用這個批註來當地語系化顯示標籤。

![數列設定的螢幕擷取畫面。](./media/workbooks-chart-visualizations/series-settings.png)

## <a name="next-steps"></a>接下來的步驟

- 瞭解如何 [在活頁簿中建立磚](workbooks-tile-visualizations.md)。
- 瞭解如何建立 [互動式活頁簿](workbooks-interactive.md)。