---
title: 對應資料流程中的匯總轉換
description: 瞭解如何使用對應的資料流程匯總轉換，在 Azure Data Factory 中大規模匯總資料。
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/14/2020
ms.openlocfilehash: 71f5488b1f689e8892155b013730bcbb3c8e0e35
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2020
ms.locfileid: "90531913"
---
# <a name="aggregate-transformation-in-mapping-data-flow"></a>對應資料流程中的匯總轉換

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

「匯總」轉換會在資料流程中定義資料行的匯總。 使用運算式產生器，您可以定義不同類型的匯總，例如 SUM、MIN、MAX 以及依現有或計算資料行分組的計數。

## <a name="group-by"></a>群組依據

選取現有的資料行，或建立新的計算資料行，以做為匯總的 group by 子句使用。 若要使用現有的資料行，請從下拉式清單中選取它。 若要建立新的計算資料行，請將滑鼠停留在子句上，然後按一下 [ **計算資料行**]。 這會開啟 [資料流程運算式](concepts-data-flow-expression-builder.md)產生器。 一旦您建立計算資料行，請在 [ **名稱為** ] 欄位中輸入輸出資料行名稱。 如果您想要新增其他 group by 子句，請將滑鼠停留在現有的子句上方，然後按一下加號圖示。

![依設定匯總轉換群組](media/data-flow/agg.png "依設定匯總轉換群組")

在匯總轉換中，group by 子句是選擇性的。

## <a name="aggregate-columns"></a>匯總資料行

移至 [ **匯總** ] 索引標籤以建立匯總運算式。 您可以使用匯總來覆寫現有的資料行，或使用新的名稱來建立新的欄位。 在 [資料行名稱選取器] 旁的右側方塊中輸入匯總運算式。 若要編輯運算式，請按一下文字方塊，然後開啟 [運算式產生器]。 若要加入更多匯總資料行，請按一下資料行清單上方的 [ **加入** ]，或現有匯總資料行旁的加號圖示。 選擇 [新增資料行] 或 [新增資料行模式]。 每個匯總運算式必須包含至少一個彙總函式。

![匯總設定](media/data-flow/aggregate-columns.png "匯總設定")

> [!NOTE]
> 在「偵測模式」中，「運算式產生器」無法使用彙總函式來產生資料預覽。 若要查看匯總轉換的資料預覽，請關閉運算式產生器，並透過 [資料預覽] 索引標籤來查看資料。

### <a name="column-patterns"></a>資料行模式

使用資料 [行模式](concepts-data-flow-column-pattern.md) ，將相同的匯總套用至一組資料行。 如果您想要保存輸入架構的許多資料行（依預設卸載），這會很有用。 使用啟發學習法（例如） `first()` 透過匯總保存輸入資料行。

## <a name="reconnect-rows-and-columns"></a>重新連接資料列和資料行

匯總轉換類似于 SQL 匯總選取查詢。 未包含在 group by 子句或彙總函式中的資料行，將不會流經匯總轉換的輸出。 如果您想要在匯總輸出中包含其他資料行，請執行下列其中一種方法：

* 使用彙總函式（例如 `last()` 或） `first()` 來包含該額外資料行。
* 使用 [自我聯結模式](https://mssqldude.wordpress.com/2018/12/20/adf-data-flows-self-join/)，將資料行重新加入至您的輸出資料流程。

## <a name="removing-duplicate-rows"></a>移除重複的資料列

「匯總」轉換的常見用法是移除或識別來源資料中的重複專案。 此處理程式稱為重復資料刪除。 以一組分組依據索引鍵為基礎，使用您選擇的啟發學習法來決定要保留的重復資料列。 常見的啟發式為 `first()` 、 `last()` 、 `max()` 和 `min()` 。 使用資料 [行模式](concepts-data-flow-column-pattern.md) ，將規則套用至每個資料行（依資料行分組除外）。

![重複](media/data-flow/agg-dedupe.png "重複資料刪除")

在上述範例中，資料行和正在用於 `ProductID` `Name` 群組。 如果兩個數據列在這兩個數據行中具有相同的值，則會被視為重複專案。 在此匯總轉換中，會保留第一個相符資料列的值，而且所有其他資料列都會被捨棄。 使用資料行模式語法時，所有名稱不是和的資料 `ProductID` `Name` 行都會對應至其現有的資料行名稱，並指定第一個相符資料列的值。 輸出架構與輸入架構相同。

若為數據驗證案例， `count()` 函數可以用來計算有多少重複的專案。

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

下列範例會採用傳入的資料流程 `MoviesYear` ，並依資料行將資料列分組 `year` 。 轉換 `avgrating` 會建立評估為數據行平均值的匯總資料行 `Rating` 。 此匯總轉換的名稱為 `AvgComedyRatingsByYear` 。

在 Data Factory UX 中，這項轉換看起來如下圖所示：

![依範例分組](media/data-flow/agg-script1.png "依範例分組")

![匯總範例](media/data-flow/agg-script2.png "匯總範例")

此轉換的資料流程腳本位於下列程式碼片段中。

```
MoviesYear aggregate(
                groupBy(year),
                avgrating = avg(toInteger(Rating))
            ) ~> AvgComedyRatingByYear
```

![匯總資料流程腳本](media/data-flow/aggdfs1.png "匯總資料流程腳本")

```MoviesYear```：定義 year 和 title 資料行的衍生資料行 ```AvgComedyRatingByYear``` ：依年份分組之平均 comedies 評等的匯總轉換 ```avgrating``` ：要建立以保存匯總值的新資料行名稱

```
MoviesYear aggregate(groupBy(year),
    avgrating = avg(toInteger(Rating))) ~> AvgComedyRatingByYear
```

## <a name="next-steps"></a>後續步驟

* 使用[視窗轉換](data-flow-window.md)定義以視窗為基礎的匯總
