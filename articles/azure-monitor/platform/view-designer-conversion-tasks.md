---
title: Azure 監視器視圖設計工具轉換為活頁簿轉換的一般工作
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 5559dac916262998d621b40757398088ec613609
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "77658739"
---
# <a name="view-designer-to-workbooks-conversion-common-tasks"></a>視圖設計工具到活頁簿轉換的一般工作
[View designer](view-designer.md) 是 Azure 監視器的功能，可讓您建立自訂視圖，以協助您在 Log Analytics 工作區中以圖表、清單和時程表來視覺化資料。 它們會被淘汰並取代為提供額外功能的活頁簿。 本文將詳細說明將視圖轉換成活頁簿時常見的工作。


## <a name="quickstart-with-preset-view-designer-templates"></a>使用預設 view designer 範本的快速入門

Log Analytics 工作區中的活頁簿已經有範本，可比對視圖設計工具中的一些觀點。 在 [ **視圖設計師輔助線** ] 類別下，選取 [ **View designer 轉換指南** ] 以瞭解您的選項，或選取其中一個預設範本。

![範本的範例](media/view-designer-conversion-tasks/templates.png)

## <a name="enabling-time-range-filter"></a>啟用時間範圍篩選
View designer 有內建的預設時間範圍篩選器，不過，在活頁簿中，預設不會啟用此設定。 活頁簿可讓使用者建立自己的時間範圍篩選器，這些篩選器可能更適合其資料記錄。 以下列出產生篩選準則的步驟：

選取 [ **加入參數** ] 選項。 預設 **樣式** 設定為 [ *框之間*]。

![新增參數](media/view-designer-conversion-tasks/add-param.png)

 選取 [ **加入參數** ] 按鈕。

![新增參數](media/view-designer-conversion-tasks/add-parameter.png)

從側邊欄功能表的 [ **參數名稱** ] 文字方塊中，輸入 *TimeRange*。 將 **參數類型** 設定為 *時間範圍選擇器*。 選取 [ **必要？** ] 核取方塊。

![參數功能表](media/view-designer-conversion-tasks/parameter-menu.png)

將參數儲存在側邊欄功能表的左上角。 您 *可以依預設* 將下拉式清單保持為 [未設定]，或選取預設的 **TimeRange** 值，例如 *24 小時*。 選取 [ **完成編輯**]。

您可以在參數名稱前後加上大括弧，以在查詢中使用參數 {} 。 如需參數的詳細資訊，請參閱活頁 [簿檔](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Documentation/Parameters/Parameters.md)中的參數。

## <a name="updating-queries-with-the-timerange-parameter"></a>使用 TimeRange 參數更新查詢

### <a name="option-1-select-timerange-from-the-time-range-dropdown"></a>選項1：從 [時間範圍] 下拉式清單選取 [TimeRange]

![Time 參數](media/view-designer-conversion-tasks/time-parameter.png)

### <a name="option-2-update-your-log-queries"></a>選項2：更新您的記錄查詢

在您的查詢中加入這一行： `| where TimeGenerated {TimeRange}` 如下列範例所示：

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
大部分的視圖設計工具視圖都包含清單，您可以在活頁簿中重現這個標準清單。

![磚清單](media/view-designer-conversion-tasks/tile-list.png)

按一下 [資料格選項] 中的 [ **加入查詢** ]，以新增視覺效果。

![新增參數](media/view-designer-conversion-tasks/add-param.png)

View designer 採用的預設查詢符合原始範例中的語法。 將查詢變更為更新的表單即可更新，如下列範例所示：

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
格線的常見功能是加入走勢圖，以摘要一段時間的各種資料模式。 View designer 提供所有清單的 **啟用** 走勢圖功能，如同活頁簿一樣。 若要在資料中包含符合 view designer 的走勢圖，請使用您的原始查詢來聯結資料，如下列範例所示：

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

選取 [資料 **行設定**]。
![資料行設定](media/view-designer-conversion-tasks/column-settings.png)

將 [資料 **行** 轉譯器] 下拉式清單更新為 *Spark 區域*。
![走勢圖](media/view-designer-conversion-tasks/sparkline.png)

儲存設定並再次執行查詢，以更新您的資料表以包含走勢圖。

產生的方格看起來會像下面這樣：走勢 ![ 圖範例](media/view-designer-conversion-tasks/sparkline-example.png)

## <a name="advanced-cell-settings"></a>Advanced 資料格設定
若要鏡像視圖設計工具，您可以執行工作，例如變更活頁簿資料格的大小，或將釘選和外部連結新增至記錄。

若要存取 **Advanced Settings** ，請選取每個資料格底部的齒輪圖示。

![進階設定](media/view-designer-conversion-tasks/advanced-settings.png)

這會顯示具有各種選項的功能表：

![Advanced settings 設定](media/view-designer-conversion-tasks/advanced-settings-settings.png)

若要加入 pin 和外部查詢的連結，請選取對應的核取方塊。 若要將標題加入至儲存格，請在 [ **圖表標題** ] 區段中輸入所需的標題。

依預設，所有的活頁簿資料格都會設定為佔用整個頁面寬度，但您可以調整其大小，方法是在 [ **Advanced Settings** ] 功能表的 [**樣式**] 索引標籤底下調整資料格

![Advanced settings 樣式](media/view-designer-conversion-tasks/advanced-settings-style.png)

 
## <a name="additional-parameters"></a>其他參數
選取 [ **加入參數** ]，在您的活頁簿中建立新的參數。 

若要選取訂用帳戶，請在側邊功能表的 [**參數名稱**] 欄位中輸入*訂*用帳戶，然後從 [**參數類型**] 下拉式清單選取 [*訂閱*

![訂用帳戶功能表](media/view-designer-conversion-tasks/subscription-filter.png)

若要選取資源，請在側邊功能表的 [**參數名稱**] 欄位中輸入*資源*，然後從 [**參數類型**] 下拉式清單中選取 [*資源選擇器*]。

![資源功能表](media/view-designer-conversion-tasks/resource-filter.png)

這會插入下拉式清單，讓您可以存取各種訂用帳戶和資源。

![訂用帳戶資源下拉式清單](media/view-designer-conversion-tasks/subscription-resource.png)


## <a name="next-steps"></a>後續步驟
- [圖格轉換](view-designer-conversion-tiles.md)
