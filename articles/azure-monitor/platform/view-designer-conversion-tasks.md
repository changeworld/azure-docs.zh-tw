---
title: Azure 監視器視圖設計器到活頁簿轉換常見任務
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 5559dac916262998d621b40757398088ec613609
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658739"
---
# <a name="view-designer-to-workbooks-conversion-common-tasks"></a>查看設計器以活頁簿轉換常見任務
[視圖設計器](view-designer.md)是 Azure 監視器的一項功能，允許您創建自訂視圖，以説明您在日誌分析工作區中視覺化資料，包括圖表、清單和時間表。 它們正在逐步淘汰，代之以提供額外功能的活頁簿。 本文詳細介紹了將視圖轉換為活頁簿時常見的任務。


## <a name="quickstart-with-preset-view-designer-templates"></a>使用預設視圖設計器範本快速入門

日誌分析工作區中的活頁簿已經具有範本，以匹配視圖設計器中的某些視圖。 在 **"查看設計器指南"** 類別下，選擇 **"查看設計器過渡指南"** 以瞭解您的選項或選擇其中一個預設範本。

![範本的範例](media/view-designer-conversion-tasks/templates.png)

## <a name="enabling-time-range-filter"></a>啟用時間範圍篩選器
視圖設計器具有內置的預設時間範圍篩選器，但是，在活頁簿中，預設情況下不會啟用此設置。 活頁簿允許使用者創建自己的時間範圍篩選器，這些篩選器可能更適用于其資料日誌。 生成篩選器的步驟如下所示：

選擇"**添加參數"** 選項。 預設**樣式**設置為 *"丸"。*

![添加 Param](media/view-designer-conversion-tasks/add-param.png)

 選擇"**添加參數**"按鈕。

![加入參數](media/view-designer-conversion-tasks/add-parameter.png)

從側邊欄功能表中，在**參數名稱**文字方塊中鍵入*TimeRange*。 將**參數類型**設置為*時間範圍選取器*。 選擇"**必需"** 核取方塊。

![參數功能表](media/view-designer-conversion-tasks/parameter-menu.png)

將參數保存在側邊欄功能表的左上角。 預設情況下，您可以將下拉清單保留為*未設置*，或選擇預設**時間範圍**值，如*24 小時*。 選擇 **"完成編輯**"。

參數可以通過{}在參數名稱周圍添加大括弧在查詢中使用。 有關參數的更多詳細資訊，請參閱[有關參數的活頁簿文檔](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Documentation/Parameters/Parameters.md)。

## <a name="updating-queries-with-the-timerange-parameter"></a>使用時間範圍參數更新查詢

### <a name="option-1-select-timerange-from-the-time-range-dropdown"></a>選項 1：從時間範圍下拉清單中選擇時間範圍

![時間參數](media/view-designer-conversion-tasks/time-parameter.png)

### <a name="option-2-update-your-log-queries"></a>選項 2：更新日誌查詢

在查詢中添加行：`| where TimeGenerated {TimeRange}`如以下示例所示：

原始查詢
```KQL
search * 
| summarize count() by Type
```

更新查詢
```KQL
search * 
| where TimeGenerated {TimeRange} 
| summarize count() by Type
```

## <a name="including-a-list"></a>包括清單
大多數視圖設計器視圖都包含一個清單，您可以在活頁簿中重現此標準清單。

![磁貼清單](media/view-designer-conversion-tasks/tile-list.png)

通過按一下"從儲存格選項**添加查詢**"來添加視覺化效果。

![添加 Param](media/view-designer-conversion-tasks/add-param.png)

視圖設計器使用與原始示例中的語法匹配的預設查詢。 可以通過將查詢更改為更新的表單來更新，如以下示例所示：

原始查詢
```KQL
search * 
| summarize AggregatedValue = count() by Type
```

更新查詢
```KQL
search * 
| summarize Count = count() by Type
```

這將生成如下所示的清單：

![清單示例](media/view-designer-conversion-tasks/list-example.png)

## <a name="enabling-sparklines"></a>啟用火花線
網格的一個常見功能是添加火花線來匯總隨時間變化的各種資料模式。 視圖設計器為所有清單提供**啟用 Sparklines**功能，活頁簿也是如此。 要在資料中包含與視圖設計器匹配的火花線，請將資料與原始查詢聯接，如以下示例所示：

原始查詢
```KQL
search *
| summarize AggregatedValue = count() by Type) on Type
```

更新查詢
```KQL
search * 
| summarize AggregatedValue = count() by Type
| join kind = inner (search * 
    | make-series Trend = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type) on Type
| project Type, AggregatedValue, Trend
```

選擇**列設置**。
![列設置](media/view-designer-conversion-tasks/column-settings.png)

將**列渲染器**下拉清單更新為*Spark 區域*。
![走勢圖](media/view-designer-conversion-tasks/sparkline.png)

保存設置並再次執行查詢以更新表以包含一個火花線。

生成的網格將類似于以下內容：Sparkline![示例](media/view-designer-conversion-tasks/sparkline-example.png)

## <a name="advanced-cell-settings"></a>高級儲存格設置
要鏡像視圖設計器，可以執行更改活頁簿儲存格大小或向日志添加引腳和外部連結等任務。

要訪問 **"高級設置"，** 請選擇每個單元底部的齒輪圖示。

![進階設定](media/view-designer-conversion-tasks/advanced-settings.png)

這將顯示包含各種選項的功能表：

![高級設置設置](media/view-designer-conversion-tasks/advanced-settings-settings.png)

要添加指向外部查詢的引腳和連結，請選擇相應的核取方塊。 要向儲存格添加標題，請將所需的標題鍵入 **"圖表標題**"部分。

預設情況下，任何活頁簿儲存格都設置為佔用整個頁面寬度，但您可以通過在 **"高級設置"** 功能表的 **"樣式"** 選項卡下向下縮放儲存格來調整此頁

![高級設置樣式](media/view-designer-conversion-tasks/advanced-settings-style.png)

 
## <a name="additional-parameters"></a>其他參數
選擇 **"添加參數**"在活頁簿中創建新參數。 

要選擇訂閱，請在側功能表中的 **"參數名稱**"欄位中鍵入 *"訂閱"，* 然後從 **"參數類型**"下拉清單中選擇 *"訂閱選取器*"

![訂閱功能表](media/view-designer-conversion-tasks/subscription-filter.png)

要選擇"資源"，請在側功能表中的 **"參數名稱**"欄位中鍵入 *"資源"，* 然後從 **"參數類型**"下拉下下下下下下選點選擇 *"資源選取器*"。

![資源功能表](media/view-designer-conversion-tasks/resource-filter.png)

這將插入下拉清單，以便您訪問各種訂閱和資源。

![訂閱資源下拉清單](media/view-designer-conversion-tasks/subscription-resource.png)


## <a name="next-steps"></a>後續步驟
- [磁貼轉換](view-designer-conversion-tiles.md)
