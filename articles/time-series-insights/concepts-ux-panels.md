---
title: 將時間序列深入解析總管-Azure 時間序列深入解析 Gen2 中的資料視覺化 |Microsoft Docs
description: 瞭解 Azure 時間序列深入解析 Explorer 中可用的功能和選項。
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/22/2021
ms.custom: seodec18
ms.openlocfilehash: 55e102cc7e27cbcd9a65999f590c8264f1ad4434
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98736847"
---
# <a name="azure-time-series-insights-explorer"></a>Azure 時間序列深入解析總管

本文說明 Azure 時間序列深入解析 Gen2 [示範環境](https://insights.timeseries.azure.com/preview/demo)內可用的各種功能和選項。

## <a name="prerequisites"></a>Prerequisites

若要開始使用 Azure 時間序列深入解析 Explorer，您必須：

* 布建 Azure 時間序列深入解析 Gen2 環境。 若要深入瞭解如何布建實例，請閱讀 [Azure 時間序列深入解析 Gen2](./tutorials-set-up-tsi-environment.md) 教學課程。
* 提供您為帳戶建立之 Azure 時間序列深入解析 Gen2 環境的[資料存取權](./concepts-access-policies.md)。 您可以將存取權提供給其他人和您自己。
* 將事件來源新增至 Azure 時間序列深入解析 Gen2 環境，以將資料推送至環境：
  * 瞭解 [如何連接到事件中樞](./how-to-ingest-data-event-hub.md)
  * 瞭解 [如何連線到 IoT 中樞](./how-to-ingest-data-iot-hub.md)

## <a name="explore-the-azure-time-series-insights-explorer"></a>探索 Azure 時間序列深入解析 Explorer

Azure 時間序列深入解析 Explorer 包含下列七個元素：

[![Azure 時間序列深入解析 Explorer 總覽](media/v2-update-explorer/preview-explorer-overview.png)](media/v2-update-explorer/preview-explorer-overview.png#lightbox)

1. [環境面板](#1-environment-panel)：顯示您所有的 Azure 時間序列深入解析 Gen2 環境。
1. [巡覽列](#2-navigation-bar)：可讓您在 [ **分析** ] 和 [ **模型** ] 頁面之間切換。
1. 階層[樹狀結構和搜尋面板](#3-hierarchy-tree-and-search-panel)：可讓您選取和搜尋要繪製的特定資料元素。
1. [時間序列很好](#4-time-series-well)：顯示您目前選取的所有資料元素。
1. [圖表面板](#5-chart-panel)：顯示目前的工作圖表。
1. [時間軸](#6-time-editor-panel)：可讓您修改工作時間範圍。
1. [應用程式行](#7-app-bar)：包含 (的使用者管理選項，例如目前的租使用者) ，並可讓您變更和語言設定。

## <a name="1-environment-panel"></a>1. 環境面板

[環境] 面板會顯示您有權存取的所有 Azure 時間序列深入解析 Gen2 環境。 此清單包含 Gen2 環境以及 Gen1 環境。 只要選取您想要用來立即使用的環境。

1. 選取顯示的環境旁的下拉箭號。

   [![環境面板](media/v2-update-explorer/environment-panel.png)](media/v2-update-explorer/environment-panel.png#lightbox)

1. 然後，選取您要的環境。

## <a name="2-navigation-bar"></a>2. 巡覽列

  [![巡覽列](media/v2-update-explorer/tsi-preview-navigation-bar.png)](media/v2-update-explorer/tsi-preview-navigation-bar.png#lightbox)

使用巡覽列在兩個視圖之間進行選取：

* **分析**：使用它來對模型化或未建模的時間序列資料進行圖表化和執行豐富的分析。
* **模型**：使用它來將新的 Azure 時間序列深入解析 Gen2 類型、階層和實例推送至您的時間序列模型。

### <a name="model-authoring"></a>模型撰寫

Azure 時間序列深入解析 Gen2 支援您的時間序列模型上 (CRUD) 作業的完整建立、讀取、更新及刪除。

[![模型搜尋面板](media/v2-update-explorer/model-search-panel.png)](media/v2-update-explorer/model-search-panel.png#lightbox)

* **時間序列模型類型**：您可以使用時間序列模型類型來定義用來執行計算的變數或公式。 它們會與指定的時間序列模型實例相關聯。 一個類型可以有一或多個變數。
* **時間序列模型** 階層：階層是您的資料的系統化組織。 階層會描述您的時間序列模型中不同實例之間的關聯性。
* **時間序列模型實例**：實例是時間序列本身。 在大部分的情況下，它們是 **DeviceID** 或 **AssetID**，也就是環境中資產的唯一識別碼。

若要深入瞭解時間序列模型，請參閱時間 [序列模型](./concepts-model-overview.md)。

## <a name="3-hierarchy-tree-and-search-panel"></a>3. 階層樹狀結構和搜尋面板

階層樹狀結構和 [搜尋] 面板可讓您輕鬆地搜尋和流覽 [時間序列模型](./concepts-model-overview.md) 階層，以找出您想要在圖表上顯示的特定時間序列實例。 當您選取實例時，不只會將其新增至目前的圖表，也會新增至資料。

[![階層樹狀結構和搜尋面板](media/v2-update-explorer/tsi-preview-explorer-hierarchy-search.png)](media/v2-update-explorer/tsi-preview-explorer-hierarchy-search.png#lightbox)

[搜尋結果] 窗格也可讓您在階層視圖或清單視圖中查看結果，讓您可以輕鬆地找到您想要顯示的實例。

## <a name="4-time-series-well"></a>4. 時間序列很好

這樣就會顯示與所選時間序列模型實例相關聯的實例欄位和其他中繼資料。 選取右側的核取方塊，即可隱藏或顯示目前圖表中的特定實例。

  [![Gen2 well](media/v2-update-explorer/tsi-preview-ui-explorer-well.png)](media/v2-update-explorer/tsi-preview-ui-explorer-well.png#lightbox)

您可以從目前的資料中移除特定的資料元素，方法是選取紅色 **刪除** (垃圾桶可以在元素的左邊) 控制項。 也可讓您控制每個元素在圖表中的顯示方式。 您可以選擇新增最小/最大陰影、資料點、在時間內移動專案，以及以逐步方式將實例視覺化。

此外，探勘控制項可讓您輕鬆地建立時間移位和散佈圖。  

  [![適當的版面配置選項](media/v2-update-explorer/well-layout-options.png)](media/v2-update-explorer/well-layout-options.png#lightbox)

> [!NOTE]
> 如果出現下列訊息，實例在選取的時間範圍內不會有任何資料。 若要解決此問題，請增加時間範圍，或確認實例正在推送資料。
>
> ![沒有資料通知](media/v2-update-explorer/tsi-preview-no-data-warning.png)

## <a name="5-chart-panel"></a>5. 圖表面板

此圖表可讓您將時間序列實例顯示為線條。 您可以按一下 Web 控制項將圖表加大，以摺疊環境面板、資料模型和時間範圍控制面板。

  [![Gen2 圖表總覽](media/v2-update-explorer/tsi-preview-chart-overview.png)](media/v2-update-explorer/tsi-preview-chart-overview.png#lightbox)

1. **圖表類型**：控制可供視覺效果使用的資料元素。

1. **間隔大小**：間隔大小滑杆工具可讓您在相同的時間範圍內放大和縮小間隔。 這可讓您更精確地控制在較短時間內顯示配量的大型片段之間的移動，讓您可以審視資料的細微、高解析度的剪下。 滑杆的預設起始點會設定為您選取專案中最理想的資料檢視;平衡解析度、查詢速度和細微性。

1. **Zoom 和平移**：選取此控制項可縮放並平移圖表。

1. **Y 軸控制項**：迴圈使用可用的 Y 軸視圖選項：

    * `Stacked`：每一行都有個別的 Y 軸。
    * `Overlap`：使用它在相同的 Y 軸上堆疊多行，Y 軸資料會根據選取的線條而變更。
    * `Shared`：所有 Y 軸資料會一起顯示。

1. **標記元素**：目前選取的資料元素及其相關的詳細資料。

您可以藉由在目前圖形上的資料點 **按一下滑鼠左鍵** ，然後將選取的區域拖曳至您選擇的端點，進一步向下切入到特定的資料配量。 以 **滑鼠右鍵按一下** 藍色選取的區域，然後選取 [**顯示比例**]，如下所示。 您也可以在選取的時間範圍內查看和下載遙測事件。

  [![Gen2 圖縮放](media/v2-update-explorer/preview-chart-zoom.png)](media/v2-update-explorer/preview-chart-zoom.png#lightbox)

執行 **縮放** 動作之後，將會顯示您選取的資料集。 選取 format 控制項，以迴圈流覽資料的三個 y 軸標記法。

  [![Gen2 圖 y 軸](media/v2-update-explorer/tsi-preview-explorer-standard-chart.png)](media/v2-update-explorer/tsi-preview-explorer-standard-chart.png#lightbox)

以下提供重 **迭圖表** 的範例：

  [![重迭的圖表選項](media/v2-update-explorer/tsi-preview-explorer-overlapping-chart.png)](media/v2-update-explorer/tsi-preview-explorer-overlapping-chart.png#lightbox)

[**其他動作**] 按鈕會展開，以顯示 [**下載為 CSV** **]、[連接到 Power BI]、[****以資料表形式顯示圖表資料**] 和 [**探索原始事件**] 選項。

  [![其他動作選項](media/v2-update-explorer/more-actions-icon.png)](media/v2-update-explorer/more-actions-icon.png#lightbox)

深入瞭解 [Power BI 連接器](concepts-power-bi.md)中的 [**連接到 Power BI]** 選項。

## <a name="6-time-editor-panel"></a>6. 時間編輯器面板

使用 Azure 時間序列深入解析 Gen2 時，您會先選取時間範圍。 選取的時間範圍會控制可使用 Azure 時間序列深入解析 Gen2 update widget 來操作的資料集。

  [![時間選取面板](media/v2-update-explorer/tsi-preview-explorer-timeline-element.png)](media/v2-update-explorer/tsi-preview-explorer-timeline-element.png#lightbox)

> [!TIP]
> 時間軸的一部分會以琥珀色或橙色的色彩反白顯示，以表示暖存放區中可用的資料範圍。

下列 web 控制項可在 Azure 時間序列深入解析 Gen2 中使用，以便選取您的工作時間範圍。

  [![探索妥善掌控](media/v2-update-explorer/exploration-well-control.png)](media/v2-update-explorer/exploration-well-control.png#lightbox)

1. **內部日期範圍滑杆控制項**：使用兩個端點控制項，方法是將它們拖曳到您想要的時間範圍內。 此內部日期範圍受限於外部日期範圍滑杆控制項。

1. **增加和減少日期範圍按鈕**：選取您想要的間隔按鈕，以增加或減少您的時間範圍。

1. **時間範圍** 折迭控制項：此 web 控制項可讓您隱藏內部日期範圍滑杆工具以外的所有控制項。

1. **外部日期範圍滑杆控制項**：使用端點控制項來選取外部日期範圍，這將可供您的內部日期範圍控制項使用。

1. **時間範圍滑杆控制項**：用來在預設的時間範圍選取專案之間快速切換，例如過去 **30 分鐘**、 **過去12小時** 或 **自訂範圍**。 變更此值時，也會變更間隔大小滑桿工具中所討論的可用間隔範圍。

   [![從選取專案面板到](media/v2-update-explorer/to-and-from-element.png)](media/v2-update-explorer/to-and-from-element.png#lightbox)

## <a name="7-app-bar"></a>7. 應用程式行

Azure 時間序列深入解析的 Gen2 導覽面板會顯示在您的應用程式頂端。 它提供下列功能：

### <a name="current-session-share-link-control"></a>目前工作階段共用連結控制項

  [![共用圖示](media/v2-update-explorer/tsi-preview-explorer-share-icon.png)](media/v2-update-explorer/tsi-preview-explorer-share-icon.png#lightbox)

選取新的 **共用** 圖示，以與您的小組共用 URL 連結。

  [![共用您的實例 URL](media/v2-update-explorer/tsi-preview-explorer-share-your-view.png)](media/v2-update-explorer/tsi-preview-explorer-share-your-view.png#lightbox)

### <a name="tenant-section"></a>租用戶區段

  [![租使用者選取](media/v2-update-explorer/tsi-preview-explorer-tenant-selection.png)](media/v2-update-explorer/tsi-preview-explorer-tenant-selection.png#lightbox)

* 顯示目前的 Azure 時間序列深入解析 Gen2 登入帳戶資訊。
* 您可以使用它在可用的主題之間切換。
* 您可以使用它來觀看 Gen2 [示範環境](https://insights.timeseries.azure.com/preview/demo)。

### <a name="theme-selection"></a>佈景主題選取項目

若要選取新的主題，請選取右上角的設定檔圖示。 然後，選取 [ **變更主題**]。

  [![佈景主題選取項目](media/v2-update-explorer/tsi-preview-theme-selection.png)](media/v2-update-explorer/tsi-preview-theme-selection.png#lightbox)

> [!TIP]
> 選取您的設定檔圖示也可以使用語言選擇。

Azure 時間序列深入解析 Explorer 支援兩個主題：

* **淺色主題**：本檔中顯示的預設主題。
* **深色主題**：轉譯 Explorer，如下所示：

  [![選取的深色主題](media/v2-update-explorer/tsi-preview-dark-theme-selected.png)](media/v2-update-explorer/tsi-preview-dark-theme-selected.png#lightbox)

## <a name="gen1-environment-controls"></a>Gen1 環境控制項

### <a name="gen2-terms-panel"></a>Gen2 條款面板

本節只適用于在更新的 UI 中嘗試使用瀏覽器的現有 Gen1 環境。 您可能會想要搭配使用 Gen1 product 和 Gen2 產品。 我們已將現有 UI 的一些功能新增至更新的瀏覽器，但您可以在新的 Azure 時間序列深入解析 Explorer 中取得 Gen1 環境的完整 UI 體驗。

Azure 時間序列深入解析的 Gen2 條款面板會顯示，而不是階層。 [詞彙] 面板可讓您在環境中定義查詢。 您也可以使用它來根據述詞來篩選資料。

  [![Where 查詢面板](media/v2-update-explorer/s1-s2-preview-query.png)](media/v2-update-explorer/s1-s2-preview-query.png#lightbox)

[Azure 時間序列深入解析 Gen2 條款編輯器] 面板會採用下列參數：

**Where**：使用 where 子句，利用下表所列的運算元集合來快速篩選您的事件。 如果透過選取運算元進行搜尋，述詞會依據該搜尋自動更新。 支援的運算元類型包括下列各項：

| 作業    | 支援的類型    | 備註 |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | Double、DateTime、TimeSpan | |
| `=`, `!=`, `<>` | String、Bool、Double、DateTime、TimeSpan、NULL |
| `IN` | String、Bool、Double、DateTime、TimeSpan、NULL | 所有的運算元都應該是相同的類型或是 NULL 常數。 |
| `HAS` | String | 右邊只允許常數位串常值。 不允許空字串和 Null。 |

若要深入瞭解支援的查詢作業和資料類型，請參閱 [ (TSX) 的時間序列運算式 ](/rest/api/time-series-insights/reference-time-series-expression-syntax)。

### <a name="examples-of-where-clauses"></a>Where 子句的範例

  [![Where 子句範例](media/v2-update-explorer/tsi-preview-example-queries.png)](media/v2-update-explorer/tsi-preview-example-queries.png#lightbox)

**量值**：顯示所有數值資料行的下拉式清單 (**雙精度** 浮點數) 您可以使用做為目前圖表的元素。

**分割依據**：這個下拉式清單會顯示您模型中的所有可用類別資料行 (字串) ，您可以將資料分組。 您最多可以加入五個詞彙，以在相同的 X 軸上查看。 輸入您想要的參數，然後選取 [ **新增** ] 以新增新的字詞。

  [![查詢和篩選視圖1](media/v2-update-explorer/s1-s2-preview-filtered-view.png)](media/v2-update-explorer/s1-s2-preview-filtered-view.png#lightbox)

您可以選取 [可見] 圖示，以顯示和隱藏 [圖表] 面板中的元素，如下圖所示。 若要完全移除查詢，請選取紅色 **X**。

  [![取消已查詢和已篩選的選項](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png)](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png#lightbox)

## <a name="next-steps"></a>後續步驟

* 瞭解如何將 [資料擷取](./concepts-ingestion-overview.md) 至您的環境。

* 查看 [儲存體](concepts-storage.md)上的文章。

* 請參閱 Azure 時間序列深入解析 Gen2 中的 [資料模型](./concepts-model-overview.md) 化。

* 瞭解如何針對您的環境進行 [診斷和疑難排解](./how-to-diagnose-troubleshoot.md) 。
