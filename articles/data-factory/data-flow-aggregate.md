---
title: 映射資料流程的聚合轉換
description: 瞭解如何使用映射資料流程聚合轉換在 Azure 資料工廠中大規模聚合資料。
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/24/2020
ms.openlocfilehash: 1830a16108e6d8bb251d7ca45ae471e2f606874b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240591"
---
# <a name="aggregate-transformation-in-mapping-data-flow"></a>映射資料流程的聚合轉換 

聚合轉換定義資料流程中列的聚合。 使用運算式產生器，可以定義不同類型的聚合，如 SUM、MIN、MAX 和 COUNT，按現有列或計算列分組。

## <a name="group-by"></a>群組依據

選擇現有列或創建新的計算列，以用作聚合的按子句分組。 要使用現有列，請從下拉清單中選擇它。 要創建新的計算列，請將滑鼠懸停在子句上，然後按一下"**計算"列**。 這將打開[資料流程運算式產生器](concepts-data-flow-expression-builder.md)。 創建計算列後，請在 **"名稱"欄位中**輸入輸出列名稱。 如果要按子句添加其他組，請將滑鼠懸停在現有子句上，然後按一下加號圖示。

![按設置聚合轉換組](media/data-flow/agg.png "按設置聚合轉換組")

在聚合轉換中，按子句分組是可選的。

## <a name="aggregate-column"></a>聚合列 

轉到**聚合**選項卡以生成聚合運算式。 您可以使用聚合覆蓋現有列，也可以使用新名稱創建新欄位。 聚合運算式在列名稱選擇器旁邊的右側框中輸入。 要編輯運算式，請按一下文字方塊以打開運算式產生器。 要添加其他聚合，請將滑鼠懸停在現有運算式上，然後按一下加號以創建新的聚合列或[列模式](concepts-data-flow-column-pattern.md)。

每個聚合運算式必須至少包含一個彙總函式。

![聚合轉換聚合設置](media/data-flow/agg2.png "聚合轉換聚合設置")


> [!NOTE]
> 在偵錯模式下，運算式產生器無法生成具有彙總函式的資料預覽。 要查看聚合轉換的資料預覽，關閉運算式產生器並通過"資料預覽"選項卡查看資料。

## <a name="reconnect-rows-and-columns"></a>重新連接行和列

聚合轉換類似于 SQL 聚合選取查詢。 按子句或彙總函式未包含在組中的列不會流到聚合轉換的輸出。 如果要在聚合輸出中包含其他列，請執行以下方法之一：

* 使用彙總函式，如`last()`或`first()`包括該附加列。
* 使用[自聯接模式](https://mssqldude.wordpress.com/2018/12/20/adf-data-flows-self-join/)將列重新加入輸出流。

## <a name="removing-duplicate-rows"></a>刪除重複行

聚合轉換的常見用途是刪除或標識來源資料中的重複條目。 此過程稱為重復資料消除。 基於按鍵分組的組，使用您選擇的啟發式方法確定要保留的重複行。 常見的啟發`first()`式是 ，`last()`和`max()`。 `min()` 使用[列模式](concepts-data-flow-column-pattern.md)將規則應用於除按列分組之外的每個列。

![重複](media/data-flow/agg-dedupe.png "重複資料刪除")

在上面的示例中，列`ProductID`和`Name`正在用於分組。 如果兩行的兩列具有相同的值，則它們被視為重複項。 在此聚合轉換中，將保留匹配的第一行的值，並刪除所有其他行的值。 使用列模式語法，所有名稱不是`ProductID`並`Name`映射到其現有列名稱並給出第一個匹配行的值的列。 輸出架構與輸入架構相同。

對於資料驗證方案，`count()`該函數可用於計算有多少重複項。

## <a name="data-flow-script"></a>資料流程指令碼

### <a name="syntax"></a>語法

```
<incomingStream>
    aggregate(
           groupBy(
                <groupByColumnName> = <groupByExpression1>,
                <groupByExpression2>
               ),
           <aggregateColumn1> = <aggregateExpression1>,
           <aggregateColumn2> = <aggregateExpression2>,
           each(
                match(matchExpression),
                <metadataColumn1> = <metadataExpression1>,
                <metadataColumn2> = <metadataExpression2>
               )
          ) ~> <aggregateTransformationName>
```

### <a name="example"></a>範例

下面的示例採用傳入流`MoviesYear`，按列`year`對行進行分組。 轉換創建一個聚合列`avgrating`，該列計算到列`Rating`的平均值。 此聚合轉換名為`AvgComedyRatingsByYear`。

在資料工廠 UX 中，此轉換類似于下圖：

![按示例分組](media/data-flow/agg-script1.png "按示例分組")

![聚合示例](media/data-flow/agg-script2.png "聚合示例")

此轉換的資料流程腳本位於下面的程式碼片段中。

```
MoviesYear aggregate(
                groupBy(year),
                avgrating = avg(toInteger(Rating))
            ) ~> AvgComedyRatingByYear
```

## <a name="next-steps"></a>後續步驟

* 使用[視窗轉換](data-flow-window.md)定義基於視窗的聚合
