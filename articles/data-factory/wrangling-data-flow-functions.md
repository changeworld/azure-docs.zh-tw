---
title: 在 Azure 資料工廠中旋轉資料流程轉換功能
description: Azure 資料工廠中可用爭用資料串流函式概述
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 7235e95e5b33fb931411a51796a8dbec96c46355
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417652"
---
# <a name="transformation-functions-in-wrangling-data-flow"></a>在爭論資料串流的轉換函數

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

在 Azure 數據工廠中建構資料串流允許您在雲端規模下進行無代碼敏捷資料準備和爭鳴。 旋轉資料流與[連線電源查詢](https://docs.microsoft.com/powerquery-m/power-query-m-reference)整合,並使 Power Query M 函數可用於透過火花執行進行資料爭鳴。 

儘管在創作期間可用,但目前並非所有 Power Query M 函數都支援用於數據爭用。 建構此資料串流時,如果不支援函數,系統將提示您出現以下錯誤訊息:

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

下面是支援電源查詢 M 函數的清單。

## <a name="column-management"></a>欄位管理

* 選擇:[表格選擇欄位](https://docs.microsoft.com/powerquery-m/table-selectcolumns)
* 刪除:[表.刪除欄位](https://docs.microsoft.com/powerquery-m/table-removecolumns)
* 重新命名:[表.重新命名欄](https://docs.microsoft.com/powerquery-m/table-renamecolumns),[表為字串](https://docs.microsoft.com/powerquery-m/table-prefixcolumns),[表.轉換欄位](https://docs.microsoft.com/powerquery-m/table-transformcolumnnames)
* 重新排序:[表.重新排序](https://docs.microsoft.com/powerquery-m/table-reordercolumns)

## <a name="row-filtering"></a>列篩選

使用 M 函數[表.選擇行](https://docs.microsoft.com/powerquery-m/table-selectrows)以篩選以下條件:

* 平等和不平等
* 數字、文字和日期比較(但不是日期時間)
* 數位資訊,如[數位.甚至](https://docs.microsoft.com/powerquery-m/number-iseven)/[奇數](https://docs.microsoft.com/powerquery-m/number-iseven)
* 使用[文字包含、](https://docs.microsoft.com/powerquery-m/text-contains)包含 、[文字.使用 開頭](https://docs.microsoft.com/powerquery-m/text-startswith)或[文字.結束](https://docs.microsoft.com/powerquery-m/text-endswith)
* 日期範圍,包括所有「IsIn」[日期函數](https://docs.microsoft.com/powerquery-m/date-functions)) 
* 這些使用和,或或不條件的組合

## <a name="adding-and-transforming-columns"></a>新增並轉換欄位

以下 M 函數新增或轉換列:[表.addColumn,](https://docs.microsoft.com/powerquery-m/table-addcolumn)[表.轉換欄](https://docs.microsoft.com/powerquery-m/table-transformcolumns)位,[表.取代值](https://docs.microsoft.com/powerquery-m/table-replacevalue),[表.重複欄](https://docs.microsoft.com/powerquery-m/table-duplicatecolumn)。 下面是支援的轉換函數。

* 數值算術
* 文字串聯
* 日期與時間算術(算術運算符,[日期.Adddays,](https://docs.microsoft.com/powerquery-m/date-adddays)[日期.addmonths,](https://docs.microsoft.com/powerquery-m/date-addmonths)[日期.AddQuarters,](https://docs.microsoft.com/powerquery-m/date-addquarters)[日期.add周](https://docs.microsoft.com/powerquery-m/date-addweeks),[日期.Add年](https://docs.microsoft.com/powerquery-m/date-addyears))
* 持續時間可用於日期和時間算術,但在寫入接收器之前必須轉換為另一種類型(算術運算符[、#duration、](https://docs.microsoft.com/powerquery-m/sharpduration)[持續時間.天](https://docs.microsoft.com/powerquery-m/duration-days)、[持續時間.小時](https://docs.microsoft.com/powerquery-m/duration-hours)、[持續時間.分鐘](https://docs.microsoft.com/powerquery-m/duration-minutes)、[持續時間.持續時間](https://docs.microsoft.com/powerquery-m/duration-seconds)[.總天數](https://docs.microsoft.com/powerquery-m/duration-totaldays)、[持續時間.總時間、持續時間.總時間、持續時間.總時間](https://docs.microsoft.com/powerquery-m/duration-totalhours)、[持續時間.總分鐘](https://docs.microsoft.com/powerquery-m/duration-totalminutes)數[、持續時間.總秒數、持續時間。"](https://docs.microsoft.com/powerquery-m/duration-totalseconds)    
* 大部份標準、科學和三角測量數值函數([操作](https://docs.microsoft.com/powerquery-m/number-functions#operations)、[捨入](https://docs.microsoft.com/powerquery-m/number-functions#rounding)與[三角測量](https://docs.microsoft.com/powerquery-m/number-functions#trigonometry)下的所有函數 *,但*數位.因數、數位、排列和數位.組合除外)
* 取代([取代器.取代文字](https://docs.microsoft.com/powerquery-m/replacer-replacetext)、[取代器.取代值](https://docs.microsoft.com/powerquery-m/replacer-replacevalue)、[文字.取代](https://docs.microsoft.com/powerquery-m/text-replace)、[文字.刪除](https://docs.microsoft.com/powerquery-m/text-remove))
* 位置文字的名稱([text.位置](https://docs.microsoft.com/powerquery-m/text-positionof),[文字. 長度](https://docs.microsoft.com/powerquery-m/text-length),[文字. 開始](https://docs.microsoft.com/powerquery-m/text-start),[文字. 結束](https://docs.microsoft.com/powerquery-m/text-end),[文字. 中間](https://docs.microsoft.com/powerquery-m/text-middle),[文字. 取代範圍](https://docs.microsoft.com/powerquery-m/text-replacerange)[, 文字. 刪除範圍](https://docs.microsoft.com/powerquery-m/text-removerange))
* 基本文字格式([text.下部](https://docs.microsoft.com/powerquery-m/text-lower),[文字. 上部](https://docs.microsoft.com/powerquery-m/text-upper),[文字. 修剪](https://docs.microsoft.com/powerquery-m/text-trim)/[開始](https://docs.microsoft.com/powerquery-m/text-trimstart)/[結束](https://docs.microsoft.com/powerquery-m/text-trimend),[文字. PadStart](https://docs.microsoft.com/powerquery-m/text-padstart)/[結束](https://docs.microsoft.com/powerquery-m/text-padend),[文字. 反向](https://docs.microsoft.com/powerquery-m/text-reverse))
* 日期/時間函數([日期.天](https://docs.microsoft.com/powerquery-m/date-day),[日期.月](https://docs.microsoft.com/powerquery-m/date-month),[日期.年](https://docs.microsoft.com/powerquery-m/date-year)[時間.小時](https://docs.microsoft.com/powerquery-m/time-hour),[時間.分鐘](https://docs.microsoft.com/powerquery-m/time-minute),[時間.秒](https://docs.microsoft.com/powerquery-m/time-second),[日期.星期一,](https://docs.microsoft.com/powerquery-m/date-dayofweek)[日期.日期,日期](https://docs.microsoft.com/powerquery-m/date-dayofyear).日期[.日期月](https://docs.microsoft.com/powerquery-m/date-daysinmonth))
* 如果表示式(但分支必須具有符合的類型)
* 列篩選器為邏輯欄位
* 數字、文字、邏輯、日期和日期時間常量

<a name="mergingjoining-tables"></a>合併/聯接表
----------------------
* 電源查詢將生成嵌套聯接(表.NestedJoin;使用者還可以手動編寫[表.AddJoinColumn)。](https://docs.microsoft.com/powerquery-m/table-addjoincolumn)
    然後,用戶必須將嵌套聯接列擴展到非嵌套聯接(表.ExpandTableColumn,在任何其他上下文中不受支援)。
* M 函數[Table.Join](https://docs.microsoft.com/powerquery-m/table-join)可以直接編寫,以避免需要額外的延伸步驟,但使用者必須確保聯接的表之間沒有重複的欄名稱
* 支援的聯接類型:[Inner](https://docs.microsoft.com/powerquery-m/joinkind-inner)內[, 左外,](https://docs.microsoft.com/powerquery-m/joinkind-leftouter)[右外,](https://docs.microsoft.com/powerquery-m/joinkind-rightouter)[全外](https://docs.microsoft.com/powerquery-m/joinkind-fullouter)
* [值.相等值](https://docs.microsoft.com/powerquery-m/value-equals)和[值.可空等值](https://docs.microsoft.com/powerquery-m/value-nullableequals)都支援作為關鍵相等性比較器

## <a name="group-by"></a>群組依據

使用[表組](https://docs.microsoft.com/powerquery-m/table-group)聚合值。
* 必須與聚合函數一起使用
* 支援的聚合函數:[表.RowCount,](https://docs.microsoft.com/powerquery-m/table-rowcount)[清單.Sum,](https://docs.microsoft.com/powerquery-m/list-sum)[清單.計數](https://docs.microsoft.com/powerquery-m/list-count),[清單.平均](https://docs.microsoft.com/powerquery-m/list-average),[清單.Min,](https://docs.microsoft.com/powerquery-m/list-min)[清單.Max,](https://docs.microsoft.com/powerquery-m/list-max)[清單.標準偏差](https://docs.microsoft.com/powerquery-m/list-standarddeviation),[清單.第一](https://docs.microsoft.com/powerquery-m/list-first),[清單.最後](https://docs.microsoft.com/powerquery-m/list-last)

## <a name="sorting"></a>排序

使用[表排序](https://docs.microsoft.com/powerquery-m/table-sort)對值進行排序。

## <a name="reducing-rows"></a>遞減行

保持與刪除頂部,保持範圍(相應的 M 函數,僅支援計數,而不是條件:[表.FirstN,](https://docs.microsoft.com/powerquery-m/table-firstn)[表.跳過](https://docs.microsoft.com/powerquery-m/table-skip),[表.刪除FirstN,](https://docs.microsoft.com/powerquery-m/table-removefirstn)[表.範圍](https://docs.microsoft.com/powerquery-m/table-range),[表.MinN,](https://docs.microsoft.com/powerquery-m/table-minn)[表.MaxN)](https://docs.microsoft.com/powerquery-m/table-maxn)

## <a name="known-unsupported-functions"></a>已知不支援的函數

| 函式 | 狀態 |
| -- | -- |
| Table.PromoteHeaders | 不支援。 通過在數據集中設置"第一行為標題",可以得出相同的結果。 |
| Table.CombineColumns | 這是一個不直接支援但可以通過添加連接兩個給定列的新列來實現的常見方案。  例如,表.Addcolumn(刪除電子郵件列,"名稱",每個 [名字] &""& [姓氏]) |
| Table.TransformColumnTypes | 在大多數情況下,這一點是支援的。 不支援以下方案:將字串轉換為貨幣類型、將字串轉換為時間類型、將字串轉換為百分比類型。 |
| Table.NestedJoin | 只需執行聯接,就會導致驗證錯誤。 必須展開列才能正常工作。 |
| Table.Distinct | 不支援刪除重複行。 |
| Table.RemoveLastN | 不支援刪除底部行。 |
| Table.RowCount | 不支援,但可以通過添加列來實現,該列的所有單元格都為空(可以使用條件列),然後在該列上按組使用。 表.組受支援。 | 
| 行級錯誤處理 | 當前不支援行級錯誤處理。 例如,要從列中篩選出非數值,一種方法是將文本列轉換為數位。 每個無法轉換的單元格都將處於錯誤狀態,需要篩選。 在爭論數據流時,這種情況是不可能的。 |
| Table.Transpose | 不支援 |
| Table.Pivot | 不支援 |

## <a name="next-steps"></a>後續步驟

瞭解如何[建立爭鳴的資料串流](wrangling-data-flow-tutorial.md)。