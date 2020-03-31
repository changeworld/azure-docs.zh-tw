---
title: 預覽資源管理器中視覺化資料 - Azure 時間序列見解 |微軟文檔
description: 瞭解 Azure 時間序列預覽資源管理器中可用的功能和選項。
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/07/2020
ms.custom: seodec18
ms.openlocfilehash: c117510749abcf997e414371faa6dea1dc79ea7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75861756"
---
# <a name="azure-time-series-insights-preview-explorer"></a>Azure 時間序列見解預覽資源管理器

本文介紹了 Azure 時間序列預覽[版預覽版 Web 應用程式中](https://insights.timeseries.azure.com/preview/demo)提供的各種功能和選項。

## <a name="prerequisites"></a>Prerequisites

要開始使用 Azure 時間序列見解預覽資源管理器，您必須：

* 預配時間序列見解環境。 通過閱讀[Azure 時間序列見解預覽](./time-series-insights-update-create-environment.md)教程，瞭解有關預配實例的更多資訊。
* 提供對為帳戶創建的時序見解環境[的資料訪問](./time-series-insights-data-access.md)。 您可以將存取權提供給其他人和您自己。
* 向時序見解環境添加事件源以將資料推送到環境中：
  * [瞭解如何連接到事件中心](./time-series-insights-how-to-add-an-event-source-eventhub.md) 
  * [瞭解如何連接到 IoT 中心](./time-series-insights-how-to-add-an-event-source-iothub.md)

## <a name="explore-the-time-series-insights-preview-explorer"></a>探索時序見解預覽資源管理器

Azure 時間序列預覽資源管理器包含以下七個元素：

[![時間序列見解 預覽資源管理器概述](media/v2-update-explorer/preview-explorer-overview.png)](media/v2-update-explorer/preview-explorer-overview.png#lightbox)

1. [環境面板](#1-environment-panel)：顯示所有 Azure 時間序列見解環境。
1. [巡覽列](#2-navigation-bar)：允許您在 **"分析"** 和 **"模型"** 頁之間切換。
1. [層次結構樹和搜索面板](#3-hierarchy-tree-and-search-panel)：允許您選擇和搜索要繪製的圖表的特定資料元素。
1. [時間序列良好](#4-time-series-well)：顯示當前選定的所有資料元素。
1. [圖表面板](#5-chart-panel)：顯示當前工作圖表。
1. [時間軸](#6-time-editor-panel)：允許您修改工作時間範圍。
1. [應用欄](#7-app-bar)：包含使用者管理選項（如當前租戶），並允許您更改它們和語言設置。


## <a name="1-environment-panel"></a>1. 環境面板

環境面板可顯示您擁有存取權的所有時間序列深入解析環境。 該清單包括即用即付（預覽）環境以及 S1/S2 環境（常規可用性）。 只需選擇要立即獲取的時間序列見解環境即可。

1. 選擇顯示環境旁邊的下拉箭頭。

   [![環境面板](media/v2-update-explorer/environment-panel.png)](media/v2-update-explorer/environment-panel.png#lightbox)

1. 然後，選擇所需的環境。

## <a name="2-navigation-bar"></a>2. 巡覽列

  [![巡覽列](media/v2-update-explorer/tsi-preview-navigation-bar.png)](media/v2-update-explorer/tsi-preview-navigation-bar.png#lightbox)

使用巡覽列在兩個視圖之間進行選擇：

* **分析**：使用它對建模或未建模的時間序列資料進行圖表並執行豐富的分析。
* **模型**：使用它將新的時序見解預覽類型、層次結構和實例推送到時序見解模型。

### <a name="model-authoring"></a>模型創作

Azure 時序見解預覽支援在時間序列模型上完全創建、讀取、更新和刪除 （CRUD） 操作。

[![模型搜索面板](media/v2-update-explorer/model-search-panel.png)](media/v2-update-explorer/model-search-panel.png#lightbox)

* **時間序列模型類型**：您可以使用時序見解類型來定義變數或公式以執行計算。 它們與給定的時間序列見解實例相關聯。 一個類型可以有一或多個變數。
* **時間序列模型層次結構**：層次結構是資料的系統組織。 階層會描述時間序列深入解析資料中不同實體之間的關係。
* **時序模型實例**：實例是時間序列本身。 在大多數情況下，它們是**DeviceID**或**AssetID**，它是環境中資產的唯一識別碼。

要瞭解有關時間序列模型的更多內容，請閱讀[時間序列模型](./time-series-insights-update-tsm.md)。

## <a name="3-hierarchy-tree-and-search-panel"></a>3. 層次結構樹和搜索面板

層次結構樹和搜索面板允許您輕鬆搜索和導航[時序模型](./time-series-insights-update-tsm.md)層次結構，以查找要在圖表上顯示的特定時間序列實例。 當您選擇實例時，它們不僅會添加到當前圖表中，還會很好地添加到資料中。 

[![層次結構樹和搜索面板](media/v2-update-explorer/tsi-preview-explorer-hierarchy-search.png)](media/v2-update-explorer/tsi-preview-explorer-hierarchy-search.png#lightbox)

搜尋結果窗格還允許您在層次結構視圖或清單視圖中查看結果，以便輕鬆查找要顯示的實例。
 
## <a name="4-time-series-well"></a>4. 時間序列良好

井顯示實例欄位和其他中繼資料，這些中繼資料與選定的時間序列見解實例相關聯。 通過選擇右側的核取方塊，可以從當前圖表中隱藏或顯示特定實例。 

  [![預覽井](media/v2-update-explorer/tsi-preview-ui-explorer-well.png)](media/v2-update-explorer/tsi-preview-ui-explorer-well.png#lightbox)

您可以通過選擇元素左側的紅色**Delete（** 垃圾箱）控制項，從當前資料中刪除特定資料元素。 井還允許您控制圖表中每個元素的顯示方式。 您可以選擇添加最小/最大陰影、資料點、及時移動元素和以階梯式方式視覺化實例。 

此外，探索控制項可讓您輕鬆創建時間偏移和散射圖。  

  [![良好佈局選項](media/v2-update-explorer/well-layout-options.png)](media/v2-update-explorer/well-layout-options.png#lightbox)

> [!NOTE]
> 如果出現以下消息，則實例在所選時間跨度內沒有任何資料。 要解決此問題，請增加時間跨度或確認實例正在推送資料。
>
> ![無資料通知](media/v2-update-explorer/tsi-preview-no-data-warning.png)

## <a name="5-chart-panel"></a>5. 圖表面板

該圖表使您能夠將時間序列實例顯示為線。 您可以按一下 Web 控制項將圖表加大，以摺疊環境面板、資料模型和時間範圍控制面板。 

  [![預覽圖表概述](media/v2-update-explorer/tsi-preview-chart-overview.png)](media/v2-update-explorer/tsi-preview-chart-overview.png#lightbox)

1. **圖表類型**：控制哪些資料元素可用於視覺化。

1. **間隔大小**：間隔大小滑塊工具使您能夠在同一時間跨度內放大和縮小間隔。 這提供了對大切片之間移動的更精確控制，這些時間切片顯示平滑趨勢，下至小至毫秒的切片，使您能夠查看資料的細微性高解析度削減。 滑桿的預設起始點會從您所選取項目中設定為資料的最佳檢視；平衡解析度、查詢速度與細微性。

1. **縮放和平移**：選擇此控制項以縮放和平移圖表。

1. **Y 軸控制**： 迴圈流覽可用的 y 軸視圖選項：

    * `Stacked`：每行都有一個單獨的 Y 軸。
    * `Overlap`：使用它在同一 Y 軸上堆疊多條線，Y 軸資料會根據所選線更改。
    * `Shared`：所有 Y 軸資料一起顯示。

1. **標記元素**：當前選擇的資料元素及其關聯詳細資訊。

通過按住滑鼠時**左鍵按一下**當前圖形上的資料點，然後將所選區域拖動到您選擇的終結點，可以進一步鑽取特定資料切片。 **按右鍵**藍色所選區域，然後選擇 **"縮放**"，如下所示。 您還可以在所選時間跨度中查看和下載遙測事件。

  [![預覽圖表縮放](media/v2-update-explorer/preview-chart-zoom.png)](media/v2-update-explorer/preview-chart-zoom.png#lightbox)

執行 **"縮放**"操作後，將顯示所選資料集。 選擇要迴圈流覽時序見解資料的三個 y 軸表示的格式控制項。

  [![預覽圖表 y 軸](media/v2-update-explorer/tsi-preview-explorer-standard-chart.png)](media/v2-update-explorer/tsi-preview-explorer-standard-chart.png#lightbox)

此處提供了**重疊圖表**的示例：

  [![重疊圖表選項](media/v2-update-explorer/tsi-preview-explorer-overlapping-chart.png)](media/v2-update-explorer/tsi-preview-explorer-overlapping-chart.png#lightbox)

**"更多操作"** 按鈕展開以顯示 **"下載為 CSV"、****連接到電源 BI、****將圖表資料顯示為表**以及**流覽原始事件**選項。

  [![更多操作選項](media/v2-update-explorer/more-actions-icon.png)](media/v2-update-explorer/more-actions-icon.png#lightbox)

詳細瞭解["在時間序列見解本機 Power BI 連接器](concepts-power-bi.md)中**連接到電源 BI"** 選項。

## <a name="6-time-editor-panel"></a>6. 時間編輯器面板

使用時間序列見解時，您首先將選擇時間跨度。 所選時間跨度將控制可用於使用時序見解更新小部件進行操作的資料集。

  [![時間選擇面板](media/v2-update-explorer/tsi-preview-explorer-timeline-element.png)](media/v2-update-explorer/tsi-preview-explorer-timeline-element.png#lightbox)

> [!TIP]
> 時間表的一部分以琥珀色或橙色突出顯示，以指示暖存儲中可用的資料範圍。

以下 Web 控制項在時間序列見解更新中提供，用於選擇工作時間跨度。 

  [![勘探井控制](media/v2-update-explorer/exploration-well-control.png)](media/v2-update-explorer/exploration-well-control.png#lightbox)

1. **內部日期範圍滑塊控制項**：通過拖動兩個終結點控制項來在所需的時間跨度內使用它們。 此內部日期範圍受外部日期範圍滑塊控制項的約束。

1. **增加和減少日期範圍按鈕**：通過選擇任意一個按鈕來選擇所需的時間跨度來增加或減少您的時間跨度。

1. **時間跨度折疊控制項**：此 Web 控制項允許您隱藏除內部日期範圍滑塊工具之外的所有控制項。

1. **外部日期範圍滑塊控制項**：使用端點控制項選擇外部日期範圍，該範圍可用於內部日期範圍控制項。

1. **時間範圍滑塊控制項**：使用它在預設的時間跨度選擇（如最後**30 分鐘**、**最後 12 小時**或**自訂範圍**）之間快速切換。 變更此值時，也會變更間隔大小滑桿工具中所討論的可用間隔範圍。

   [![到/從選擇面板](media/v2-update-explorer/to-and-from-element.png)](media/v2-update-explorer/to-and-from-element.png#lightbox)

## <a name="7-app-bar"></a>7. 應用程式欄

"時序見解預覽"導航面板顯示在時序見解應用的頂部。 它提供了以下功能：

### <a name="current-session-share-link-control"></a>目前工作階段共用連結控制項

  [![共用圖示](media/v2-update-explorer/tsi-preview-explorer-share-icon.png)](media/v2-update-explorer/tsi-preview-explorer-share-icon.png#lightbox)

選擇新的 **"共用"** 圖示可與您的團隊共用 URL 連結。

  [![共用實例 URL](media/v2-update-explorer/tsi-preview-explorer-share-your-view.png)](media/v2-update-explorer/tsi-preview-explorer-share-your-view.png#lightbox)

### <a name="tenant-section"></a>租用戶區段

  [![租戶選擇](media/v2-update-explorer/tsi-preview-explorer-tenant-selection.png)](media/v2-update-explorer/tsi-preview-explorer-tenant-selection.png#lightbox)

* 顯示目前時間序列見解登錄帳戶資訊。
* 使用它在可用的時序見解主題之間切換。
* 使用它查看預覽[演示 Web 應用程式](https://insights.timeseries.azure.com/preview/demo)。

### <a name="theme-selection"></a>佈景主題選取項目

要選擇新主題，請選擇位於右上角的個人資料圖示。 然後，選擇 **"更改主題**"。

  [![佈景主題選取項目](media/v2-update-explorer/tsi-preview-theme-selection.png)](media/v2-update-explorer/tsi-preview-theme-selection.png#lightbox)

> [!TIP]
> 通過選擇設定檔圖示，也可以選擇語言。

Azure 時間序列深入解析預覽支援兩種佈景主題：

* **淺色主題**：本文檔中顯示的預設主題。
* **黑暗主題**： 渲染資源管理器，如下所示：

  [![選定的深色主題](media/v2-update-explorer/tsi-preview-dark-theme-selected.png)](media/v2-update-explorer/tsi-preview-dark-theme-selected.png#lightbox)

## <a name="s1s2-environment-controls"></a>S1/S2 環境控制項

### <a name="preview-terms-panel"></a>預覽術語面板

此區段僅適用於嘗試在更新後的 UI 中使用總管的現有 S1/S2 環境。 您可能希望將一般可用的產品和預覽組合使用。 我們已透過現有的 UI 對更新後的總管新增了一些功能，但您可以在現有的時間序列深入解析總管中，體驗 S1/S2 環境的完整 UI。 

而不是層次結構，時間序列見解術語面板顯示。 術語面板允許您在環境中定義查詢。 使用它還可以根據謂詞篩選資料。

  [![查詢面板的位置](media/v2-update-explorer/s1-s2-preview-query.png)](media/v2-update-explorer/s1-s2-preview-query.png#lightbox)

時間序列深入解析預覽字詞編輯器使用以下參數：

**Where**： 使用 where 子句使用下表中列出的一組運算元快速篩選事件。 如果透過選取運算元進行搜尋，述詞會依據該搜尋自動更新。 支援的運算元類型包括以下內容：

| 作業 | 支援的類型   | 注意 |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | Double、DateTime、TimeSpan | |
| `=`, `!=`, `<>` | String、Bool、Double、DateTime、TimeSpan、NULL |
| `IN` | String、Bool、Double、DateTime、TimeSpan、NULL | 所有的運算元都應該是相同的類型或是 NULL 常數。 |
| `HAS` | String | 右側只允許恒定的字串文本。 不允許使用空字串和 Null。 |

要瞭解有關受支援的查詢操作和資料類型的更多內容，請閱讀[時間序列運算式 （TSX）。](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)

### <a name="examples-of-where-clauses"></a>子句的位置示例

  [![其中子句示例](media/v2-update-explorer/tsi-preview-example-queries.png)](media/v2-update-explorer/tsi-preview-example-queries.png#lightbox)

**度量值**：顯示所有數位列（**雙精度**列）的下拉清單，可用於當前圖表的元素。

**拆分為**： 此下拉清單顯示模型中所有可用的分類列（字串），您可以按該列對資料進行分組。 您最多可以添加五個術語，以便在同一 X 軸上查看。 輸入所需的參數，然後選擇 **"添加"** 以添加新術語。

  [![查詢檢視和篩選視圖之一](media/v2-update-explorer/s1-s2-preview-filtered-view.png)](media/v2-update-explorer/s1-s2-preview-filtered-view.png#lightbox)

您可以通過選擇可見圖示來在圖表面板中顯示和隱藏元素，如下圖所示。 要完全刪除查詢，請選擇紅色**X**。

  [![取消查詢和篩選選項](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png)](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png#lightbox)

## <a name="next-steps"></a>後續步驟

- 在 Azure 時間序列見解預覽中瞭解[存儲和入口](./time-series-insights-update-storage-ingress.md)。

- 閱讀時間序列見解預覽文檔，瞭解[資料建模](./time-series-insights-update-tsm.md)。

- [瞭解如何診斷和排除](./time-series-insights-update-how-to-troubleshoot.md)時間序列見解實例。
