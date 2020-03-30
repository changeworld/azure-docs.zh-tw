---
title: 與參數化 URL 共用自訂視圖 - Azure 時間序列見解 |微軟文檔
description: 瞭解如何創建參數化 URL 以在 Azure 時間序列見解中輕鬆共用自訂資源管理器視圖。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.topic: conceptual
ms.workload: big-data
ms.date: 02/04/2020
ms.custom: seodec18
ms.openlocfilehash: 9dfe499a7d6084a23fd71ab98db472befe71fc04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024359"
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>使用參數化 URL 共用自訂檢視

要在時間序列見解資源管理器中共用自訂視圖，可以以程式設計方式創建自訂視圖的參數化 URL。

時序見解資源管理器支援 URL 查詢參數，以便直接從 URL 指定體驗中的視圖。 例如，僅使用 URL，您就可以指定目標環境、搜尋述詞，以及所需的時間範圍。 當使用者選擇自訂 URL 時，該介面將在時序見解門戶中直接提供指向該資產的連結。 適用資料存取原則。

> [!TIP]
> * 查看免費[時間序列見解演示](https://insights.timeseries.azure.com/samples)。
> * 閱讀隨附的[時間序列見解資源管理器](./time-series-insights-explorer.md)文檔。

## <a name="environment-id"></a>環境識別碼

`environmentId=<guid>` 參數可指定目標環境識別碼。 它是資料訪問 FQDN 的元件，您可以在 Azure 門戶中環境概述的右上角找到它。 這是在 `env.timeseries.azure.com` 之前的所有內容。

範例環境識別碼參數為 `?environmentId=10000000-0000-0000-0000-100000000108`。

## <a name="time"></a>Time

您可以使用參數化 URL 來指定絕對或相對時間值。

### <a name="absolute-time-values"></a>絕對時間值

對於絕對時間值，使用 `from=<integer>` 和 `to=<integer>` 參數。

* `from=<integer>` 是搜尋範圍的開始時間值 (以 JavaScript 毫秒為單位)。
* `to=<integer>` 是搜尋範圍的結束時間值 (以 JavaScript 毫秒為單位)。

> [!TIP]
> 要輕鬆將日期轉換為 JavaScript 毫秒，請嘗試["紀元& Unix 時間戳記轉換器](https://www.freeformatter.com/epoch-timestamp-to-date-converter.html)。

### <a name="relative-time-values"></a>相對時間值

對於相對時間值，請使用`relativeMillis=<value>`，其中*值*以 JavaScript 毫秒為單位，從 API 收到的最新時間戳記。

例如，`&relativeMillis=3600000` 可顯示最近 60 分鐘的資料。

接受的值對應于時間序列見解資源管理器**快速時間**功能表，包括：

* `1800000`（最後30分鐘）
* `3600000`（最後60分鐘）
* `10800000`（持續3小時）
* `21600000`（持續6小時）
* `43200000`（持續 12 小時）
* `86400000`（持續24小時）
* `604800000`（最近7天）
* `2592000000`（過去 30 小時）

### <a name="optional-parameters"></a>選擇性參數

參數`timeSeriesDefinitions=<collection of term objects>`指定將在時序見解視圖中顯示的謂詞：

| 參數 | URL 專案 | 描述 |
| --- | --- | --- |
| **名稱** | `\<string>` | term** 的名稱。 |
| **拆分由** | `\<string>` | split by** 的資料行名稱。 |
| **度量值名稱** | `\<string>` | measure** 的資料行名稱。 |
| **謂詞** | `\<string>` | 用於伺服器端篩選的 where** 子句。 |
| **使用Sum** | `true` | 指定度量值使用總和的可選參數。 |

> [!NOTE]
> 如果`Events`所選的**useSum**度量值，則預設情況下選擇計數。  
> 如果未`Events`選中，則預設選擇平均值。 |

* 鍵`multiChartStack=<true/false>`值對支援在圖表中堆疊。
* 鍵`multiChartSameScale=<true/false>`值對可在可選參數內跨術語實現相同的 Y 軸比例。  
* `timeBucketUnit=<Unit>&timeBucketSize=<integer>`使您能夠調整間隔滑塊，以提供更精細或更平滑、更聚合的圖表視圖。  
* 該`timezoneOffset=<integer>`參數使您能夠將圖表的時區設置為與 UTC 的偏移量。

| 對子） | 描述 |
| --- | --- |
| `multiChartStack=false` | `true`預設情況下啟用，因此通過`false`堆疊。 |
| `multiChartStack=false&multiChartSameScale=true` | 必須啟用堆疊，才能在不同時段使用相同的 Y 軸刻度。  預設情況下，`false`通過`true`啟用此功能。 |
| `timeBucketUnit=<Unit>&timeBucketSize=<integer>` | 單位`days`= `hours` `minutes`、、、、、、、`seconds``milliseconds`單位、  單位一律大寫。 </br> 通過傳遞**時間存儲量大小的**所需整數來定義單位數。  |
| `timezoneOffset=-<integer>` | 整數一律以毫秒為單位。 |

> [!NOTE]
> **時間桶單位**值可平滑長達 7 天。
> **時區偏移**值既不是 UTC 也不是本地時間。

### <a name="examples"></a>範例

要將時間序列定義作為 URL 參數添加到時序見解環境，附加：

```URL parameter
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

使用示例時序定義：

* 環境 ID
* 最後 60 分鐘的資料
* 包含可選參數的術語 **（F1壓力**ID、F2TempStation和**F3振動PL）** **F2TempStation**

您可以為視圖構造以下參數化 URL：

```URL
https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

[![時間序列見解資源管理器參數化 URL](media/parameterized-url/share-parameterized-url.png)](media/parameterized-url/share-parameterized-url.png#lightbox)

> [!TIP]
> 使用上面的[URL](https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}])示例查看資源管理器即時。

上面的 URL 描述並顯示參數化的時間序列見解資源管理器視圖。 

* 參數化謂詞。

  [![時間序列見解資源管理器參數化謂詞。](media/parameterized-url/share-parameterized-url-predicates.png)](media/parameterized-url/share-parameterized-url-predicates.png#lightbox)

* 共用的完整圖表視圖。

  [![共用的完整圖表視圖。](media/parameterized-url/share-parameterized-url-full-chart.png)](media/parameterized-url/share-parameterized-url-full-chart.png#lightbox)

## <a name="next-steps"></a>後續步驟

* 瞭解如何使用[C# 查詢資料](time-series-insights-query-data-csharp.md)。

* 瞭解[時間序列見解資源管理器](./time-series-insights-explorer.md)。
