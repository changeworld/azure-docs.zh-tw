---
title: Azure Data Factory 中的資料整頓函數
description: Azure Data Factory 中可用資料整頓函式的總覽
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/19/2021
ms.openlocfilehash: a88f9fab2b10271aa7856a6d0b5ee114f46cfb49
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98633927"
---
# <a name="transformation-functions-in-power-query-for-data-wrangling"></a>Power Query 中的資料整頓轉換函數

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory 中的資料整頓可讓您藉由將 Power Query ```M``` 腳本轉譯成資料流程腳本，以雲端規模進行無程式碼的敏捷式資料準備和整頓。 ADF 會與 [Power Query Online](/powerquery-m/power-query-m-reference) 整合，並 ```M``` 使用資料流程 Spark 基礎結構，讓 Power Query 的函式可用於透過 Spark 執行整頓的資料。 

> [!NOTE]
> ADF 中的 Power Query 目前 cmpivot 為公開預覽狀態

目前並不支援所有 Power Query M 函式進行資料整頓（儘管在撰寫期間可供使用）。 當您建立混合時，如果不支援函式，系統會提示您輸入下列錯誤訊息：

`The Wrangling Data Flow is invalid. Expression.Error: The transformation logic is not supported. Please try a simpler expression.`

以下是支援的 Power Query M 函數清單。

## <a name="column-management"></a>資料行管理

* 選取： [資料表. SelectColumns](/powerquery-m/table-selectcolumns)
* 移除： [資料表. table.removecolumns](/powerquery-m/table-removecolumns)
* 重新命名： [RenameColumns](/powerquery-m/table-renamecolumns)，資料表. [PrefixColumns](/powerquery-m/table-prefixcolumns)， [table. 無法辨識 table.transformcolumnnames](/powerquery-m/table-transformcolumnnames)
* 重新排列： [資料表. ReorderColumns](/powerquery-m/table-reordercolumns)

## <a name="row-filtering"></a>資料列篩選

使用 M 函式 [table.selectrows](/powerquery-m/table-selectrows) 來篩選下列條件：

* 相等和不等比較
* 數值、文字和日期比較 (但不是 DateTime) 
* 數值資訊，例如[Number. IsEven](/powerquery-m/number-iseven) / [奇數](/powerquery-m/number-iseven)
* 使用 Text 的文字[內含專案。 Contains](/powerquery-m/text-contains)、 [StartsWith](/powerquery-m/text-startswith)或[EndsWith](/powerquery-m/text-endswith) 。
* 日期範圍，包括所有「IsIn' [日期函數](/powerquery-m/date-functions) 」)  
* 使用 and、or 或 not 條件的組合

## <a name="adding-and-transforming-columns"></a>新增和轉換資料行

下列 M 函數會新增或轉換資料行： [AddColumn](/powerquery-m/table-addcolumn)、 [table.transformcolumns](/powerquery-m/table-transformcolumns)、 [list.replacevalue](/powerquery-m/table-replacevalue)、 [table. table.duplicatecolumn](/powerquery-m/table-duplicatecolumn)。 以下是支援的轉換函數。

* 數值算術
* 文字串連
* Date andTime 算術 (算術運算子、 [date. AddDays](/powerquery-m/date-adddays)、 [date. AddMonths](/powerquery-m/date-addmonths)、 [date. AddQuarters](/powerquery-m/date-addquarters)、 [date. AddWeeks](/powerquery-m/date-addweeks)、 [date. AddYears](/powerquery-m/date-addyears)) 
* 持續時間可用於日期和時間算術，但必須先轉換成另一種類型，再寫入至接收 (算術運算子[、#duration](/powerquery-m/sharpduration)、[持續時間、日](/powerquery-m/duration-days)、[持續時間](/powerquery-m/duration-hours)、時數、持續時間、[分鐘](/powerquery-m/duration-minutes)數、持續時間（小時）、 [TotalDays](/powerquery-m/duration-totaldays)、Duration. [TotalHours](/powerquery-m/duration-totalhours)、 [duration. TotalMinutes](/powerquery-m/duration-totalminutes)、duration. [TotalSeconds](/powerquery-m/duration-totalseconds)) [](/powerquery-m/duration-seconds)    
* 大部分的標準、科學和三角數值函式 (在 [運算](/powerquery-m/number-functions#operations)、 [舍入](/powerquery-m/number-functions#rounding)和 [三角函數](/powerquery-m/number-functions#trigonometry)下的所有函式， *但不包括* 數位。階乘、數位、排列和數位組合) 
* 取代 ([取代子. replacer.replacetext](/powerquery-m/replacer-replacetext)、 [取代子. list.replacevalue](/powerquery-m/replacer-replacevalue)、 [Replace](/powerquery-m/text-replace)、 [text. Remove](/powerquery-m/text-remove)) 
* 位置文字解壓縮 ([PositionOf](/powerquery-m/text-positionof)、 [text. Length](/powerquery-m/text-length)、text. [Start](/powerquery-m/text-start) [、text.](/powerquery-m/text-end) [ReplaceRange](/powerquery-m/text-replacerange)、 [RemoveRange](/powerquery-m/text-removerange)) [](/powerquery-m/text-middle)的文字
* 基本文字格式 ([文字。小寫](/powerquery-m/text-lower)、[文字、大寫](/powerquery-m/text-upper)、[文字. Trim](/powerquery-m/text-trim) / [開始](/powerquery-m/text-trimstart) / [ ](/powerquery-m/text-trimend)、 [PadStart](/powerquery-m/text-padstart) / [結束](/powerquery-m/text-padend)、[文字反向](/powerquery-m/text-reverse)) 
* 日期/時間函式 ([日期、日](/powerquery-m/date-day)、[月](/powerquery-m/date-month)、[日期。年](/powerquery-m/date-year)[時間](/powerquery-m/time-hour)、時間、[分鐘](/powerquery-m/time-minute)、 [DayOfWeek](/powerquery-m/date-dayofweek)、日期[](/powerquery-m/time-second) [. DayOfYear](/powerquery-m/date-dayofyear)、[日期. DaysInMonth](/powerquery-m/date-daysinmonth)) 
* 如果運算式 (但分支必須有相符的類型) 
* 做為邏輯資料行的資料列篩選
* Number、text、logical、date 和 datetime 常數

<a name="mergingjoining-tables"></a>合併/聯結資料表
----------------------
* Power Query 將會產生嵌套聯結 (資料表. Table.nestedjoin;使用者也可以手動寫入 [AddJoinColumn](/powerquery-m/table-addjoincolumn)) 。
    然後，使用者必須將嵌套的聯結資料行展開為非嵌套的聯結 (Table.expandtablecolumn，不支援任何其他內容) 。
* 您可以直接撰寫 M 函數   [資料表](/powerquery-m/table-join) ，以避免需要額外的擴充步驟，但使用者必須確定聯結資料表之間沒有重複的資料行名稱。
* 支援的聯結種類：   [Inner](/powerquery-m/joinkind-inner)、   [LeftOuter](/powerquery-m/joinkind-leftouter)、   [RightOuter](/powerquery-m/joinkind-rightouter)、   [FullOuter](/powerquery-m/joinkind-fullouter)
* [值 Equals](/powerquery-m/value-equals)和[值. NullableEquals](/powerquery-m/value-nullableequals)都支援做為索引鍵相等比較子

## <a name="group-by"></a>群組依據

使用 [資料表群組](/powerquery-m/table-group) 來匯總值。
* 必須搭配彙總函式使用
* 支援的彙總函式   [： list. Sum](/powerquery-m/list-sum)、   [list、Count](/powerquery-m/list-count)、   [list. Average](/powerquery-m/list-average)、List.   [Min](/powerquery-m/list-min)、list.   [Max](/powerquery-m/list-max)、   [list. list.standarddeviation](/powerquery-m/list-standarddeviation)、list.   [First](/powerquery-m/list-first)、   [list. Last](/powerquery-m/list-last)

## <a name="sorting"></a>排序

使用 [ [資料表](/powerquery-m/table-sort) ] 來排序值。

## <a name="reducing-rows"></a>減少資料列

保留和移除 Top、保留範圍 (對應的 M 函式，僅支援計數，而不是條件： [FirstN](/powerquery-m/table-firstn)， [table. Skip](/powerquery-m/table-skip)，RemoveFirstN， [table.](/powerquery-m/table-range) [ ](/powerquery-m/table-removefirstn)，table. [doug](/powerquery-m/table-minn)， [table. MaxN](/powerquery-m/table-maxn)) 

## <a name="known-unsupported-functions"></a>已知不支援的函式

| 函式 | 狀態 |
| -- | -- |
| Table.PromoteHeaders | 不支援。 您可以藉由在資料集中設定「第一個資料列做為標頭」來達成相同的結果。 |
| Table.CombineColumns | 這是不直接支援的常見案例，但可以藉由加入串連兩個指定資料行的新資料行來達成。  例如，AddColumn (RemoveEmailColumn，"Name"，each [FirstName] & "" & [LastName] )  |
| Table.TransformColumnTypes | 大部分情況下都支援此功能。 下列案例不受支援：將字串轉換為貨幣類型、將字串轉換為時間類型、將字串轉換為百分比類型。 |
| Table.NestedJoin | 單純的聯結會導致驗證錯誤。 必須展開這些資料行才能運作。 |
| Table.Distinct | 不支援移除重複的資料列。 |
| Table.RemoveLastN | 不支援移除底部的資料列。 |
| Table.RowCount | 不受支援，但可以藉由加入包含值1的自訂資料行，然後以清單 Sum 來匯總該資料行來達成。 Table。支援群組。 | 
| 資料列層級錯誤處理 | 目前不支援資料列層級錯誤處理。 例如，若要篩選出資料行中的非數值，其中一個方法就是將文字資料行轉換成數位。 無法轉換的每個資料格將會處於錯誤狀態，且需要進行篩選。 此案例無法在整頓資料流程中進行。 |
| Table.Transpose | 不支援 |
| Table.Pivot | 不支援 |

## <a name="next-steps"></a>後續步驟

瞭解如何 [在 ADF 中建立資料整頓 Power Query](wrangling-tutorial.md)。
