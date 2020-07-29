---
title: 對應資料流程中的匯總轉換
description: 瞭解如何使用對應資料流程匯總轉換，在 Azure Data Factory 中大規模匯總資料。
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/24/2020
ms.openlocfilehash: 871f2b49e2dce9d762ef8a54923da04b0f24e4be
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81606541"
---
# <a name="aggregate-transformation-in-mapping-data-flow"></a>對應資料流程中的匯總轉換

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

「匯總」轉換會定義資料流程中資料行的匯總。 使用運算式產生器，您可以定義不同類型的匯總，例如 SUM、MIN、MAX 和 COUNT，並依現有或計算資料行分組。

## <a name="group-by"></a>群組依據

選取現有的資料行，或建立新的計算資料行，作為匯總的 group by 子句使用。 若要使用現有的資料行，請從下拉式清單中選取它。 若要建立新的計算資料行，請將滑鼠停留在子句上，然後按一下 [**計算資料行**]。 這會開啟[資料流程運算式](concepts-data-flow-expression-builder.md)產生器。 建立計算資料行之後，請在 [**名稱**] 欄位中輸入輸出資料行名稱。 如果您想要加入額外的 group by 子句，請將滑鼠停留在現有的子句上方，然後按一下加號圖示。

![依設定匯總轉換群組](media/data-flow/agg.png "依設定匯總轉換群組")

Group by 子句在匯總轉換中是選擇性的。

## <a name="aggregate-column"></a>匯總資料行 

移至 [**匯總**] 索引標籤以建立匯總運算式。 您可以使用匯總來覆寫現有的資料行，或使用新的名稱來建立新的欄位。 匯總運算式會在資料行名稱選取器旁的右側方塊中輸入。 若要編輯運算式，請按一下文字方塊來開啟 [運算式產生器]。 若要加入其他匯總，請將滑鼠停留在現有的運算式上，然後按一下加號圖示，以建立新的匯總資料行或資料[行模式](concepts-data-flow-column-pattern.md)。

每個匯總運算式都必須包含至少一個彙總函式。

![匯總轉換匯總設定](media/data-flow/agg2.png "匯總轉換匯總設定")


> [!NOTE]
> 在 [調試] 模式中，[運算式產生器] 無法使用彙總函式來產生資料預覽。 若要查看匯總轉換的資料預覽，請關閉 [運算式產生器]，並透過 [資料預覽] 索引標籤來查看資料。

## <a name="reconnect-rows-and-columns"></a>重新連接資料列和資料行

匯總轉換類似 SQL 匯總選取查詢。 不包含在 group by 子句或彙總函式中的資料行，將不會流經匯總轉換的輸出。 如果您想要在匯總輸出中包含其他資料行，請執行下列其中一種方法：

* 使用彙總函式（例如 `last()` 或） `first()` 以包含該額外的資料行。
* 使用[自我聯結模式](https://mssqldude.wordpress.com/2018/12/20/adf-data-flows-self-join/)，將資料行重新加入至您的輸出資料流程。

## <a name="removing-duplicate-rows"></a>移除重複的資料列

「匯總」轉換的常見用法是移除或識別來源資料中的重複專案。 此程式稱為「重復資料刪除」。 根據一組依據索引鍵，使用您選擇的啟發學習法來判斷要保留的重復資料列。 一般啟發學習法為 `first()` 、 `last()` 、 `max()` 和 `min()` 。 使用資料[行模式](concepts-data-flow-column-pattern.md)將規則套用到每個資料行，但 [群組依據] 資料行除外。

![重複](media/data-flow/agg-dedupe.png "重複資料刪除")

在上述範例中， `ProductID` 會使用資料行和 `Name` 來進行群組。 如果兩個數據列的值相同，則會將它們視為重複。 在此匯總轉換中，會保留第一個符合的資料列值，並捨棄其他所有專案。 使用資料行模式語法時，所有名稱不 `ProductID` 是和的資料 `Name` 行都會對應到現有的資料行名稱，並指定第一個相符資料列的值。 輸出架構與輸入架構相同。

對於資料驗證案例， `count()` 函數可以用來計算有多少重複專案。

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

下列範例會接受傳入的資料流程 `MoviesYear` ，並依資料行分組資料列 `year` 。 轉換 `avgrating` 會建立評估為數據行平均值的匯總資料行 `Rating` 。 這個匯總轉換名為 `AvgComedyRatingsByYear` 。

在 Data Factory UX 中，這項轉換看起來如下圖所示：

![依範例分組](media/data-flow/agg-script1.png "依範例分組")

![匯總範例](media/data-flow/agg-script2.png "匯總範例")

此轉換的資料流程腳本位於下列程式碼片段。

```
MoviesYear aggregate(
                groupBy(year),
                avgrating = avg(toInteger(Rating))
            ) ~> AvgComedyRatingByYear
```

![匯總資料流程腳本](media/data-flow/aggdfs1.png "匯總資料流程腳本")

```MoviesYear```：定義年份和標題資料行的衍生資料行 ```AvgComedyRatingByYear``` ：依年份分組之平均 comedies 評等的匯總轉換 ```avgrating``` ：所建立之新資料行的名稱，用以保存匯總值

```
MoviesYear aggregate(groupBy(year),
    avgrating = avg(toInteger(Rating))) ~> AvgComedyRatingByYear
```

## <a name="next-steps"></a>後續步驟

* 使用[視窗轉換](data-flow-window.md)來定義以視窗為基礎的匯總
