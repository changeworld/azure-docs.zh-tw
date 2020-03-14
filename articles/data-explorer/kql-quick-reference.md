---
title: KQL 快速參考
description: 有用的 KQL 函數及其定義的清單，其中包含語法範例。
author: orspod
ms.author: orspodek
ms.reviewer: ''
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/19/2020
ms.openlocfilehash: e7196ccccb1203ce56b2e53fa358d091374cd3f8
ms.sourcegitcommit: d322d0a9d9479dbd473eae239c43707ac2c77a77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2020
ms.locfileid: "79139074"
---
# <a name="kql-quick-reference"></a>KQL 快速參考

本文將說明函式清單及其描述，以協助您開始使用 Kusto 查詢語言。

| Operator/Function                               | 描述                           | 語法                                           |
| :---------------------------------------------- | :------------------------------------ |:-------------------------------------------------|
|**篩選/搜尋/條件**                      |**_藉由篩選或搜尋來尋找相關資料_** |                      |
| [where](/azure/kusto/query/whereoperator)                      | 特定述詞上的篩選準則           | `T | where Predicate`                         |
| [其中包含/具有](/azure/kusto/query/whereoperator)        | `Contains`：尋找任何符合的子字串 <br> `Has`：尋找特定單字（較佳的效能）  | `T | where col1 contains/has "[search term]"`|
| [search](/azure/kusto/query/searchoperator)                    | 搜尋資料表中的所有資料行中的值 | `[TabularSource |] search [kind=CaseSensitivity] [in (TableSources)] SearchPredicate` |
| [take](/azure/kusto/query/takeoperator)                        | 傳回指定的記錄數目。 用來測試查詢<br>**_注意_** ： `_take`_ 和 `_limit`_ 是同義字。 | `T | take NumberOfRows` |
| [例圖](/azure/kusto/query/casefunction)                        | 加入條件陳述式，類似于其他系統中的 if/then/elseif。 | `case(predicate_1, then_1, predicate_2, then_2, predicate_3, then_3, else)` |
| [distinct](/azure/kusto/query/distinctoperator)                | 產生資料表，其中包含輸入資料表之所提供資料行的相異組合 | `distinct [ColumnName], [ColumnName]` |
| **日期/時間**                                   |**_使用日期和時間函數的作業_**               |                          |
|[曾](/azure/kusto/query/agofunction)                           | 傳回相對於查詢執行時間的時間位移。 例如，`ago(1h)` 是目前時鐘讀取前的一小時。 | `ago(a_timespan)` |
| [format_datetime](/azure/kusto/query/format-datetimefunction)  | 傳回[各種日期格式](/azure/kusto/query/format-datetimefunction#supported-formats)的資料。 | `format_datetime(datetime , format)` |
| [bin](/azure/kusto/query/binfunction)                          | 四捨五入時間範圍中的所有值，並將它們分組 | `bin(value,roundTo)` |
| **建立/移除資料行**                   |**_在資料表中新增或移除資料行_** |                                                    |
| [印刷](/azure/kusto/query/printoperator)                      | 輸出含有一或多個純量運算式的單一資料列 | `print [ColumnName =] ScalarExpression [',' ...]` |
| [project](/azure/kusto/query/projectoperator)                  | 選取要包含在指定順序中的資料行 | `T | project ColumnName [= Expression] [, ...]` <br> Or <br> `T | project [ColumnName | (ColumnName[,]) =] Expression [, ...]` |
| [project-away](/azure/kusto/query/projectawayoperator)         | 選取要從輸出中排除的資料行 | `T | project-away ColumnNameOrPattern [, ...]` |
| [extend](/azure/kusto/query/extendoperator)                    | 建立匯出資料行，並將它加入至結果集 | `T | extend [ColumnName | (ColumnName[, ...]) =] Expression [, ...]` |
| **排序和匯總資料集**                 |**_以有意義的方式將資料排序或分組來重新組織_**|                  |
| [sort](/azure/kusto/query/sortoperator)                        | 依據一或多個資料行，以遞增或遞減順序排序輸入資料表的資料列 | `T | sort by expression1 [asc|desc], expression2 [asc|desc], …` |
| [top](/azure/kusto/query/topoperator)                          | 當使用 `by` 排序資料集時，傳回資料集的前 N 個數據列 | `T | top numberOfRows by expression [asc|desc] [nulls first|last]` |
| [summarize](/azure/kusto/query/summarizeoperator)              | 根據 `by` 群組資料行將資料列分組，並計算每個群組的匯總 | `T | summarize [[Column =] Aggregation [, ...]] [by [Column =] GroupExpression [, ...]]` |
| [計數](/azure/kusto/query/countoperator)                       | 計算輸入資料表中的記錄（例如，T）<br>此運算子是 `summarize count() ` 的縮寫| `T | count` |
| [join](/azure/kusto/query/joinoperator)                        | 藉由比對每個資料表中指定之資料行的值，來合併兩個數據表的資料列，以形成新的資料表。 支援完整範圍的聯結類型： `flouter`、`inner`、`innerunique`、`leftanti`、`leftantisemi`、`leftouter`、`leftsemi`、`rightanti`、`rightantisemi`、`rightouter`、`rightsemi` | `LeftTable | join [JoinParameters] ( RightTable ) on Attributes` |
| [union](/azure/kusto/query/unionoperator)                      | 接受兩個或多個資料表，並傳回其所有資料列 | `[T1] | union [T2], [T3], …` |
| [range](/azure/kusto/query/rangeoperator)                      | 產生具有算術數列值的資料表 | `range columnName from start to stop step step` |
| **格式化資料**                                 | **_以實用的方式將資料重新組織成輸出_** | |
| [反向](/azure/kusto/query/lookupoperator)                    | 使用維度資料表中所查閱的值，擴充事實資料表的資料行 | `T1 | lookup [kind = (leftouter|inner)] ( T2 ) on Attributes` |
| [mv-展開](/azure/kusto/query/mvexpandoperator)               | 將動態陣列轉換成資料列（多重值擴充） | `T | mv-expand Column` |
| [分析](/azure/kusto/query/parseoperator)                      | 評估字串運算式，並將其值剖析至一或多個計算的資料行。 用於結構化的資料。 | `T | parse [kind=regex  [flags=regex_flags] |simple|relaxed] Expression with * (StringConstant ColumnName [: ColumnType]) *...` |
| [make 系列](/azure/kusto/query/make-seriesoperator)          | 沿著指定的軸建立一系列指定的匯總值 | `T | make-series [MakeSeriesParamters] [Column =] Aggregation [default = DefaultValue] [, ...] on AxisColumn from start to end step step [by [Column =] GroupExpression [, ...]]` |
| [現在](/azure/kusto/query/letstatement)                         | 將名稱系結至可參考其系結值的運算式。 值可以是 lambda 運算式，以便在查詢過程中建立特定函數。 使用 `let`，在其結果看起來像新資料表的資料表上建立運算式。 | `let Name = ScalarExpression | TabularExpression | FunctionDefinitionExpression` |
| **一般**                                     | **_其他作業和函數_** | |
| [invoke](/azure/kusto/query/invokeoperator)                    | 在當做輸入接收的資料表上執行函數。 | `T | invoke function([param1, param2])` |
| [評估 pluginName](/azure/kusto/query/evaluateoperator)     | 評估查詢語言延伸模組（外掛程式） | `[T |] evaluate [ evaluateParameters ] PluginName ( [PluginArg1 [, PluginArg2]... )` |
| **視覺效果**                               | **_以圖形格式顯示資料的作業_** | |
| [使得](/azure/kusto/query/renderoperator) | 以圖形化輸出呈現結果 | `T | render Visualization [with (PropertyName = PropertyValue [, ...] )]` |
