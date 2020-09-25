---
title: Azure 監視器活頁簿 honey 合併數十億視覺效果
description: 瞭解 Azure 監視器活頁簿 honey 合併數十億的視覺效果。
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/18/2020
ms.author: lagayhar
ms.openlocfilehash: 859240623c5dd3009890cd2633ffb9cccf230080
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91342662"
---
# <a name="honey-comb-visualizations"></a>Honey 合併數十億視覺效果

Honey combs 允許可選擇性地分組為叢集的計量或類別的高密度觀點。 它們在以視覺化方式識別熱點和深入探討方面很有用。

下圖顯示跨兩個訂用帳戶之虛擬機器的 CPU 使用率。 每個儲存格都代表一部虛擬機器，而色彩/標籤代表其平均 CPU 使用率 (reds 是熱機) 。 虛擬機器依訂用帳戶叢集化。

[![螢幕擷取畫面顯示兩個訂用帳戶中虛擬機器的 CPU 使用率](.\media\workbooks-honey-comb\cpu-example.png)](.\media\workbooks-honey-comb\cpu-example.png#lightbox)

## <a name="adding-a-honey-comb"></a>新增 honey 合併數十億

1. 按一下 [編輯] 工具列專案，將活頁簿切換至編輯模式。
2. 使用底部的 [ **新增**  ]，然後 *新增查詢* 將記錄查詢控制項加入至活頁簿。
3. 選取 [記錄] 作為 *資料來源*、將 [Log Analytics] 作為 *資源類型*，以及將 *資源* 指向具有虛擬機器效能記錄的工作區。
4. 使用查詢編輯器來輸入您分析的 KQL。

```kusto
    Perf
| where CounterName == 'Available MBytes'
| summarize CounterValue = avg(CounterValue) by Computer, _ResourceId
| extend ResourceGroup = extract(@'/subscriptions/.+/resourcegroups/(.+)/providers/microsoft.compute/virtualmachines/.+', 1, _ResourceId)
| extend ResourceGroup = iff(ResourceGroup == '', 'On-premise computers', ResourceGroup), Id = strcat(_ResourceId, '::', Computer)
```

5. 執行查詢。
6. 將 *視覺效果* 設定為「圖形」。
7. 選取 [ **圖形設定]**。
    1. 在底部的 *版面配置欄位* 中，設定：
        1. 圖形類型： **Hive**叢集。
        2. 節點識別碼： `Id` 。
        3. 分組方式： `None` 。
        4. 節點大小：100。
        5. 六邊形之間的邊界： `5` 。
        6. 色彩類型： **熱度圖**。
        7. 節點色彩欄位： `CouterValue` 。
        8. 色調色板： `Red to Green` 。
        9. 最小值： `100` 。
        10. 最大值： `2000` 。
    2. 在頂端的 *節點格式* 設定中，設定：
        1. 熱門內容：
            1. 使用資料行： `Computer` 。
            2. 資料行轉譯器」 `Text` 。
        9. 中心內容：
            1. 使用資料行： `CounterValue` 。
            2. 資料行轉譯器： `Big Number` 。
            3. 色調色板： `None` 。
            4. 選取 [ *自訂數位格式* ] 方塊。
            5. 單位： `Megabytes` 。
            6. 最大小數位數： `1` 。
8. 選取窗格底部的 [ **儲存並關閉** ] 按鈕。

[![具有上述查詢和設定的查詢控制項、圖形設定和 honey 合併數十億的螢幕擷取畫面](.\media\workbooks-honey-comb\available-memory.png)](.\media\workbooks-honey-comb\available-memory.png#lightbox)

## <a name="honey-comb-layout-settings"></a>Honey 合併數十億版面配置設定

| 設定 | 說明 |
|:------------- |:-------------|
| `Node Id` | 選取提供節點唯一識別碼的資料行。 資料行的值可以是字串或數位。 |
| `Group By Field` | 選取要對節點進行叢集的資料行。 |
| `Node Size` | 設定六邊形儲存格的大小。 使用與 `Margin between hexagons` 屬性可自訂 honey 合併數十億圖表的外觀。 |
| `Margin between hexagons` | 設定六邊形儲存格之間的間距。 使用與 `Node size` 屬性可自訂 honey 合併數十億圖表的外觀。 |
| `Coloring type` | 選取要用來為節點上色的配置。 |
| `Node Color Field` | 選取一個資料行，此資料行提供節點區域所依據的度量。 |

## <a name="node-coloring-types"></a>節點著色類型

| 色彩類型 | 說明 |
|:------------- |:-------------|
| `None` | 所有節點都有相同的色彩。 |
| `Categorical` | 節點會根據結果集中資料行的值或類別來指派色彩。 在上述範例中，色彩是以結果集的資料行 _類型_ 為基礎。 支援的調色板為 `Default` 、 `Pastel` 和 `Cool tone` 。  |
| `Heatmap` | 在此類型中，儲存格會根據度量資料行和調色板來著色。 這可提供簡單的方式來醒目提示跨資料格的計量分佈。 |
| `Thresholds` | 在此類型中，資料格色彩是由閾值規則設定 (例如， _cpu > 90% => 紅色、60% > cpu > 90% => 黃色，cpu < 60% => 綠色_)  |
| `Field Based` | 在此類型中，資料行提供要用於節點的特定 RGB 值。 提供最大的彈性，但通常需要更多的工作才能啟用。  |
      
## <a name="node-format-settings"></a>節點格式設定

Honey 合併數十億作者可以指定要傳送至節點不同部分的內容：頂端、左方、置中、靠右和底部。 作者可以自由使用任何轉譯器活頁簿，以支援 (文字、大數位、spark 線、圖示等 ) 。

## <a name="next-steps"></a>後續步驟

- 瞭解如何 [在活頁簿中建立複合 bar](workbooks-composite-bar.md)轉譯器。
- 瞭解如何將 [Azure 監視器記錄資料匯入 Power BI](powerbi.md)。
