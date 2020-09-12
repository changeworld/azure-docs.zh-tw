---
title: Azure 監視器活頁簿圖形視覺效果
description: 瞭解所有 Azure 監視器活頁簿圖形視覺效果。
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: a538eaf06013dcce6529c91816b419159a2600a1
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89664029"
---
# <a name="graph-visualizations"></a>圖形視覺效果

活頁簿支援根據記錄檔中的資料來視覺化任意圖形，以顯示監視實體之間的關聯性。

下圖顯示透過各種埠進出外部電腦的資料流程入/流出電腦。 它是依類型 (電腦與外部 IP) 的色彩，而邊緣大小則對應至之間流動的資料量。 基礎資料來自以 VM 連接為目標的 KQL 查詢。

[![磚摘要視圖的螢幕擷取畫面](./media/workbooks-graph-visualizations/graph.png)](./media/workbooks-graph-visualizations/graph.png#lightbox)

## <a name="adding-a-graph"></a>新增圖形
1. 按一下 [ _編輯_ ] 工具列專案，將活頁簿切換至編輯模式。
2. 使用 [ **加入查詢** ] 連結可將記錄查詢控制項加入至活頁簿。
3. 選取 [ **記錄**]、[資源類型] 作為 [查詢類型] (例如 Application Insights) 以及要作為目標的資源。
4. 使用查詢編輯器來輸入您分析的 KQL。

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

5. 將視覺效果設為 **圖形**
6. 選取 [ **圖形設定** ] 按鈕以開啟 [設定] 窗格
7. 在底部的 _版面配置欄位_ 中，設定：
    * 節點識別碼： `Id`
    * 來源識別碼： `SourceId`
    * 目標識別碼： `TargetId`
    * Edge 標籤： `None`
    * Edge 大小： `Calls`
    * 節點大小： `None`
    * 著色類型： `Categorical`
    * 節點色彩欄位： `Kind`
    * 調色板： `Pastel`
8. 在頂端的 _節點格式_ 設定中，設定：
    * _最上層內容_-使用資料行：、資料行轉譯器 `Name` ： `Text`
    * _置中內容_-使用資料行：，資料行轉譯器 `Calls` ： `Big Number` ，色調色板： `None`
    * _底部內容_-使用資料行：、資料行轉譯器 `Kind` ： `Text`
9. 選取窗格底部的 [ _儲存後關閉_ ] 按鈕。

[![具有上述查詢和設定之磚摘要視圖的螢幕擷取畫面。](./media/workbooks-graph-visualizations/graph-settings.png)](./media/workbooks-graph-visualizations/graph-settings.png#lightbox)

## <a name="graph-settings"></a>圖形設定

| 設定         | 說明                                                                                                        |
|:----------------|:-------------------------------------------------------------------------------------------------------------------|
| `Node Id`       | 選取提供圖形上節點唯一識別碼的資料行。 資料行的值可以是字串或數位。 |
| `Source Id`     | 選取提供圖形邊緣之來源節點識別碼的資料行。 值必須對應至 [ _節點識別碼_ ] 資料行中的值。 |
| `Target Id`     | 選取資料行，以提供圖表邊緣的目標節點識別碼。 值必須對應至 [ _節點識別碼_ ] 資料行中的值。 |
| `Edge Label`    | 選取在圖形上提供邊緣標籤的資料行。                                                            |
| `Edge Size`     | 選取一個資料行，此資料行提供 edge 寬度將依據的度量。                                |
| `Node Size`     | 選取一個資料行，此資料行提供節點區域所依據的度量。                                 |
| `Coloring Type` | 用來選擇節點色彩配置。                                                                            |

## <a name="node-coloring-types"></a>節點著色類型

| 色彩類型 | 說明 |
|:------------- |:------------|
| `None`        | 所有節點都有相同的色彩。 |
| `Categorical` | 節點會根據結果集中資料行的值或類別來指派色彩。 在上述範例中，色彩是以結果集的資料行 _類型_ 為基礎。 支援的調色板為 `Default` 、 `Pastel` 和 `Cool tone` 。  |
| `Field Based` | 在此類型中，資料行提供要用於節點的特定 RGB 值。 提供最大的彈性，但通常需要更多的工作才能啟用。  |

## <a name="node-format-settings"></a>節點格式設定

圖形作者可以指定要將哪些內容移至節點的不同部分：頂端、左方、置中、靠右和底部。 圖形可以使用任何轉譯器活頁簿支援 (文字、大數位、spark 線條、圖示等 ) 。

## <a name="field-based-node-coloring"></a>以欄位為基礎的節點著色

1. 按一下 [ _編輯_ ] 工具列專案，將活頁簿切換至編輯模式。
2. 使用 [ **加入查詢** ] 連結可將記錄查詢控制項加入至活頁簿。
3. 將查詢類型選取為 [ **記錄**]、[資源類型] (例如 Application Insights) ，以及要作為目標的資源。
4. 使用查詢編輯器來輸入您分析的 KQL。

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
    | project Id = App, Name = App, Calls, Color = 'FD7F23'
    | union (data
        | summarize Calls = sum(Calls) by RequestId, Request
        | project Id = RequestId, Name = Request, Calls, Color = 'B3DE8E')
    | union (data
        | summarize Calls = sum(Calls) by Dependency
        | project Id = Dependency, Name = Dependency, Calls, Color = 'C9B3D5');
    nodes
    | union (links)
    ```
5. 將視覺效果設為 *圖形*
6. 選取 [ **圖形設定**  ] 按鈕以開啟 [設定] 窗格。
7. 在底部的 *版面配置欄位* 中，設定：
    * 節點識別碼：`Id`
    * 來源識別碼： `SourceId`
    * 目標識別碼： `TargetId`
    * Edge 標籤： `None`
    * Edge 大小： `Calls`
    * 節點大小： `Node`
    * 著色類型： `Field Based`
    * 節點色彩欄位： `Color`
8. 在頂端的 [ *節點格式] 設定* 中，輸入下列各項。
    * 在 *頂端內容*中，設定：
        * 使用資料行： `Name` 。
        * 資料行轉譯器： `Text` 。
    * 在 *中央內容*中，設定：
        * 使用資料行： `Calls`
        * 資料行轉譯器： `Big Number`
        * 調色板： `None`
    * 在 *底部內容*中，設定：
        * 使用資料行： `Kind`
        * 資料行轉譯器： `Text` 。
9. 選取窗格底部的 [ **儲存後關閉] 按鈕** 。

[![顯示如何使用 [欄位基底] 節點著色建立圖形視覺效果的螢幕擷取畫面。](./media/workbooks-graph-visualizations/graph-field-based.png)](./media/workbooks-graph-visualizations/graph-field-based.png#lightbox)

## <a name="next-steps"></a>接下來的步驟

* 圖形也支援複合條形轉譯器。 若要深入瞭解，請造訪 [複合 Bar 檔](workbooks-composite-bar.md)。
* 深入瞭解您可以在活頁簿中使用的 [資料來源](workbooks-data-sources.md) 。
