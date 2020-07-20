---
title: 對應資料流程中的衍生資料行轉換
description: 了解如何使用對應資料流程的衍生資料行轉換，在 Azure Data Factory 中大規模地轉換資料。
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/15/2019
ms.openlocfilehash: 38ec2d4619f47bf9fc4d1815cb6e9990cef72dcf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81606493"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>對應資料流程中的衍生資料行轉換

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

使用衍生的資料行轉換可在資料流程中產生新資料行或修改現有欄位。

## <a name="derived-column-settings"></a>衍生的資料行設定

若要覆寫現有資料行，請透過 [資料行] 下拉式清單加以選取。 否則，請使用資料行選取欄位作為文字方塊，並鍵入新資料行的名稱。 若要建立衍生資料行的運算式，請按一下 [輸入運算式] 方塊以開啟[資料流程運算式產生器](concepts-data-flow-expression-builder.md)。

![衍生的資料行設定](media/data-flow/dc1.png "衍生的資料行設定")

若要新增其他衍生的資料行，請將滑鼠移至現有衍生的資料行上方，然後按一下加號圖示。 選擇 [新增資料行] 或 [新增資料行模式]。 如果資料行名稱是來源的變數，則資料行模式可能會很有用。 如需詳細資訊，請參閱[資料行模式](concepts-data-flow-column-pattern.md)。

![新的衍生資料行選項](media/data-flow/columnpattern.png "新的衍生資料行選項")

## <a name="build-schemas-in-output-schema-pane"></a>在輸出結構描述窗格中建立結構描述

您要修改並新增至結構描述的資料行會列在 [輸出結構描述] 窗格中。 您可在這裡以互動方式建立簡單和複雜的資料結構。 若要新增其他欄位，請選取 [新增資料行]。 若要建立階層，請選取 [新增子資料行]。

![新增子資料行](media/data-flow/addsubcolumn.png "新增子資料行")

如需處理資料流程中複雜類型的詳細資訊，請參閱[對應資料流程中的 JSON 處理](format-json.md#mapping-data-flow-properties)。

![新增複雜資料行](media/data-flow/complexcolumn.png "新增資料行")

## <a name="data-flow-script"></a>資料流程指令碼

### <a name="syntax"></a>語法

```
<incomingStream>
    derive(
           <columnName1> = <expression1>,
           <columnName2> = <expression2>,
           each(
                match(matchExpression),
                <metadataColumn1> = <metadataExpression1>,
                <metadataColumn2> = <metadataExpression2>
               )
          ) ~> <deriveTransformationName>
```

### <a name="example"></a>範例

以下範例是一個名為 `CleanData` 的衍生資料行，其會接受傳入的資料流 `MoviesYear` 並建立兩個所衍生資料行。 第一個所衍生資料行會將資料行 `Rating` 取代為整數類型的分級值。 第二個衍生的資料行是一種模式，其會比對名稱開頭為 'movies' 的每個資料行。 針對每個相符的資料行，它會建立一個資料行 `movie`，其值等於前面加上 'movie_' 的相符資料行值。 

在 Data Factory UX 中，這項轉換看起來如下圖所示：

![衍生範例](media/data-flow/derive-script1.png "衍生範例")

此轉換的資料流指令碼位於下列程式碼片段中：

```
MoviesYear derive(
                Rating = toInteger(Rating),
                each(
                    match(startsWith(name,'movies')),
                    'movie' = 'movie_' + toString($$)
                )
            ) ~> CleanData
```

## <a name="next-steps"></a>後續步驟

- 深入了解[對應資料流程運算式語言](data-flow-expression-functions.md)。
