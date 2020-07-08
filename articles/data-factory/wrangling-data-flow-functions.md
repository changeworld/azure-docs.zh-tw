---
title: Azure Data Factory 中的整頓資料流程轉換函數
description: Azure Data Factory 中可用的整頓資料流程函數總覽
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: e63c3f329cb9c1fd5ca91274540f5145c3ad098a
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85921552"
---
# <a name="transformation-functions-in-wrangling-data-flow"></a>整頓資料流程中的轉換函數

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory 中的整頓資料流程可讓您以雲端規模進行無程式碼的敏捷式資料準備和整頓。 整頓資料流程會與[Power Query Online](https://docs.microsoft.com/powerquery-m/power-query-m-reference)整合，並透過 spark 執行讓 Power Query M 函數可供資料整頓。 

> [!NOTE]
> 整頓資料流程目前已供于公開預覽

目前不支援所有 Power Query M 函數來進行資料整頓，但仍可在撰寫期間使用。 建立整頓的資料流程時，如果不支援函式，系統會提示您輸入下列錯誤訊息：

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

以下是支援的 Power Query M 函數清單。

## <a name="column-management"></a>資料行管理

* 選取：[資料表. SelectColumns](https://docs.microsoft.com/powerquery-m/table-selectcolumns)
* 移除：[資料表。 table.removecolumns](https://docs.microsoft.com/powerquery-m/table-removecolumns)
* 重新命名： [RenameColumns](https://docs.microsoft.com/powerquery-m/table-renamecolumns)、[資料表 PrefixColumns](https://docs.microsoft.com/powerquery-m/table-prefixcolumns)、[資料表. TransformColumnNames](https://docs.microsoft.com/powerquery-m/table-transformcolumnnames)
* 重新排列：[資料表. ReorderColumns](https://docs.microsoft.com/powerquery-m/table-reordercolumns)

## <a name="row-filtering"></a>資料列篩選

使用 M 函數[Table. table.selectrows](https://docs.microsoft.com/powerquery-m/table-selectrows)來篩選下列條件：

* 相等和不相等
* 數值、文字和日期的比較（但不是 DateTime）
* 數值資訊，例如[Number. IsEven](https://docs.microsoft.com/powerquery-m/number-iseven) / [奇數](https://docs.microsoft.com/powerquery-m/number-iseven)
* 使用 Text 的文字[內含專案。 Contains](https://docs.microsoft.com/powerquery-m/text-contains)、 [Text. StartsWith](https://docs.microsoft.com/powerquery-m/text-startswith)或[text. EndsWith](https://docs.microsoft.com/powerquery-m/text-endswith)
* 包含所有 ' IsIn' [Date 函數](https://docs.microsoft.com/powerquery-m/date-functions)的日期範圍 
* 使用 and、or 或 not 條件的組合

## <a name="adding-and-transforming-columns"></a>加入和轉換資料行

下列 M 函數會加入或轉換資料行： [AddColumn](https://docs.microsoft.com/powerquery-m/table-addcolumn)、 [table.transformcolumns](https://docs.microsoft.com/powerquery-m/table-transformcolumns)、 [. u](https://docs.microsoft.com/powerquery-m/table-replacevalue)、 [table. DuplicateColumn](https://docs.microsoft.com/powerquery-m/table-duplicatecolumn)。 以下是支援的轉換函數。

* 數值算術
* 文字串連
* Date andTime 算術（算術運算子、 [date. AddDays](https://docs.microsoft.com/powerquery-m/date-adddays)、 [date. AddMonths](https://docs.microsoft.com/powerquery-m/date-addmonths)、 [Date AddQuarters](https://docs.microsoft.com/powerquery-m/date-addquarters)、 [date. AddWeeks](https://docs.microsoft.com/powerquery-m/date-addweeks)、 [date. AddYears](https://docs.microsoft.com/powerquery-m/date-addyears)）
* Duration 可以用於日期和時間運算，但必須先轉換成另一種類型，才能寫入至接收（算術運算子、 [#duration](https://docs.microsoft.com/powerquery-m/sharpduration)、[持續時間、天數](https://docs.microsoft.com/powerquery-m/duration-days)、[持續時間、小時](https://docs.microsoft.com/powerquery-m/duration-hours)、持續時間、[分鐘](https://docs.microsoft.com/powerquery-m/duration-minutes)、持續時間、[秒](https://docs.microsoft.com/powerquery-m/duration-seconds)數、 [TotalDays](https://docs.microsoft.com/powerquery-m/duration-totaldays)、持續時間、 [TotalHours](https://docs.microsoft.com/powerquery-m/duration-totalhours)、[持續時間 TotalMinutes](https://docs.microsoft.com/powerquery-m/duration-totalminutes)、持續時間[TotalSeconds](https://docs.microsoft.com/powerquery-m/duration-totalseconds)）    
* 最標準、科學和三角數值函式（除數位以外的所有函[式、](https://docs.microsoft.com/powerquery-m/number-functions#operations)[進位](https://docs.microsoft.com/powerquery-m/number-functions#rounding)和[三角函數](https://docs.microsoft.com/powerquery-m/number-functions#trigonometry)*除外*）。
* 取代（[取代子. ReplaceText](https://docs.microsoft.com/powerquery-m/replacer-replacetext)，[取代子.. u](https://docs.microsoft.com/powerquery-m/replacer-replacevalue)， [text. Replace](https://docs.microsoft.com/powerquery-m/text-replace)， [text. Remove](https://docs.microsoft.com/powerquery-m/text-remove)）
* 位置文字解壓縮（[PositionOf](https://docs.microsoft.com/powerquery-m/text-positionof)、 [text. Length](https://docs.microsoft.com/powerquery-m/text-length)、text、 [Start](https://docs.microsoft.com/powerquery-m/text-start)、 [text. End](https://docs.microsoft.com/powerquery-m/text-end)、 [text](https://docs.microsoft.com/powerquery-m/text-middle). [ReplaceRange](https://docs.microsoft.com/powerquery-m/text-replacerange)、 [text. RemoveRange](https://docs.microsoft.com/powerquery-m/text-removerange)）
* 基本文字格式（[文字. Lower](https://docs.microsoft.com/powerquery-m/text-lower)、[文字. 大寫](https://docs.microsoft.com/powerquery-m/text-upper)、[文字. 修剪](https://docs.microsoft.com/powerquery-m/text-trim) / [開始](https://docs.microsoft.com/powerquery-m/text-trimstart) / [結束](https://docs.microsoft.com/powerquery-m/text-trimend)、 [PadStart](https://docs.microsoft.com/powerquery-m/text-padstart) / [結束](https://docs.microsoft.com/powerquery-m/text-padend)、文字） [。](https://docs.microsoft.com/powerquery-m/text-reverse)
* 日期/時間函數（[date、Day](https://docs.microsoft.com/powerquery-m/date-day)、 [date、Month](https://docs.microsoft.com/powerquery-m/date-month)、 [Date、Year](https://docs.microsoft.com/powerquery-m/date-year) [time、Hour](https://docs.microsoft.com/powerquery-m/time-hour)、 [Minute](https://docs.microsoft.com/powerquery-m/time-minute)、Time. [Second](https://docs.microsoft.com/powerquery-m/time-second)、 [date. DayOfWeek](https://docs.microsoft.com/powerquery-m/date-dayofweek)、 [date. DayOfYear](https://docs.microsoft.com/powerquery-m/date-dayofyear)、 [date. DaysInMonth](https://docs.microsoft.com/powerquery-m/date-daysinmonth)）
* If 運算式（但分支必須有相符的類型）
* 當做邏輯資料行的資料列篩選
* 數位、文字、邏輯、日期和日期時間常數

<a name="mergingjoining-tables"></a>合併/聯結資料表
----------------------
* Power Query 會產生一個 nested 聯結（NestedJoin; 使用者也可以手動撰寫[AddJoinColumn](https://docs.microsoft.com/powerquery-m/table-addjoincolumn)）。
    使用者接著必須將 nested 聯結資料行展開為非嵌套聯結（Table.expandtablecolumn，在任何其他內容中不支援）。
* 可以直接寫入 M 函數[資料表](https://docs.microsoft.com/powerquery-m/table-join)，以避免需要額外的擴充步驟，但是使用者必須確定聯結的資料表之間沒有重複的資料行名稱
* 支援的聯結類型： [Inner](https://docs.microsoft.com/powerquery-m/joinkind-inner)、 [LeftOuter](https://docs.microsoft.com/powerquery-m/joinkind-leftouter)、 [RightOuter](https://docs.microsoft.com/powerquery-m/joinkind-rightouter)、 [FullOuter](https://docs.microsoft.com/powerquery-m/joinkind-fullouter)
* [值. Equals](https://docs.microsoft.com/powerquery-m/value-equals)和[value. NullableEquals](https://docs.microsoft.com/powerquery-m/value-nullableequals)都支援做為索引鍵相等比較子

## <a name="group-by"></a>群組依據

使用[資料表群組](https://docs.microsoft.com/powerquery-m/table-group)來匯總值。
* 必須搭配彙總函式使用
* 支援的彙總函式：[資料表。](https://docs.microsoft.com/powerquery-m/table-rowcount)資料[列計數，清單. 總和](https://docs.microsoft.com/powerquery-m/list-sum)，[清單. 計數](https://docs.microsoft.com/powerquery-m/list-count)，[清單。平均值](https://docs.microsoft.com/powerquery-m/list-average) [，清單](https://docs.microsoft.com/powerquery-m/list-min)。 [StandardDeviation](https://docs.microsoft.com/powerquery-m/list-standarddeviation) [，清單](https://docs.microsoft.com/powerquery-m/list-first)，[最大值](https://docs.microsoft.com/powerquery-m/list-max)，清單。[最後](https://docs.microsoft.com/powerquery-m/list-last)一個

## <a name="sorting"></a>排序

使用 [[資料表]。排序](https://docs.microsoft.com/powerquery-m/table-sort)以排序值。

## <a name="reducing-rows"></a>減少資料列

保留和移除頂端、保留範圍（對應的 M 函數、僅支援計數，而不是條件： [FirstN](https://docs.microsoft.com/powerquery-m/table-firstn)、[資料表. Skip](https://docs.microsoft.com/powerquery-m/table-skip)、 [RemoveFirstN](https://docs.microsoft.com/powerquery-m/table-removefirstn)、 [table. Range](https://docs.microsoft.com/powerquery-m/table-range)、[德盧斯](https://docs.microsoft.com/powerquery-m/table-minn)、 [table. MaxN](https://docs.microsoft.com/powerquery-m/table-maxn)）

## <a name="known-unsupported-functions"></a>已知不支援的函式

| 函式 | 狀態 |
| -- | -- |
| Table.PromoteHeaders | 不支援。 藉由在資料集中設定「第一個資料列做為標頭」，可以達到相同的結果。 |
| Table.CombineColumns | 這是不直接支援的常見案例，但可以藉由加入新的資料行來串連兩個指定的資料行來達成。  例如，AddColumn （RemoveEmailColumn，"Name"，each [FirstName] & "" & [LastName]） |
| Table.TransformColumnTypes | 這在大部分情況下都受到支援。 下列案例不受支援：將字串轉換為貨幣類型、將字串轉換成時間類型，並將字串轉換成百分比類型。 |
| Table.NestedJoin | 只要執行聯結，就會導致驗證錯誤。 必須擴充資料行，才能使其正常執行。 |
| Table.Distinct | 不支援移除重複的資料列。 |
| Table.RemoveLastN | 不支援移除底部資料列。 |
| Table.RowCount | 不支援，但是可以透過加入資料行（其中包含空白的 [條件] 資料行），然後在該資料行上使用 [群組依據] 來達成。 資料表。支援群組。 | 
| 資料列層級錯誤處理 | 目前不支援資料列層級的錯誤處理。 例如，若要篩選出資料行中的非數值，其中一種方法是將文字資料行轉換成數位。 無法轉換的每個資料格都會處於錯誤狀態，而且需要進行篩選。 此案例在整頓資料流程中是不可行的。 |
| Table.Transpose | 不支援 |
| Table.Pivot | 不支援 |

## <a name="next-steps"></a>後續步驟

瞭解如何[建立整頓資料流程](wrangling-data-flow-tutorial.md)。