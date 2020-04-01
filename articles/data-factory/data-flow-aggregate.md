---
title: 對應資料串流的集合轉換
description: 瞭解如何使用映射數據流聚合轉換在 Azure 數據工廠中大規模聚合數據。
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/24/2020
ms.openlocfilehash: e4b076d96cad280c4da6c2424f056c2216c47602
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80408857"
---
# <a name="aggregate-transformation-in-mapping-data-flow"></a>對應資料串流的集合轉換 

聚合轉換定義數據流中列的聚合。 使用運算式生成器,可以定義不同類型的聚合,如 SUM、MIN、MAX 和 COUNT,按現有列或計算列分組。

## <a name="group-by"></a>群組依據

選擇現有列或創建新的計算列,以用作聚合的按子句分組。 要使用現有列,請從下拉清單中選擇它。 要建立新的計算欄位,請將滑鼠的移動在子句上,然後按下「**計算」 欄位**。 這會開啟[資料串流表示式產生器](concepts-data-flow-expression-builder.md)。 建立計算欄後,請在 **「名稱」欄位中**輸入輸出列名稱。 如果要按子句添加其他組,請將滑鼠懸停在現有子句上,然後單擊加號圖示。

![依設定集合組](media/data-flow/agg.png "依設定集合組")

在聚合轉換中,按子句分組是可選的。

## <a name="aggregate-column"></a>彙列 

轉到**聚合**選項卡以生成聚合運算式。 您可以使用聚合覆蓋現有列,也可以使用新名稱創建新欄位。 聚合表達式在列名稱選擇器旁邊的右側框中輸入。 要編輯運算式,請按一下文字框以打開運算式產生器。 要新增其他集合,請將滑鼠的移動在現有表示式上,然後按一下加號以建立新的集合欄位或[列模式](concepts-data-flow-column-pattern.md)。

每個聚合表達式必須至少包含一個聚合函數。

![彙總轉換集合設定](media/data-flow/agg2.png "彙總轉換集合設定")


> [!NOTE]
> 在調試模式下,表達式生成器無法生成具有聚合函數的數據預覽。 要查看聚合轉換的數據預覽,關閉表達式生成器並通過「數據預覽」選項卡查看數據。

## <a name="reconnect-rows-and-columns"></a>重新連線列與欄位

聚合轉換類似於 SQL 聚合選擇查詢。 按子句或聚合函數未包含在組中的列不會流到聚合轉換的輸出。 如果要在聚合輸出中包含其他欄,請執行以下方法之一:

* 使用聚合函數,如`last()`或`first()`包括該附加列。
* 使用[自聯接模式](https://mssqldude.wordpress.com/2018/12/20/adf-data-flows-self-join/)將列重新加入輸出流。

## <a name="removing-duplicate-rows"></a>刪除重複列

聚合轉換的常見用途是刪除或標識源數據中的重複條目。 此過程稱為重複數據消除。 以按鍵分組的組,使用您選擇的啟發式方法確定要保留的重複行。 常見的啟發`first()`式是`last()``max()`,`min()`和 。 使用[列模式](concepts-data-flow-column-pattern.md)將規則應用於除按列分組之外的每個列。

![重複](media/data-flow/agg-dedupe.png "重複資料刪除")

在上面的示例中,列`ProductID`和`Name`正在用於分組。 如果兩行的兩列具有相同的值,則它們被視為重複項。 在此聚合轉換中,將保留匹配的第一行的值,並刪除所有其他行的值。 使用列模式語法,所有名稱不是`ProductID`並`Name`映射到其現有列名稱並給出第一個匹配行的值的列。 輸出架構與輸入架構相同。

對於資料驗證方案,`count()`該函數可用於計算有多少重複項。

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

下面的示例採用傳入流`MoviesYear`,按`year`列 對行進行分組。 轉換創建一個聚合列`avgrating`,該列計算到`Rating`列 的平均值。 此聚合轉換名為`AvgComedyRatingsByYear`。

在資料工廠的一個數字的數字, 此轉換類似於下圖:

![依範例群組](media/data-flow/agg-script1.png "依範例群組")

![彙總範例](media/data-flow/agg-script2.png "彙總範例")

此轉換的資料流腳本位於下面的代碼段中。

```
MoviesYear aggregate(
                groupBy(year),
                avgrating = avg(toInteger(Rating))
            ) ~> AvgComedyRatingByYear
```

![彙總資料串流文稿](media/data-flow/aggdfs1.png "彙總資料串流文稿")

```MoviesYear```:衍生定義年份與標題列```AvgComedyRatingByYear```:按年份```avgrating```群組的喜劇平均評級的聚合轉換:為儲存聚合值而建立的新欄的名稱

```
MoviesYear aggregate(groupBy(year),
    avgrating = avg(toInteger(Rating))) ~> AvgComedyRatingByYear
```

## <a name="next-steps"></a>後續步驟

* 使用[視窗轉換](data-flow-window.md)定義的視窗的組合
