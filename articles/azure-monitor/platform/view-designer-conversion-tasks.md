---
title: Azure 監視器視圖設計工具轉換為活頁簿的一般工作
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 5559dac916262998d621b40757398088ec613609
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77658739"
---
# <a name="view-designer-to-workbooks-conversion-common-tasks"></a>將設計工具轉換成活頁簿轉換的一般工作
「[視圖設計](view-designer.md)工具」是 Azure 監視器的功能，可讓您建立自訂的視圖，以協助您將 Log Analytics 工作區中的資料視覺化，其中包含圖表、清單和時間軸。 它們會被淘汰，並取代為提供額外功能的活頁簿。 本文詳細說明將 views 轉換成活頁簿時常見的工作。


## <a name="quickstart-with-preset-view-designer-templates"></a>使用預設視圖設計工具範本的快速入門

Log Analytics 工作區中的活頁簿已經有範本，可以符合視圖設計工具中的某些視圖。 在 [**視圖設計工具輔助線**] 類別底下，選取 [**視圖設計工具轉換指南**] 以瞭解您的選項，或選取其中一個預設範本。

![範本的範例](media/view-designer-conversion-tasks/templates.png)

## <a name="enabling-time-range-filter"></a>啟用時間範圍篩選
View designer 具有內建的預設時間範圍篩選器，不過，在活頁簿中，預設不會啟用此設定。 活頁簿可讓使用者建立自己的時間範圍篩選器，其資料記錄可能更適用。 產生篩選的步驟如下所示：

選取 [**新增參數**] 選項。 預設**樣式**是設定為*藥丸*。

![新增參數](media/view-designer-conversion-tasks/add-param.png)

 選取 [**新增參數**] 按鈕。

![新增參數](media/view-designer-conversion-tasks/add-parameter.png)

在 [提要欄位] 功能表的 [**參數名稱**] 文字方塊中，輸入*TimeRange*。 將**參數類型**設定為*時間範圍選擇器*。 選取 [**必要？** ] 核取方塊。

![參數功能表](media/view-designer-conversion-tasks/parameter-menu.png)

將參數儲存在側邊欄功能表的左上角。 根據預設，您可以將下拉式清單*保留為 [* 未設定]，或選取預設的**TimeRange**值，例如*24 小時*。 選取 [**完成編輯**]。

在 {} 您的參數名稱前後加上大括弧，即可在查詢中使用參數。 如需參數的詳細資訊，請參閱[參數的活頁簿檔](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Documentation/Parameters/Parameters.md)。

## <a name="updating-queries-with-the-timerange-parameter"></a>以 TimeRange 參數更新查詢

### <a name="option-1-select-timerange-from-the-time-range-dropdown"></a>選項1：從 [時間範圍] 下拉式清單中選取 [TimeRange]

![Time 參數](media/view-designer-conversion-tasks/time-parameter.png)

### <a name="option-2-update-your-log-queries"></a>選項2：更新您的記錄查詢

在您的查詢中新增行： `| where TimeGenerated {TimeRange}` ，如下列範例所示：

原始查詢
```KQL
search * 
| summarize count() by Type
```

已更新查詢
```KQL
search * 
| where TimeGenerated {TimeRange} 
| summarize count() by Type
```

## <a name="including-a-list"></a>包含清單
大部分的視圖設計工具視圖都包含清單，您可以在活頁簿中重現此標準清單。

![磚清單](media/view-designer-conversion-tasks/tile-list.png)

按一下 [資料格選項] 中的 [**加入查詢**] 來加入視覺效果。

![新增參數](media/view-designer-conversion-tasks/add-param.png)

視圖設計工具會使用預設查詢，以符合原始範例中的語法。 這可以藉由將查詢變更為更新後的格式來進行更新，如下列範例所示：

原始查詢
```KQL
search * 
| summarize AggregatedValue = count() by Type
```

已更新查詢
```KQL
search * 
| summarize Count = count() by Type
```

這會產生類似下列的清單：

![清單範例](media/view-designer-conversion-tasks/list-example.png)

## <a name="enabling-sparklines"></a>啟用走勢圖
格線的常見功能是在一段時間後新增走勢圖來摘要各種資料模式。 [視圖設計工具] 提供所有清單的 [**啟用**走勢圖] 功能，就像活頁簿一樣。 若要在您的資料中包含符合 view designer 的走勢圖，請使用原始查詢來聯結資料，如下列範例所示：

原始查詢
```KQL
search *
| summarize AggregatedValue = count() by Type) on Type
```

已更新查詢
```KQL
search * 
| summarize AggregatedValue = count() by Type
| join kind = inner (search * 
    | make-series Trend = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type) on Type
| project Type, AggregatedValue, Trend
```

選取 [資料**行設定**]。
![資料行設定](media/view-designer-conversion-tasks/column-settings.png)

將 [資料**行**轉譯器] 下拉式清單更新為*Spark 區域*。
![走勢圖](media/view-designer-conversion-tasks/sparkline.png)

儲存設定並再次執行查詢，以更新您的資料表以包含走勢圖。

產生的方格看起來會像下面這樣：走勢 ![ 圖範例](media/view-designer-conversion-tasks/sparkline-example.png)

## <a name="advanced-cell-settings"></a>先進的資料格設定
在 [鏡像視圖設計工具] 中，您可以執行工作，例如變更活頁簿儲存格的大小，或將釘選和外部連結加入至記錄。

若要存取 [**高級設定**]，請選取每個資料格底部的齒輪圖示。

![進階設定](media/view-designer-conversion-tasks/advanced-settings.png)

這會顯示功能表，其中包含各種選項：

![Advanced settings 設定](media/view-designer-conversion-tasks/advanced-settings-settings.png)

若要加入 pin 和外部查詢的連結，請選取對應的核取方塊。 若要將標題加入至您的儲存格，請在 [**圖表標題**] 區段中輸入想要的標題。

根據預設，任何活頁簿資料格都會設定為佔用整個頁面寬度，但是您可以在 [**高級設定**] 功能表的 [**樣式**] 索引標籤底下相應縮小資料格，來調整大小。

![Advanced 設定樣式](media/view-designer-conversion-tasks/advanced-settings-style.png)

 
## <a name="additional-parameters"></a>其他參數
選取 [**新增參數**]，在您的活頁簿中建立新的參數。 

若要選取訂用帳戶，請在側邊功能表的 [**參數名稱**] 欄位中輸入*訂*用帳戶，然後從**參數類型**下拉式清單中選取*訂*用

![訂用帳戶功能表](media/view-designer-conversion-tasks/subscription-filter.png)

若要選取資源，請在側邊功能表的 [**參數名稱**] 欄位中輸入*資源*，然後從 [**參數類型**] 下拉式清單中選取 [*資源選擇器*]。

![資源功能表](media/view-designer-conversion-tasks/resource-filter.png)

這會插入下拉清單，讓您存取各種訂用帳戶和資源。

![訂用帳戶資源下拉式清單](media/view-designer-conversion-tasks/subscription-resource.png)


## <a name="next-steps"></a>後續步驟
- [圖格轉換](view-designer-conversion-tiles.md)
