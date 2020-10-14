---
title: 使用計量顧問診斷事件
titleSuffix: Azure Cognitive Services
description: 瞭解如何使用計量建議程式來診斷事件，並取得資料中異常的詳細觀點。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: mbullwin
ms.openlocfilehash: d1f792859aa5407cfaceda0e3ed1b5c21ee160f0
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92043369"
---
# <a name="how-to-diagnose-an-incident-using-metrics-advisor"></a>如何：使用計量建議程式診斷事件

計量建議程式會提供數個診斷功能，並提供偵測到事件的深入觀點，並提供根本原因分析。 當偵測到計量的一組異常時，計量顧問會將異常分組到階層中，並在其上分析。

> [!NOTE]
> 目前，「計量顧問」支援至少具有一個維度之計量的事件診斷，並以  *數值* 類型進行測量。 您的計量需要有匯總的維度值，例如每個維度的總和，這會用來建立診斷階層。 計量審查程式會提供 [**自動匯總設定**](onboard-your-data.md#automatic-roll-up-settings) ，以協助產生匯總值。 

按一下左側導覽視窗中的 [ **事件中樞** ]，以查看指定度量下的所有事件。 在頁面頂端，您可以選取不同的度量來查看其偵測設定和偵測結果，以及變更時間範圍。

> [!TIP]
> 您也可以透過下列方式進入 **事件中樞** ：
> * 針對您的度量按一下視覺效果中的資料點，然後使用出現在 [ **新增意見** 反應] 視窗底部的連結。
> * 針對您的計量，按一下 [ **事件** ] 索引標籤中的其中一個異常。 

[ **總覽** ] 區段包含偵測結果，其中包含所選時間範圍內的異常和警示計數。

:::image type="content" source="../media/diagnostics/incident-hub-overview.png" alt-text="事件中樞" lightbox="../media/diagnostics/incident-hub-overview.png":::

在所選計量和時間範圍內偵測到的事件會列在 [ **事件] 清單**中。 有一些選項可篩選和排序事件。 例如，依嚴重性。 按一下其中一個事件以移至 [ **事件** ] 頁面，以進行進一步的診斷。

:::image type="content" source="../media/diagnostics/incident-list.png" alt-text="事件中樞" lightbox="../media/diagnostics/incident-list.png":::

**診斷**區段可讓您執行事件的深入分析，以及用來識別根本原因的工具。

:::image type="content" source="../media/diagnostics/diagnose-incident.png" alt-text="事件中樞" lightbox="../media/diagnostics/diagnose-incident.png" :::

## <a name="root-cause-advice"></a>根本原因建議

在計量中偵測到異常群組，並導致事件時，計量審查程式將會嘗試分析事件的根本原因。 **根本原因建議** 會針對事件的可能原因提供自動建議。 只有在維度內有匯總的值時，才能使用這項功能。 如果計量沒有維度，根本原因將會是本身。 根本原因會列在右側面板中，而且可能會有幾個原因。 如果資料表中沒有資料，則表示您的維度不符合執行分析的需求。

:::image type="content" source="../media/diagnostics/root-cause-advice.png" lightbox="../media/diagnostics/root-cause-advice.png" alt-text="事件中樞":::


當提供特定維度的根本原因計量時，您可以按一下 [ **移至** 計量] 以查看度量的更多詳細資料。

## <a name="incident-tree"></a>事件樹狀結構

計量審查程式除了可能根本原因的自動化分析之外，還支援使用 **事件樹狀結構**進行手動根本原因分析。 [事件] 頁面中有兩種事件樹狀結構： **快速診斷** 樹狀目錄和 **互動式樹狀**目錄。

快速診斷樹狀目錄用於診斷目前的事件，而根節點僅限於目前的事件根節點。 您可以按一下樹狀節點來展開和折迭節點，它的數列會與樹狀結構上方的圖表中目前的事件系列一起顯示。

互動式樹狀結構可讓您診斷目前的事件以及較舊的事件，以及相關的事件。 使用互動式樹狀結構時，以滑鼠右鍵按一下節點以開啟 [動作] 功能表，您可以在其中選擇要透過根節點向上切入的維度，以及用來向下切入每個節點的維度。 按一下頂端維度清單的 [取消] 按鈕，即可從這個維度中移除向上或向下切入。 以滑鼠左鍵按一下節點以選取它，然後在圖表中顯示其數列和目前的事件系列。

:::image type="content" source="../media/diagnostics/incident-tree.png" alt-text="事件中樞" lightbox="../media/diagnostics/incident-tree.png" :::

## <a name="anomaly-drill-down"></a>異常向下切入

當您正在查看事件資訊時，可能需要取得更詳細的資訊，例如，針對不同的維度和時間戳記。 如果您的資料有一或多個維度，您可以使用向下切入函數來取得更詳細的觀點。 

若要使用向下切入功能，請按一下 [**事件中樞**] 中的 [計量**鑽孔**] 索引標籤。 

:::image type="content" source="../media/diagnostics/metric-drilling.png" lightbox="../media/diagnostics/metric-drilling.png" alt-text="事件中樞":::

[ **維度** ] 設定是事件的維度清單，您可以為每個事件選取其他可用的維度值。 變更維度值之後。 **時間戳記**設定可讓您在不同時刻查看目前的事件。

### <a name="select-drilling-options-and-choose-a-dimension"></a>選取 [切入選項] 並選擇維度

有兩種類型的向下切入選項： **向下** 切入和 **水準比較**。

> [!Note]
> 1. 若要向下切入，您可以流覽不同維度值的資料，但目前選取的維度除外。 
> 2. 針對水準比較，您可以流覽不同維度值的資料，但不包括全部維度。

:::image type="content" source="../media/diagnostics/drill-down-dimension.png"  lightbox="../media/diagnostics/drill-down-dimension.png" alt-text="事件中樞":::

### <a name="value-comparison-for-different-dimension-values"></a>不同維度值的值比較

[向下切入] 索引標籤的第二個區段是一個資料表，其中包含不同維度值的比較。 它包括值、基準值、差異值、差異值，以及是否為異常。
 
:::image type="content" source="../media/diagnostics/drill-down-comparison.png" alt-text="事件中樞" lightbox="../media/diagnostics/drill-down-comparison.png":::


### <a name="value-and-expected-value-comparisons-for-different-dimension-value"></a>不同維度值的值和預期值比較

[向下切入] 索引標籤的第三個區段是具有值和預期值的長條圖，適用于不同的維度值。 長條圖會依值和預期值之間的差異進行排序。 您可以輕鬆地找到未預期的值。 例如，在上圖中，我們可以發現除了 all up 的值之外， **US7** 還會對異常造成最大的貢獻。

:::image type="content" source="../media/diagnostics/drill-down-table.png" alt-text="事件中樞" lightbox="../media/diagnostics/drill-down-table.png":::

### <a name="raw-value-visualization"></a>原始值視覺效果
[向下切入] 索引標籤的最後一個部分是原始值的折線圖。 提供此圖表時，您不需要流覽至 [計量] 頁面以查看詳細資料。

:::image type="content" source="../media/diagnostics/drill-down-line-chart.png" alt-text="事件中樞" lightbox="../media/diagnostics/drill-down-line-chart.png":::

## <a name="view-similar-anomalies-using-time-series-clustering"></a>使用時間序列群集來查看類似的異常

在查看事件時，您可以使用類似的 [ **時間序列** 叢集] 索引標籤來查看與其相關聯的各種數列。 一個群組中的數列會匯總在一起。 從上圖中，我們可以知道至少有兩個數列群組。 只有在符合下列需求時，才能使用這項功能：

1. 計量必須有一或多個維度或維度值。
2. 一個度量內的數列必須有類似的趨勢。

可用的維度會列在索引標籤頂端，您可以進行選取以指定數列。

:::image type="content" source="../media/diagnostics/series-group.png" lightbox="../media/diagnostics/series-group.png"alt-text="事件中樞":::

## <a name="compare-time-series"></a>比較時間序列

有時，在特定時間序列上偵測到異常時，在單一視覺效果中，將它與其他多個數列進行比較會很有説明。 按一下 [ **比較工具** ] 索引標籤，然後按一下藍色的 [ **新增** ] 按鈕。 

:::image type="content" source="../media/diagnostics/add-series.png" alt-text="事件中樞" lightbox="../media/diagnostics/add-series.png":::

從您的資料摘要中選取一個數列。 您可以選擇相同的資料細微性或不同的資料細微性。 選取目標維度並載入數列趨勢，然後按一下 **[確定]** ，將它與前一個數列比較。 數列將會一起放在一個視覺效果中。 您可以繼續新增更多數列以進行比較，並取得進一步的見解。 按一下 [ **比較工具** ] 索引標籤頂端的下拉式功能表，將時間序列資料與時間移動的期間進行比較。  

> [!Warning]
> 若要進行比較，時間序列資料分析可能需要在資料點中移動，讓資料的資料細微性必須支援。 例如，如果您的資料是每週，而您使用當天的 **日期** 比較，您將不會得到任何結果。 在此範例中，您會改為使用月份的比較 **月份** 比較。

選取經過時間移位的比較之後，您可以選取要比較資料值、差異值或百分比差異。

> [!Note]
> * **資料值** 是原始資料值。
> * **Delta 值** 是原始值和比較值之間的差異。
> * **百分比 delta 值** 是原始值與比較值除以比較值之間的差異。

## <a name="related-incidents-across-metrics"></a>計量之間的相關事件

有時您可能需要同時檢查不同度量的事件，或其他計量中的相關事件。 您可以在 [ **交叉計量分析** ] 區段中找到相關事件的清單。 

:::image type="content" source="../media/graph/metrics-graph-cross-metrics-analysis.png" alt-text="事件中樞":::

您必須先新增計量之間的關聯性，才可以看到目前計量的相關事件。 按一下 [ **計量圖表設定]** 以新增關聯性。 只有具有相同維度名稱的計量可以相關。 使用下列參數。

- 目前的資料摘要 & 度量：目前事件的資料摘要和度量
- 方向：兩個度量之間的關聯性方向。 現在 (不會影響相關事件清單) 
- 另一個資料摘要 & 計量：要與目前計量連接的資料摘要和度量


## <a name="next-steps"></a>後續步驟 

- [使用意見反應來調整異常偵測](anomaly-feedback.md)
- [設定計量和微調偵測組態](configure-metrics.md)
