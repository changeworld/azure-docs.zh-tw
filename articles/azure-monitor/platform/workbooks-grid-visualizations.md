---
title: Azure 監視器活頁簿格線視覺效果
description: 瞭解所有 Azure 監視器活頁簿格線視覺效果。
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: 114245030ba2356861b83a580f103d3b84c0eae3
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89663936"
---
# <a name="grid-visualizations"></a>方格視覺效果

方格或資料表是向使用者呈現資料的常見方式。 活頁簿可讓使用者個別將方格的資料行樣式，為其報表提供豐富的 UI。

下列範例顯示的方格會結合圖示、熱度圖和 spark 橫條來呈現複雜的資訊。 活頁簿也提供排序、搜尋方塊和進入分析按鈕。

[![以記錄為基礎之方格的螢幕擷取畫面](./media/workbooks-grid-visualizations/grid.png)](./media/workbooks-grid-visualizations/grid.png#lightbox)

## <a name="adding-a-log-based-grid"></a>加入以記錄為基礎的方格

1. 按一下 [ **編輯** ] 工具列專案，將活頁簿切換至編輯模式。
2. 使用 [ **加入查詢** ] 連結可將記錄查詢控制項加入至活頁簿。
3. 選取 [ **記錄**]、[資源類型] 作為 [查詢類型] (例如 Application Insights) 以及要作為目標的資源。
4. 使用查詢編輯器來輸入分析 (的 KQL 例如，記憶體低於閾值的 Vm) 
5. 將視覺效果設定為 **方格**
6. 如有需要，請設定其他參數，例如時間範圍、大小、調色板和圖例。

[![以記錄為基礎的方格查詢螢幕擷取畫面](./media/workbooks-grid-visualizations/grid-query.png)](./media/workbooks-grid-visualizations/grid-query.png#lightbox)

## <a name="log-chart-parameters"></a>記錄圖表參數

| 參數 | 說明 | 範例 |
| ------------- |:-------------|:-------------|
| `Query Type` | 要使用的查詢類型。 | 記錄檔、Azure Resource Graph 等等。 |
| `Resource Type` | 要設為目標的資源類型。 | Application Insights、Log Analytics 或 Azure 優先 |
| `Resources` | 要從中取得計量值的一組資源。 | MyApp1 |
| `Time Range` | 用來查看記錄圖表的時間範圍。 | 過去一小時、過去24小時等等。 |
| `Visualization` | 要使用的視覺效果。 | 方格 |
| `Size` | 控制項的垂直大小。 | Small、medium、大型或 full |
| `Query` | 以圖表視覺效果預期格式傳回資料的任何 KQL 查詢。 | _要求 \| 摘要要求 = 計數 ( # A1 依名稱_ |

## <a name="simple-grid"></a>簡單方格

活頁簿可以將 KQL 結果轉譯為簡單的資料表。 下列方格顯示應用程式中每個要求類型的要求計數和唯一使用者。

```kusto
requests
| where name !endswith('.eot')
| summarize Requests = count(), Users = dcount(user_Id) by name
| order by Requests desc
```

[![編輯模式中以記錄為基礎之方格的螢幕擷取畫面](./media/workbooks-grid-visualizations/log-chart-simple-grid.png)](./media/workbooks-grid-visualizations/log-chart-simple-grid.png#lightbox)

## <a name="grid-styling"></a>方格樣式

雖然純資料表會顯示資料，但很難閱讀，且深入解析不會永遠明顯。 設定格線樣式有助於讓您更輕鬆地讀取和解讀資料。

以下是上一節中樣式為熱度圖的相同方格。

[![記錄式方格的螢幕擷取畫面，其中已將資料行樣式為熱度圖](./media/workbooks-grid-visualizations/log-chart-grid-heatmap.png)](./media/workbooks-grid-visualizations/log-chart-grid-heatmap.png#lightbox)

以下是以橫條圖樣式的相同方格：以[ ![ 記錄為基礎之方格的螢幕擷取畫面，其中的資料行樣式為橫條圖](./media/workbooks-grid-visualizations/log-chart-grid-bar.png)](./media/workbooks-grid-visualizations/log-chart-grid-bar.png#lightbox)

### <a name="styling-a-grid-column"></a>設定格線欄的樣式

1. 選取 [查詢控制] 工具列上的 [資料 **行設定** ] 按鈕。
2. 在 [ *編輯資料行設定*] 中，選取要設定樣式的資料行。
3. 選擇資料行轉譯器 (例如，[熱度圖]、[橫條圖]、[下]、[列] 等等，) 以及設定資料行樣式的相關設定

以下範例會將 *要求* 資料行的樣式建立為橫條：

[![以記錄為基礎之方格的螢幕擷取畫面，其中已將要求資料行樣式為橫條圖。](./media/workbooks-grid-visualizations/log-chart-grid-column-settings-start.png)](./media/workbooks-grid-visualizations/log-chart-grid-column-settings-start.png#lightbox)

### <a name="column-renderers"></a>資料行轉譯器

| 資料行轉譯器 | 說明 | 其他選項 |
|:------------- |:-------------|:-------------|
| `Automatic` | 預設值-根據資料行類型，使用最適當的轉譯器。  |  |
| `Text` | 將資料行值轉譯為文字。 | |
| `Right Aligned` | 類似于文字，但它是靠右對齊。 | |
| `Date/Time` | 呈現可讀取的日期時間字串。 | |
| `Heatmap` | 根據資料格的值來為方格儲存格建立色彩。 | 色階和最小/最大值（用於調整）。 |
| `Bar` | 依據儲存格的值，在資料格旁邊呈現橫條。 | 色階和最小/最大值（用於調整）。 |
| `Bar underneath` | 依據儲存格的值，在接近資料格底部的周圍呈現橫條。 | 色階和最小/最大值（用於調整）。 |
| `Composite bar` | 使用該資料列中的指定資料行，呈現複合列。 請參閱 [複合](workbooks-composite-bar.md) 列以取得詳細資料。 | 具有對應色彩可轉譯橫條圖的資料行，以及列頂端顯示的標籤。 |
| `Spark bars` | 根據資料格中的動態陣列值，在資料格中呈現 spark 列。 例如，來自頂端螢幕擷取畫面的趨勢資料行。 | 色階和最小/最大值（用於調整）。 |
| `Spark lines` | 根據資料格中的動態陣列值，在資料格中呈現 spark 行。 | 色階和最小/最大值（用於調整）。 |
| `Icon` | 根據資料格中的文字值呈現圖示。 支援的值包括： `cancelled` 、 `critical` 、 `disabled` 、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、 `error` `failed` `info` `none` `pending` `stopped` `question` `success` `unknown` `warning` `uninitialized` `resource` `up` `down` `left` `right` `trendup` `trenddown` `4` `3` `2` `1` `Sev0` `Sev1` `Sev2` `Sev3` `Sev4` `Fired` `Resolved` `Available` `Unavailable` `Degraded` `Unknown` 和 `Blank` 。|  |
| `Link` | 轉譯按一下或執行可設定動作時的連結。 如果您 *只* 想要讓專案成為連結，請使用此選項。  任何其他類型 *也* 可以使用設定來作為連結 `Make this item a link` 。 如需詳細資訊，請參閱下列 [連結動作](#link-actions) 。 |  |
| `Location` | 根據區域識別碼呈現易記的 Azure 區功能變數名稱稱。 |  |
| `Resource type` | 根據資源類型識別碼呈現易記的資源類型字串  |  |
| `Resource` | 根據資源識別碼呈現易記的資源名稱和連結  | 顯示資源類型圖示的選項  |
| `Resource group` | 根據資源群組識別碼呈現易記的資源組名和連結。如果儲存格的值不是資源群組，則會轉換成一個資源群組。  | 顯示資源群組圖示的選項  |
| `Subscription` | 根據訂用帳戶識別碼，轉譯易記的訂用帳戶名稱和連結。 如果資料格的值不是訂用帳戶，則會將它轉換成一個。  | 顯示訂用帳戶圖示的選項。  |
| `Hidden` | 隱藏方格中的資料行。 當預設查詢傳回的資料行數超過所需，但不想要專案離開時，會很有用 |  |

### <a name="link-actions"></a>連結動作

如果 `Link` 已選取轉譯器或選取 *[將此專案設為連結* ] 核取方塊，則作者可以設定在選取資料格時所發生的連結動作。 這通常會讓使用者前往某個資料格內容的其他觀點，或可能會開啟 url。

### <a name="custom-formatting"></a>自訂格式

活頁簿也可讓使用者設定其儲存格值的數位格式。 若要這樣做，請按一下 [ *自訂格式* ] 核取方塊（如果有的話）。

| 格式化選項 | 說明 |
|:------------- |:-------------|
| `Units` | 適用于百分比、計數、時間、位元組、計數/時間、位元組/時間等各資料行各種選項的單位。例如，值為1234的單位可以設定為毫秒，並且會轉譯為 1.234 s。 |
| `Style` | 以-decimal、currency、percent 格式呈現的格式。 |
| `Show group separator` | 核取方塊以顯示群組分隔符號。 將1234轉譯為美國的1234。 |
| `Minimum integer digits` | 要使用 (預設值 1) 的最小整數位數。 |
| `Minimum fractional digits` | 要使用 (預設值為 0) 的最小小數位數。  |
| `Maximum fractional digits` | 要使用的小數位數上限。 |
| `Minimum significant digits` | 使用 (預設值 1) 的有效位數下限。 |
| `Maximum significant digits` | 要使用之有效位數的最大數目。 |
| `Custom text for missing values` | 如果資料點沒有值，請顯示此自訂文字，而非空白。 |

### <a name="custom-date-formatting"></a>自訂日期格式

當作者指定將資料行設定為日期/時間轉譯器時，作者可以使用 *自訂日期格式* 設定核取方塊來指定自訂日期格式選項。

| 格式化選項 | 說明 |
|:------------- |:-------------|
| `Style` | 將日期轉譯為簡短、完整格式或時間格式的日期格式。 |
| `Show time as` | 允許作者決定以當地時間顯示 (預設) 的時間，或以 UTC 表示。 視選取的日期格式樣式而定，可能不會顯示 UTC/時區資訊。 |

## <a name="custom-column-width-setting"></a>自訂資料行寬度設定

作者可以使用 [資料*行設定*] 中的 [*自訂資料行寬度*] 欄位，自訂方格中任何資料行的寬度。

![使用紅色方塊指出的自訂資料行寬度欄位，顯示資料行設定的螢幕擷取畫面](./media/workbooks-grid-visualizations/custom-column-width-setting.png)

如果欄位是黑色，則會根據資料行中的字元數和可見的資料行數目，自動決定寬度。 預設單位為 "ch" (字元) 。

選取標籤中的藍色 ** (目前寬度) ** 按鈕，就會在文字欄位中填入所選資料行的目前寬度。 如果值出現在 [自訂寬度] 欄位中，但沒有測量單位，則會使用預設值。

可用的度量單位為：

| 測量單位 | 定義           |
|:--------------------|:---------------------|
| ch                  |  (預設) 的字元 |
| px                  | 像素               |
| fr                  | 分數單位     |
| %                   | percentage           |

輸入驗證-如果驗證失敗，就會在欄位下方顯示紅色指引訊息，但使用者仍然可以套用寬度。 如果輸入中有某個值，則會將其剖析。如果找不到有效的測量單位，則會使用預設值。

沒有最小/最大寬度，因為這會留給作者的決定。 隱藏資料行的自訂資料行寬度欄位已停用。

## <a name="examples"></a>範例

### <a name="spark-lines-and-bar-underneath"></a>下方的 Spark 行和橫條

下列範例顯示要求計數及其依要求名稱的趨勢。

```kusto
requests
| make-series Trend = count() default = 0 on timestamp from ago(1d) to now() step 1h by name
| project-away timestamp
| join kind = inner (requests
    | summarize Requests = count() by name
    ) on name
| project name, Requests, Trend
| order by Requests desc
```

[![以記錄為基礎之方格的螢幕擷取畫面，其中包含下方的橫條和 spark 線](./media/workbooks-grid-visualizations/log-chart-grid-spark-line.png)](./media/workbooks-grid-visualizations/log-chart-grid-spark-line.png#lightbox)

### <a name="heatmap-with-shared-scales-and-custom-formatting"></a>具有共用規模和自訂格式的熱度圖

此範例顯示各種要求持續時間計量和計數。 熱度圖轉譯器會使用設定中設定的最小值，或計算資料行的最小值和最大值，並根據資料格的值（相對於資料行的最小值和最大值），為數據格的選定調色板指派背景色彩。

```
requests
| summarize Mean = avg(duration), (Median, p80, p95, p99) = percentiles(duration, 50, 80, 95, 99), Requests = count() by name
| order by Requests desc
```

[![以記錄為基礎之方格的螢幕擷取畫面，其中熱度圖具有跨資料行的共用比例](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale.png)](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale.png#lightbox)

在上述範例中，共用的調色板 (綠色或紅色) 和小數位數會用來將資料行 (mean、中位數、p80、p95 和 p99) 的色彩。 不同的調色板 (blue) 用於 [要求] 資料行。

#### <a name="shared-scale"></a>共用規模

若要取得共用規模：

1. 使用正則運算式來選取要套用設定的一個以上的資料行。 例如，將 [資料行名稱] 設定為 [ `Mean|Median|p80|p95|p99` 全部選取]。
2. 刪除個別資料行的預設設定。

這會導致新的多欄設定套用其設定，以包含共用規模。

[![以記錄為基礎之方格設定的螢幕擷取畫面，以取得跨資料行的共用刻度](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale-settings.png)](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale-settings.png#lightbox)

### <a name="icons-to-represent-status"></a>代表狀態的圖示

下列範例會根據 p95 持續期間，顯示要求的自訂狀態。

```
requests
| summarize p95 = percentile(duration, 95) by name
| order by p95 desc
| project Status = case(p95 > 5000, 'critical', p95 > 1000, 'error', 'success'), name, p95
```

[![以記錄為基礎之方格的螢幕擷取畫面，其中熱度圖具有使用上述查詢的跨資料行進行共用調整。](./media/workbooks-grid-visualizations/log-chart-grid-icons.png)](./media/workbooks-grid-visualizations/log-chart-grid-icons.png#lightbox)

支援的圖示名稱包括： `cancelled` 、 `critical` 、 `disabled` 、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、 `error` `failed` `info` `none` `pending` `stopped` `question` `success` `unknown` `warning` `uninitialized` `resource` `up` `down` `left` `right` `trendup` `trenddown` `4` `3` `2` `1` `Sev0` `Sev1` `Sev2` `Sev3` `Sev4` `Fired` `Resolved` `Available` `Unavailable` `Degraded` `Unknown` 和 `Blank` 。

### <a name="using-thresholds-with-links"></a>搭配使用臨界值與連結

下列指示將說明如何搭配使用臨界值與連結來指派圖示及開啟不同的活頁簿。 格線中的每個連結都會開啟該 Application Insights 資源的不同活頁簿範本。

1. 選取 [ *編輯* ] 工具列專案，將活頁簿切換至編輯模式。
2. 選取 [ **加入** ]，然後按一下 [ *加入查詢*]。
3. 將 *資料來源* 變更為 "JSON"，並將 *視覺效果* 變更為「方格」。
4. 輸入下列查詢。

```json
[ 
    { "name": "warning", "link": "Community-Workbooks/Performance/Performance Counter Analysis" },
    { "name": "info", "link": "Community-Workbooks/Performance/Performance Insights" },
    { "name": "error", "link": "Community-Workbooks/Performance/Apdex" }
]
```

5. 執行查詢。
6. 選取 [資料 **行設定** ] 以開啟設定。
7. 從資料 *行*中選取 [名稱]。
8. 在 [資料 *行*轉譯器] 下，選擇 [臨界值]。
9.  輸入並選擇下列 *閾值設定*。
   
    | 運算子 | 值   | 圖示   |
    |----------|---------|---------|
    | ==       | warning | 警告 |
    | ==       | error   | Failed  |

    ![使用上述設定的 [編輯資料行設定] 索引標籤的螢幕擷取畫面。](./media/workbooks-grid-visualizations/column-settings.png)

    保持預設資料列的狀態。 您可以輸入任何您想要的文字。 文字資料行採用字串格式做為輸入，並在指定時填入資料行值和單位。 例如，如果 [警告] 是資料行值，則文字可以是 " {0} {1} link！"，它會顯示為「警告連結！」。
10. 選取 [ *將此專案設為連結* ] 方塊。
    1. 在 [ *要開啟的視圖*] 下，選擇 [活頁簿 (範本) ]。
    2. 在 [ *連結值*來源] 下，選擇 [連結]。
    3. 選取 [ *在內容 Blade 中開啟連結* ] 方塊。
    4. 選擇活頁*簿連結設定*中的下列設定
        1. 在 [ *範本識別碼*來源] 下，選擇 [資料行]。
        2. 在 [資料 *行* ] 下選擇 [連結]。

    ![具有上述設定之連結設定的螢幕擷取畫面。](./media/workbooks-grid-visualizations/make-this-item-a-link.png)

11. 從資料 *行*中選取 [連結]。 在 [資料 *行*轉譯器] 旁的 [設定] 底下，選取 ** (隱藏資料行) **。
1. 若要變更 [名稱] 資料行的顯示名稱，請選取 [ **標籤] 索引標籤** 。在 [名稱] 作為其資料行 *識別碼*的資料列中，于 [資料行標籤] 下輸入您要顯示的名稱。
2. 選取 **套用**

![螢幕擷取畫面：具有上述設定的方格中的臨界值](./media/workbooks-grid-visualizations/thresholds-workbooks-links.png)

## <a name="fractional-units-percentages"></a>分數單位百分比

 (fr) 的小數單位是在各種類型的方格中，常用的度量動態單位。 當視窗大小/解析度變更時，fr 寬度也會變更。

下列螢幕擷取畫面顯示一個資料表，其中有八個數據行，分別是1fr 寬度和所有相等寬度。 當視窗大小變更時，每個資料行的寬度會依比例變更。

[![方格中資料行寬度值為1fr 的螢幕擷取畫面](./media/workbooks-grid-visualizations/custom-column-width-fr.png)](./media/workbooks-grid-visualizations/custom-column-width-fr.png#lightbox)

下圖顯示相同的資料表，但第一個資料行的寬度設定為50%。 這會將資料行設定為動態方格寬度總計的一半。 除非視窗大小變得太小，否則調整視窗的大小會繼續保留50% 的寬度。 這些動態資料行的最小寬度是以其內容為基礎。 其餘50% 的方格會以八個總小數單位來分割。 下方的「種類」資料行會設定為2fr，因此會佔用剩餘空間的四分之一。 當其他資料行1fr 時，每個資料行都會佔用方格的上半部。

[![方格中資料行的螢幕擷取畫面，其中1個數據行寬度值為50%，而其餘部分為1fr](./media/workbooks-grid-visualizations/custom-column-width-fr2.png)](./media/workbooks-grid-visualizations/custom-column-width-fr2.png#lightbox)

您可以結合 fr、%、px 和 ch 寬度，且其運作方式類似先前的範例。 靜態單位 (ch 和 px) 所設定的寬度是固定的常數，即使視窗/解析度變更，也不會變更。 依% 的資料行所設定的資料行，會根據總方格寬度 (可能不會因為先前的最小寬度) 而不精確。 使用 fr 設定的資料行，只會根據所配置的小數單位數目來分割剩餘的方格空間。

[![螢幕擷取畫面：已使用不同寬度單位的方格中的資料行](./media/workbooks-grid-visualizations/custom-column-width-fr3.png)](./media/workbooks-grid-visualizations/custom-column-width-fr3.png#lightbox)

## <a name="next-steps"></a>接下來的步驟

* 瞭解如何 [在活頁簿中建立樹狀結構](workbooks-tree-visualizations.md)。
* 瞭解如何建立活頁 [簿文字參數](workbooks-text.md)。