---
title: Azure 監視器活頁簿時間參數
description: 使用預先建立及使用自訂參數之活頁簿來簡化複雜的報告
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 380b8a7ce286ab06b6935bf63bf3a0e82f371c2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658008"
---
# <a name="workbook-time-parameters"></a>活頁簿時間參數

時間參數允許使用者設置分析的時間上下文，並且幾乎所有報表都使用。 設置和使用相對簡單 - 允許作者指定要在下拉清單中顯示的時間範圍，包括自訂時間範圍的選項。 

## <a name="creating-a-time-parameter"></a>創建時間參數
1. 從編輯模式下的空活頁簿開始。
2. 從活頁簿中的連結中選擇 _"添加參數_"。
3. 按一下藍色 _"添加參數"_ 按鈕。
4. 在彈出的新參數窗格中，輸入：
    1. 參數名稱：`TimeRange`
    2. 參數類型：`Time range picker`
    3. 必填：`checked`
    4. 可用時間範圍：最後一小時、最後 12 小時、最近 24 小時、最近 48 小時、最近 3 天、最近 7 天以及允許自訂時間範圍選擇
5. 從工具列中選擇"保存"以創建參數。

    ![顯示時間範圍參數創建的圖像](./media/workbooks-time/time-settings.png)

這就是活頁簿在閱讀模式下的樣子。

![在讀取模式下顯示時間範圍參數的圖像](./media/workbooks-time/parameters-time.png)

## <a name="referencing-a-time-parameter"></a>引用時間參數
### <a name="via-bindings"></a>通過綁定
1. 向活頁簿添加查詢控制項並選擇應用程式見解資源。
2. 大多數活頁簿控制項都支援_時間範圍_選取器。 打開_時間範圍_下拉清單，`{TimeRange}`並在底部選擇時間鈴參數組。
3. 這將時間範圍參數綁定到圖表的時間範圍。 依例查詢的時間範圍現在是最近 24 小時。
4. 執行查詢以查看結果

    ![顯示通過綁定引用的時間範圍參數的圖像](./media/workbooks-time/time-binding.png)

### <a name="in-kql"></a>在 KQL 中
1. 向活頁簿添加查詢控制項並選擇應用程式見解資源。
2. 在 KQL 中，使用 參數輸入時間範圍篩選器：`| where timestamp {TimeRange}`
3. 這將在查詢評估時間上擴展為`| where timestamp > ago(1d)`，這是參數的時間範圍值。
4. 執行查詢以查看結果

    ![顯示 KQL 中引用的時間範圍的圖像](./media/workbooks-time/time-in-code.png)

### <a name="in-text"></a>在文本中 
1. 向活頁簿添加文本控制項。
2. 在標記下，輸入`The chosen time range is {TimeRange:label}`
3. 選擇 _"已完成編輯"_
4. 文本控制項將顯示文本：_所選時間範圍為"過去 24 小時"_

## <a name="time-parameter-options"></a>時間參數選項
| 參數 | 說明 | 範例 |
| ------------- |:-------------|:-------------|
| `{TimeRange}` | 時間範圍標籤 | 過去 24 小時 |
| `{TimeRange:label}` | 時間範圍標籤 | 過去 24 小時 |
| `{TimeRange:value}` | 時間範圍值 | >前（1d） |
| `{TimeRange:query}` | 時間範圍查詢 | >前（1d） |
| `{TimeRange:start}` | 時間範圍開始時間 | 2019/3/20 下午 4：18 |
| `{TimeRange:end}` | 時間範圍結束時間 | 2019/3/21 下午 4：18 |
| `{TimeRange:grain}` | 時間範圍顆粒 | 30 米 |


### <a name="using-parameter-options-in-a-query"></a>在查詢中使用參數選項
```kusto
requests
| make-series Requests = count() default = 0 on timestamp from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain}
```

## <a name="next-steps"></a>後續步驟

* [開始](workbooks-visualizations.md)瞭解有關活頁簿的許多豐富視覺化選項的詳細資訊。
* [控制和](workbooks-access-control.md)共用對活頁簿資源的存取權限。
