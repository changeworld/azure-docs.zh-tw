---
ms.openlocfilehash: 0bae8946b7d81fbf45698cefe7c8b00bbc94d00c
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2020
ms.locfileid: "92168100"
---
#### <a name="determine-memory-usage"></a>判斷記憶體使用量 

在 [ **監視**] 底下，選取 [ **記錄 (分析]) **，然後複製下列遙測查詢並貼到查詢視窗中，然後選取 [ **執行**]。 此查詢會傳回每個取樣時間的總記憶體使用量。

```
performanceCounters
| where name == "Private Bytes"
| project timestamp, name, value
```

結果看起來如下列範例所示：

| 時間戳記 \[ UTC\]          | NAME          | value       |
|----------------------------|---------------|-------------|
| 9/12/2019，1:05:14 \. 947 AM | 私用位元組 | 209932288 |
| 9/12/2019，1:06:14 \. 994 AM | 私用位元組 | 212189184 |
| 9/12/2019、1:06:30 \. 010 AM | 私用位元組 | 231714816 |
| 9/12/2019，1:07:15 \. 040 AM | 私用位元組 | 210591744 |
| 9/12/2019，1:12:16 \. 285 AM | 私用位元組 | 216285184 |
| 9/12/2019，1:12:31 \. 376 AM | 私用位元組 | 235806720 |

#### <a name="determine-duration"></a>判斷持續時間 

Azure 監視器會追蹤資源層級的計量，而函式是函數應用程式。 Application Insights 整合會以每個函式為基礎發出度量。 以下是取得函數平均持續時間的範例分析查詢：

```
customMetrics
| where name contains "Duration"
| extend averageDuration = valueSum / valueCount
| summarize averageDurationMilliseconds=avg(averageDuration) by name
```

| NAME                       | averageDurationMilliseconds |
|----------------------------|-----------------------------|
| QueueTrigger AvgDurationMs | 16 \. 087                     |
| QueueTrigger MaxDurationMs | 90 \. 249                     |
| QueueTrigger MinDurationMs | 8 \. 522                      |
