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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77658008"
---
# <a name="workbook-time-parameters"></a>活頁簿時間參數

時間參數可讓使用者設定分析的時間內容，並供幾乎所有報表使用。 設定和使用的方式相當簡單-允許作者指定要在下拉式選單中顯示的時間範圍，包括自訂時間範圍的選項。 

## <a name="creating-a-time-parameter"></a>建立時間參數
1. 從編輯模式中的空白活頁簿開始。
2. 從活頁簿內的連結選擇 [_加入參數_]。
3. 按一下藍色的 [_加入參數_] 按鈕。
4. 在彈出的 [新增參數] 窗格中輸入：
    1. 參數名稱：`TimeRange`
    2. 參數類型：`Time range picker`
    3. 必填：`checked`
    4. 可用的時間範圍：過去1小時，過去12小時，過去24小時，過去48小時，過去3天，過去7天，允許自訂時間範圍選取
5. 從工具列選擇 [儲存] 以建立參數。

    ![顯示時間範圍參數建立的影像](./media/workbooks-time/time-settings.png)

這是活頁簿在讀取模式中看起來的樣子。

![在讀取模式中顯示時間範圍參數的影像](./media/workbooks-time/parameters-time.png)

## <a name="referencing-a-time-parameter"></a>參考時間參數
### <a name="via-bindings"></a>Via 系結
1. 將查詢控制項加入至活頁簿，然後選取 Application Insights 資源。
2. 大部分的活頁簿控制項都支援_時間範圍_範圍選取器。 開啟 [_時間範圍_] 下拉式選單，並`{TimeRange}`在底部的 [時間鈴聲響起人數參數] 群組中選取。
3. 這會將時間範圍參數系結至圖表的時間範圍。 範例查詢的時間範圍現在是過去24小時。
4. 執行查詢以查看結果

    ![顯示透過系結參考之時間範圍參數的影像](./media/workbooks-time/time-binding.png)

### <a name="in-kql"></a>在 KQL 中
1. 將查詢控制項加入至活頁簿，然後選取 Application Insights 資源。
2. 在 KQL 中，使用參數輸入時間範圍篩選準則：`| where timestamp {TimeRange}`
3. 這會將查詢評估時間展開`| where timestamp > ago(1d)`為，這是參數的時間範圍值。
4. 執行查詢以查看結果

    ![顯示 KQL 中參考之時間範圍的影像](./media/workbooks-time/time-in-code.png)

### <a name="in-text"></a>在文字中 
1. 將文字控制項加入至活頁簿。
2. 在 markdown 中，輸入`The chosen time range is {TimeRange:label}`
3. 選擇 [_完成編輯_]
4. 文字控制項將會顯示文字：_所選的時間範圍是過去24小時_

## <a name="time-parameter-options"></a>時間參數選項
| 參數 | 說明 | 範例 |
| ------------- |:-------------|:-------------|
| `{TimeRange}` | 時間範圍標籤 | 過去 24 小時 |
| `{TimeRange:label}` | 時間範圍標籤 | 過去 24 小時 |
| `{TimeRange:value}` | 時間範圍值 | > 前（1d） |
| `{TimeRange:query}` | 時間範圍查詢 | > 前（1d） |
| `{TimeRange:start}` | 時間範圍開始時間 | 下午 3/20/2019 4:18 |
| `{TimeRange:end}` | 時間範圍結束時間 | 下午 3/21/2019 4:18 |
| `{TimeRange:grain}` | 時間範圍細微性 | 30 m |


### <a name="using-parameter-options-in-a-query"></a>在查詢中使用參數選項
```kusto
requests
| make-series Requests = count() default = 0 on timestamp from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain}
```

## <a name="next-steps"></a>後續步驟

* [開始深入](workbooks-visualizations.md)瞭解活頁簿許多豐富的視覺效果選項。
* [控制](workbooks-access-control.md)和共用您的活頁簿資源的存取權。
