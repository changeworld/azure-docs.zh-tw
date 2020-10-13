---
title: Azure 監視器活頁簿時間參數
description: 瞭解如何設定時間參數，以允許使用者設定分析的時間內容。 幾乎所有報表都會使用時間參數。
services: azure-monitor
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: 471b775abfebec646ab3950803622271c0bd559b
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91932289"
---
# <a name="workbook-time-parameters"></a>活頁簿時間參數

時間參數可讓使用者設定分析的時間內容，而且幾乎所有報表都會使用此內容。 設定和使用-允許作者指定要在下拉式清單中顯示的時間範圍，包括自訂時間範圍的選項，這相當簡單。 

## <a name="creating-a-time-parameter"></a>建立時間參數
1. 在編輯模式中，從空白的活頁簿開始。
2. 從活頁簿內的連結選擇 [ _加入參數_ ]。
3. 按一下藍色的 [ _加入參數_ ] 按鈕。
4. 在彈出的新參數窗格中輸入：
    1. 參數名稱： `TimeRange`
    2. 參數類型： `Time range picker`
    3. 必填： `checked`
    4. 可用的時間範圍：過去一小時、過去12小時、過去24小時、過去48小時、過去3天、過去7天，並允許自訂時間範圍選取
5. 從工具列中選擇 [儲存] 以建立參數。

    ![顯示建立時間範圍參數的影像](./media/workbooks-time/time-settings.png)

這就是活頁簿在讀取模式中的樣子。

![以讀取模式顯示時間範圍參數的影像](./media/workbooks-time/parameters-time.png)

## <a name="referencing-a-time-parameter"></a>參考 time 參數
### <a name="via-bindings"></a>Via 系結
1. 將查詢控制項新增至活頁簿，然後選取 Application Insights 資源。
2. 大部分的活頁簿控制項都支援 _時間範圍_ 範圍選取器。 開啟 [ _時間範圍_ ] 下拉式清單，然後選取 `{TimeRange}` 底部的 [時間鈴聲響起人數參數] 群組中的。
3. 這會將時間範圍參數系結至圖表的時間範圍。 範例查詢的時間範圍現在是過去24小時。
4. 執行查詢以查看結果

    ![顯示透過系結參考之時間範圍參數的影像](./media/workbooks-time/time-binding.png)

### <a name="in-kql"></a>在 KQL 中
1. 將查詢控制項新增至活頁簿，然後選取 Application Insights 資源。
2. 在 KQL 中，使用參數輸入時間範圍篩選準則： `| where timestamp {TimeRange}`
3. 這會將查詢評估時間展開為 `| where timestamp > ago(1d)` ，這是參數的時間範圍值。
4. 執行查詢以查看結果

    ![顯示 KQL 中所參考時間範圍的影像](./media/workbooks-time/time-in-code.png)

### <a name="in-text"></a>在文字中 
1. 將文字控制項加入至活頁簿。
2. 在 markdown 中，輸入 `The chosen time range is {TimeRange:label}`
3. 選擇 [_完成編輯_]
4. 文字控制項將顯示文字： _選擇的時間範圍是過去24小時_

## <a name="time-parameter-options"></a>時間參數選項
| 參數 | 說明 | 範例 |
| ------------- |:-------------|:-------------|
| `{TimeRange}` | 時間範圍標籤 | 過去 24 小時 |
| `{TimeRange:label}` | 時間範圍標籤 | 過去 24 小時 |
| `{TimeRange:value}` | 時間範圍值 | > 前 (1d)  |
| `{TimeRange:query}` | 時間範圍查詢 | > 前 (1d)  |
| `{TimeRange:start}` | 時間範圍開始時間 | 3/20/2019 4:18 PM |
| `{TimeRange:end}` | 時間範圍結束時間 | 3/21/2019 4:18 PM |
| `{TimeRange:grain}` | 時間範圍細微性 | 30 m |


### <a name="using-parameter-options-in-a-query"></a>在查詢中使用參數選項
```kusto
requests
| make-series Requests = count() default = 0 on timestamp from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain}
```

## <a name="next-steps"></a>後續步驟

* [開始深入](workbooks-visualizations.md) 瞭解活頁簿許多豐富的視覺效果選項。
* [控制](workbooks-access-control.md) 和共用活頁簿資源的存取權。
