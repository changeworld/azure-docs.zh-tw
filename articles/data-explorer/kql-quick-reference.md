---
title: KQL 快速參考
description: 有用的 KQL 函數及其定義的清單，包含語法示例。
author: orspod
ms.author: orspodek
ms.reviewer: ''
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/19/2020
ms.openlocfilehash: e7196ccccb1203ce56b2e53fa358d091374cd3f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139074"
---
# <a name="kql-quick-reference"></a>KQL 快速參考

本文將列出函數及其說明清單，以説明您開始使用 Kusto 查詢語言。

| 操作員/功能                               | 描述                           | 語法                                           |
| :---------------------------------------------- | :------------------------------------ |:-------------------------------------------------|
|**篩檢程式/搜索/條件**                      |**_通過篩選或搜索查找相關資料_** |                      |
| [where](/azure/kusto/query/whereoperator)                      | 特定謂詞上的篩選器           | `T | where Predicate`                         |
| [其中包含/有](/azure/kusto/query/whereoperator)        | `Contains`： 查找任何子字串匹配項 <br> `Has`：查找特定單詞（性能更好）  | `T | where col1 contains/has "[search term]"`|
| [搜尋](/azure/kusto/query/searchoperator)                    | 搜索表中的所有列以搜索值 | `[TabularSource |] search [kind=CaseSensitivity] [in (TableSources)] SearchPredicate` |
| [採取](/azure/kusto/query/takeoperator)                        | 返回指定的記錄數。 用於測試查詢<br>**_注意_** `_take`：`_limit`和 * 是同義字。 | `T | take NumberOfRows` |
| [案例](/azure/kusto/query/casefunction)                        | 添加條件陳述式，類似于其他系統中的 if/然後/elseif。 | `case(predicate_1, then_1, predicate_2, then_2, predicate_3, then_3, else)` |
| [不同](/azure/kusto/query/distinctoperator)                | 生成具有輸入表提供的列的不同組合的表 | `distinct [ColumnName], [ColumnName]` |
| **日期/時間**                                   |**_使用日期和時間函數的操作_**               |                          |
|[前](/azure/kusto/query/agofunction)                           | 返回相對於查詢執行時間的時間偏移量。 例如，`ago(1h)`是當前時鐘讀數前一小時。 | `ago(a_timespan)` |
| [format_datetime](/azure/kusto/query/format-datetimefunction)  | 返回[各種日期格式](/azure/kusto/query/format-datetimefunction#supported-formats)的資料。 | `format_datetime(datetime , format)` |
| [站](/azure/kusto/query/binfunction)                          | 在時間範圍內舍入所有值並對其進行分組 | `bin(value,roundTo)` |
| **創建/刪除列**                   |**_添加或刪除表中的列_** |                                                    |
| [列印](/azure/kusto/query/printoperator)                      | 使用一個或多個標量運算式輸出單個行 | `print [ColumnName =] ScalarExpression [',' ...]` |
| [專案](/azure/kusto/query/projectoperator)                  | 選擇要按指定順序包括的列 | `T | project ColumnName [= Expression] [, ...]` <br> Or <br> `T | project [ColumnName | (ColumnName[,]) =] Expression [, ...]` |
| [project-away](/azure/kusto/query/projectawayoperator)         | 選擇要從輸出中排除的列 | `T | project-away ColumnNameOrPattern [, ...]` |
| [擴展](/azure/kusto/query/extendoperator)                    | 創建計算列並將其添加到結果集 | `T | extend [ColumnName | (ColumnName[, ...]) =] Expression [, ...]` |
| **排序和聚合資料集**                 |**_通過以有意義的方式對資料進行排序或分組來重組資料_**|                  |
| [排序](/azure/kusto/query/sortoperator)                        | 按昇冪或降冪按一個或多個列對輸入表的行進行排序 | `T | sort by expression1 [asc|desc], expression2 [asc|desc], …` |
| [返回頁首](/azure/kusto/query/topoperator)                          | 使用資料集排序時返回資料集的前 N 行`by` | `T | top numberOfRows by expression [asc|desc] [nulls first|last]` |
| [總結](/azure/kusto/query/summarizeoperator)              | 根據組列對行進行`by`分組，並計算每個組的聚合 | `T | summarize [[Column =] Aggregation [, ...]] [by [Column =] GroupExpression [, ...]]` |
| [count](/azure/kusto/query/countoperator)                       | 計算輸入表中的記錄（例如，T）<br>此運算子是速記`summarize count() `| `T | count` |
| [加入](/azure/kusto/query/joinoperator)                        | 通過匹配每個表中指定列的值，合併兩個表的行以形成新表。 支援各種`flouter`聯接類型： 、 `inner`、 `innerunique`、 `leftanti` `leftantisemi`、 `leftouter` `leftsemi`、 `rightanti` `rightantisemi`、 `rightouter`、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、`rightsemi` | `LeftTable | join [JoinParameters] ( RightTable ) on Attributes` |
| [聯盟](/azure/kusto/query/unionoperator)                      | 獲取兩個或多個表並返回其所有行 | `[T1] | union [T2], [T3], …` |
| [range](/azure/kusto/query/rangeoperator)                      | 生成具有算術值序列的表 | `range columnName from start to stop step step` |
| **將資料格式化**                                 | **_以有用的方式重組資料以輸出_** | |
| [查找](/azure/kusto/query/lookupoperator)                    | 在維度表中使用拾值擴展事實資料表的列 | `T1 | lookup [kind = (leftouter|inner)] ( T2 ) on Attributes` |
| [mv 擴展](/azure/kusto/query/mvexpandoperator)               | 將動態陣列轉換為行（多值擴展） | `T | mv-expand Column` |
| [解析](/azure/kusto/query/parseoperator)                      | 評估字串運算式，並將其值剖析至一或多個計算的資料行。 用於構建非結構化資料。 | `T | parse [kind=regex  [flags=regex_flags] |simple|relaxed] Expression with * (StringConstant ColumnName [: ColumnType]) *...` |
| [make-series](/azure/kusto/query/make-seriesoperator)          | 沿指定軸創建一系列指定的聚合值 | `T | make-series [MakeSeriesParamters] [Column =] Aggregation [default = DefaultValue] [, ...] on AxisColumn from start to end step step [by [Column =] GroupExpression [, ...]]` |
| [讓](/azure/kusto/query/letstatement)                         | 將名稱綁定到可以引用其綁定值的運算式。 值可以是 lambda 運算式，以創建作為查詢的一部分的臨時函數。 用於`let`在結果看起來像新表的表上創建運算式。 | `let Name = ScalarExpression | TabularExpression | FunctionDefinitionExpression` |
| **一般**                                     | **_雜項操作和職能_** | |
| [invoke](/azure/kusto/query/invokeoperator)                    | 在作為輸入接收的表上運行函數。 | `T | invoke function([param1, param2])` |
| [評估外掛程式名稱](/azure/kusto/query/evaluateoperator)     | 評估查詢語言擴展（外掛程式） | `[T |] evaluate [ evaluateParameters ] PluginName ( [PluginArg1 [, PluginArg2]... )` |
| **視覺效果**                               | **_以圖形格式顯示資料的操作_** | |
| [呈現](/azure/kusto/query/renderoperator) | 渲染結果為圖形輸出 | `T | render Visualization [with (PropertyName = PropertyValue [, ...] )]` |
