---
title: 在預覽瀏覽器中將資料視覺化-Azure 時間序列深入解析 |Microsoft Docs
description: 瞭解 Azure 時間序列深入解析預覽瀏覽器中可用的功能和選項。
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/07/2020
ms.custom: seodec18
ms.openlocfilehash: 06f235b7f1c20681beee1f5be4c18de60d53a690
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86037302"
---
# <a name="azure-time-series-insights-preview-explorer"></a>Azure 時間序列深入解析預覽 explorer

本文說明 Azure 時間序列深入解析 Preview[示範 web 應用程式](https://insights.timeseries.azure.com/preview/demo)中可用的各種功能和選項。

## <a name="prerequisites"></a>必要條件

若要開始使用 Azure 時間序列深入解析 Preview explorer，您必須：

* 已布建時間序列深入解析環境。 閱讀[Azure 時間序列深入解析 Preview](./time-series-insights-update-create-environment.md)教學課程，以深入瞭解如何布建實例。
* 提供您為帳戶所建立時間序列深入解析環境的[資料存取權](./time-series-insights-data-access.md)。 您可以將存取權提供給其他人和您自己。
* 將事件來源新增至時間序列深入解析環境，以將資料推送至環境：
  * 瞭解[如何連接到事件中樞](./time-series-insights-how-to-add-an-event-source-eventhub.md) 
  * 瞭解[如何連接到 IoT 中樞](./time-series-insights-how-to-add-an-event-source-iothub.md)

## <a name="explore-the-time-series-insights-preview-explorer"></a>探索時間序列深入解析預覽 explorer

Azure 時間序列深入解析 Preview explorer 包含下列七個元素：

[![時間序列深入解析預覽 explorer 總覽](media/v2-update-explorer/preview-explorer-overview.png)](media/v2-update-explorer/preview-explorer-overview.png#lightbox)

1. [環境面板](#1-environment-panel)：顯示您所有的 Azure 時間序列深入解析環境。
1. [巡覽列](#2-navigation-bar)：可讓您在 [**分析**] 和 [**模型**] 頁面之間切換。
1. 階層[樹狀結構和搜尋面板](#3-hierarchy-tree-and-search-panel)：可讓您選取及搜尋要繪製成圖表的特定資料元素。
1. [時間序列良好](#4-time-series-well)：顯示您目前選取的所有資料元素。
1. [圖表面板](#5-chart-panel)：顯示您目前的工作圖表。
1. [時間軸](#6-time-editor-panel)：可讓您修改工作時間範圍。
1. [應用程式行](#7-app-bar)：包含您的使用者管理選項（例如目前的租使用者），並可讓您變更它們和語言設定。


## <a name="1-environment-panel"></a>1. 環境面板

環境面板可顯示您擁有存取權的所有時間序列深入解析環境。 此清單包含「隨用隨付」（預覽）環境以及 S1/S2 環境（一般可用性）。 只要選取您想要用來立即執行的時間序列深入解析環境即可。

1. 選取顯示的環境旁邊的下拉箭號。

   [![環境面板](media/v2-update-explorer/environment-panel.png)](media/v2-update-explorer/environment-panel.png#lightbox)

1. 然後，選取您想要的環境。

## <a name="2-navigation-bar"></a>2. 巡覽列

  [![巡覽列](media/v2-update-explorer/tsi-preview-navigation-bar.png)](media/v2-update-explorer/tsi-preview-navigation-bar.png#lightbox)

使用巡覽列，在兩個不同的視圖之間進行選取：

* **分析**：用來繪製模型化或未模型化時間序列資料，並對其執行豐富的分析。
* **Model**：用來將新的時間序列深入解析預覽類型、階層和實例推送至您的時間序列深入解析模型。

### <a name="model-authoring"></a>模型撰寫

Azure 時間序列深入解析 Preview 支援在您的時間序列模型上進行完整的建立、讀取、更新和刪除（CRUD）作業。

[![模型搜尋面板](media/v2-update-explorer/model-search-panel.png)](media/v2-update-explorer/model-search-panel.png#lightbox)

* **時間序列模型類型**：您可以使用時間序列深入解析類型來定義用於執行計算的變數或公式。 它們會與指定的時間序列深入解析實例相關聯。 一個類型可以有一或多個變數。
* **時間序列模型**階層：階層是您的資料的系統化組織。 階層會描述時間序列深入解析資料中不同實體之間的關係。
* **時間序列模型實例**：實例是時間序列本身。 在大部分情況下，它們是**DeviceID**或**AssetID**，也就是環境中資產的唯一識別碼。

若要深入瞭解時間序列模型，請參閱[時間序列](./concepts-model-overview.md)模型。

## <a name="3-hierarchy-tree-and-search-panel"></a>3. 階層樹狀結構和搜尋面板

階層樹狀結構和 [搜尋] 面板可讓您輕鬆地搜尋及流覽[時間序列模型](./concepts-model-overview.md)階層，以尋找您想要在圖表上顯示的特定時間序列實例。 當您選取實例時，它們不僅會加入目前的圖表中，還會加入至資料中。 

[![階層樹狀結構和搜尋面板](media/v2-update-explorer/tsi-preview-explorer-hierarchy-search.png)](media/v2-update-explorer/tsi-preview-explorer-hierarchy-search.png#lightbox)

[搜尋結果] 窗格也可讓您在階層視圖或清單視圖中查看結果，讓您可以輕鬆地找到想要顯示的實例。
 
## <a name="4-time-series-well"></a>4. 時間序列良好

這會顯示實例欄位，以及與所選時間序列深入解析實例相關聯的其他中繼資料。 藉由選取右側的核取方塊，您可以隱藏或顯示目前圖表中的特定實例。 

  [![預覽良好](media/v2-update-explorer/tsi-preview-ui-explorer-well.png)](media/v2-update-explorer/tsi-preview-ui-explorer-well.png#lightbox)

您可以選取專案左側的紅色 [**刪除**] （垃圾桶）控制項，從目前的資料中移除特定的資料元素。 也可以讓您控制每個元素在圖表中的顯示方式。 您可以加入宣告最小/最大陰影、資料點、將專案移入時間，並以逐步方式將實例視覺化。 

此外，探勘控制項可讓您輕鬆地建立時間移位和散佈圖。  

  [![良好的版面配置選項](media/v2-update-explorer/well-layout-options.png)](media/v2-update-explorer/well-layout-options.png#lightbox)

> [!NOTE]
> 如果出現下列訊息，在選取的時間範圍內，實例就不會有任何資料。 若要解決此問題，請增加時間範圍，或確認實例正在推送資料。
>
> ![沒有資料通知](media/v2-update-explorer/tsi-preview-no-data-warning.png)

## <a name="5-chart-panel"></a>5. 圖表面板

圖表可讓您將時間序列實例顯示為線條。 您可以按一下 Web 控制項將圖表加大，以摺疊環境面板、資料模型和時間範圍控制面板。 

  [![預覽圖表總覽](media/v2-update-explorer/tsi-preview-chart-overview.png)](media/v2-update-explorer/tsi-preview-chart-overview.png#lightbox)

1. **圖表類型**：控制哪些資料元素可供視覺效果使用。

1. **間隔大小**：間隔大小滑杆工具可讓您在相同的時間範圍內放大和縮小間隔。 這可讓您更精確地控制在大型時間配量之間的移動，以將平滑的趨勢向下顯示為小至毫秒的磁區，讓您能夠查看細微、高解析度的資料剪切。 滑桿的預設起始點會從您所選取項目中設定為資料的最佳檢視；平衡解析度、查詢速度與細微性。

1. **縮放和平移**：選取此控制項可縮放和移動圖表。

1. **Y 軸控制項**：迴圈顯示可用的 Y 軸視圖選項：

    * `Stacked`：每一行都有個別的 Y 軸。
    * `Overlap`：用來在相同的 Y 軸上堆疊多行，並根據選取的線條變更 Y 軸資料。
    * `Shared`：所有 Y 軸的資料會一起顯示。

1. **標記元素**：目前選取的資料元素及其相關聯的詳細資料。

您可以藉由在目前圖表上的資料點上**按一下滑鼠左鍵**，然後將選取的區域拖曳至您選擇的端點，進一步切入特定的資料配量。 以**滑鼠右鍵按一下**藍色、選取的區域，然後選取 [**縮放**]，如下所示。 您也可以在選取的時間範圍內，查看並下載遙測事件。

  [![預覽圖表縮放比例](media/v2-update-explorer/preview-chart-zoom.png)](media/v2-update-explorer/preview-chart-zoom.png#lightbox)

執行 [**縮放**] 動作之後，將會顯示您選取的資料集。 選取 [格式] 控制項以迴圈流覽時間序列深入解析資料的三個 y 軸標記法。

  [![預覽圖表 y 軸](media/v2-update-explorer/tsi-preview-explorer-standard-chart.png)](media/v2-update-explorer/tsi-preview-explorer-standard-chart.png#lightbox)

這裡提供重**迭圖表**的範例：

  [![重迭圖表選項](media/v2-update-explorer/tsi-preview-explorer-overlapping-chart.png)](media/v2-update-explorer/tsi-preview-explorer-overlapping-chart.png#lightbox)

[**更多動作**] 按鈕會展開以顯示 [**下載為 CSV** **]、[連接到 Power BI]、[將****圖表資料顯示為數據表**] 和 [**探索原始事件**] 選項。

  [![其他動作選項](media/v2-update-explorer/more-actions-icon.png)](media/v2-update-explorer/more-actions-icon.png#lightbox)

深入瞭解[時間序列深入解析 native Power BI 連接器](concepts-power-bi.md)中的 [**連接到 Power BI]** 選項。

## <a name="6-time-editor-panel"></a>6. 時間編輯器面板

當您使用時間序列深入解析時，您會先選取時間範圍。 選取的時間範圍將會控制可用來操作時間序列深入解析更新 widget 的資料集。

  [![時間選取面板](media/v2-update-explorer/tsi-preview-explorer-timeline-element.png)](media/v2-update-explorer/tsi-preview-explorer-timeline-element.png#lightbox)

> [!TIP]
> 時間軸的一部分會以琥珀色或橙色反白顯示，以指出暖存放區中可用的資料範圍。

時間序列深入解析更新中提供下列 web 控制項來選取您的工作時間範圍。 

  [![探索妥善掌控](media/v2-update-explorer/exploration-well-control.png)](media/v2-update-explorer/exploration-well-control.png#lightbox)

1. **內部日期範圍滑杆控制項**：使用兩個端點控制項，方法是將它們拖曳至您想要的時間範圍。 這個內部日期範圍受限於外部日期範圍滑杆控制項。

1. **增加和減少日期範圍按鈕**：在您想要的間隔中選取任一按鈕，以增加或減少時間範圍。

1. **時間範圍**折迭控制項：這個 web 控制項可讓您隱藏內部日期範圍滑杆工具以外的所有控制項。

1. **外部日期範圍滑杆控制項**：使用端點控制項選取外部日期範圍，這將可供您的內部日期範圍控制項使用。

1. **時間範圍滑杆控制項**：用來在預設的時間範圍選項之間快速切換，例如過去**30 分鐘**、**過去12小時**或**自訂範圍**。 變更此值時，也會變更間隔大小滑桿工具中所討論的可用間隔範圍。

   [![從選取面板到和](media/v2-update-explorer/to-and-from-element.png)](media/v2-update-explorer/to-and-from-element.png#lightbox)

## <a name="7-app-bar"></a>7. 應用程式行

時間序列深入解析預覽] 導覽面板會出現在時間序列深入解析應用程式的頂端。 它提供下列功能：

### <a name="current-session-share-link-control"></a>目前工作階段共用連結控制項

  [![共用圖示](media/v2-update-explorer/tsi-preview-explorer-share-icon.png)](media/v2-update-explorer/tsi-preview-explorer-share-icon.png#lightbox)

選取 [新增**共用**] 圖示，與您的小組共用 URL 連結。

  [![共用您的實例 URL](media/v2-update-explorer/tsi-preview-explorer-share-your-view.png)](media/v2-update-explorer/tsi-preview-explorer-share-your-view.png#lightbox)

### <a name="tenant-section"></a>租用戶區段

  [![租使用者選取](media/v2-update-explorer/tsi-preview-explorer-tenant-selection.png)](media/v2-update-explorer/tsi-preview-explorer-tenant-selection.png#lightbox)

* 顯示目前時間序列深入解析登入帳戶資訊。
* 使用它在可用的時間序列深入解析主題之間切換。
* 使用它來觀看預覽[示範 web 應用程式](https://insights.timeseries.azure.com/preview/demo)。

### <a name="theme-selection"></a>佈景主題選取項目

若要選取新的主題，請選取位於右上角的設定檔圖示。 然後，選取 [**變更主題**]。

  [![佈景主題選取項目](media/v2-update-explorer/tsi-preview-theme-selection.png)](media/v2-update-explorer/tsi-preview-theme-selection.png#lightbox)

> [!TIP]
> 選取您的設定檔圖示也可以選取 [語言]。

Azure 時間序列深入解析預覽支援兩種佈景主題：

* **淺色主題**：這份檔中顯示的預設主題。
* **深色主題**：呈現 explorer，如下所示：

  [![選取的深色主題](media/v2-update-explorer/tsi-preview-dark-theme-selected.png)](media/v2-update-explorer/tsi-preview-dark-theme-selected.png#lightbox)

## <a name="s1s2-environment-controls"></a>S1/S2 環境控制項

### <a name="preview-terms-panel"></a>預覽條款面板

此區段僅適用於嘗試在更新後的 UI 中使用總管的現有 S1/S2 環境。 您可能想要使用正式推出的產品和預覽組合。 我們已透過現有的 UI 對更新後的總管新增了一些功能，但您可以在現有的時間序列深入解析總管中，體驗 S1/S2 環境的完整 UI。 

而不是階層，會顯示 [時間序列深入解析詞彙] 面板。 [詞彙] 面板可讓您定義環境中的查詢。 您也可以使用它來根據述詞來篩選資料。

  [![Where 查詢面板](media/v2-update-explorer/s1-s2-preview-query.png)](media/v2-update-explorer/s1-s2-preview-query.png#lightbox)

時間序列深入解析預覽字詞編輯器使用以下參數：

**Where**：使用 where 子句，藉由使用下表所列的運算元集合來快速篩選事件。 如果透過選取運算元進行搜尋，述詞會依據該搜尋自動更新。 支援的運算元類型包括下列各項：

| 操作 | 支援的類型   | 備註 |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | Double、DateTime、TimeSpan | |
| `=`, `!=`, `<>` | String、Bool、Double、DateTime、TimeSpan、NULL |
| `IN` | String、Bool、Double、DateTime、TimeSpan、NULL | 所有的運算元都應該是相同的類型或是 NULL 常數。 |
| `HAS` | String | 右側只允許常數位串常值。 不允許空字串和 Null。 |

若要深入瞭解支援的查詢作業和資料類型，請參閱[時間序列運算式（TSX）](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)。

### <a name="examples-of-where-clauses"></a>Where 子句範例

  [![Where 子句範例](media/v2-update-explorer/tsi-preview-example-queries.png)](media/v2-update-explorer/tsi-preview-example-queries.png#lightbox)

**量值**：顯示所有數值資料行（**雙精度**浮點數）的下拉式清單，您可以用來做為目前圖表的元素。

**分割依據**：此下拉式清單會顯示您模型中的所有可用類別資料行（字串），您可以將其分組。 您最多可以在同一個 X 軸上加入五個詞彙。 輸入您想要的參數，然後選取 [**新增**] 以新增新的字詞。

  [![查詢和篩選的視圖一](media/v2-update-explorer/s1-s2-preview-filtered-view.png)](media/v2-update-explorer/s1-s2-preview-filtered-view.png#lightbox)

您可以藉由選取可見圖示來顯示和隱藏 [圖表] 面板中的元素，如下圖所示。 若要完全移除查詢，請選取紅色**X**。

  [![取消查詢和篩選的選項](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png)](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png#lightbox)

## <a name="next-steps"></a>後續步驟

- 瞭解如何將[資料內嵌](./concepts-ingestion-overview.md)至您的環境。

- 查看[儲存體](concepts-storage.md)上的文章

- 閱讀有關[資料模型](./concepts-model-overview.md)化的時間序列深入解析預覽檔。

- 瞭解[如何診斷和疑難排解](./time-series-insights-update-how-to-troubleshoot.md)您的時間序列深入解析實例。
