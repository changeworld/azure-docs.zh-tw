---
title: 映射資料流程中的派生列轉換
description: 瞭解如何使用映射資料流程派生列轉換在 Azure 資料工廠中大規模轉換資料。
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/15/2019
ms.openlocfilehash: 66396de52b3709c1d9357f32a375a29a8dcdbd1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77048742"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>映射資料流程中的派生列轉換

使用派生的列轉換在資料流程中生成新列或修改現有欄位。

## <a name="derived-column-settings"></a>派生列設置

要覆蓋現有列，請通過列下拉清單選擇它。 否則，將列選擇欄位用作文字方塊，並在新列的名稱中鍵入。 要生成派生列的運算式，請按一下"輸入運算式"框以打開["資料流程運算式產生器](concepts-data-flow-expression-builder.md)"。

![派生列設置](media/data-flow/dc1.png "派生列設置")

要添加其他派生列，請將滑鼠懸停在現有派生列上，然後按一下加號圖示。 選擇 **"添加列**"或 **"添加列"模式**。 如果列名稱從源中可變，則列模式可能會派上用場。 有關詳細資訊，請參閱[列模式](concepts-data-flow-column-pattern.md)。

![新派生列選擇](media/data-flow/columnpattern.png "新派生列選擇")

## <a name="build-schemas-in-output-schema-pane"></a>在輸出架構窗格中生成架構

要修改並添加到架構中的列列將列在"輸出架構"窗格中。 您可以在此處以對話模式構建簡單而複雜的資料結構。 要添加其他欄位，請選擇"**添加列**"。 要生成層次結構，請選擇"**添加子列**"。

![添加子列](media/data-flow/addsubcolumn.png "添加子列")

有關處理資料流程中複雜類型的詳細資訊，請參閱[映射資料流程的 JSON 處理](format-json.md#mapping-data-flow-properties)。

![添加複雜列](media/data-flow/complexcolumn.png "新增資料行")

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

下面的示例是名為的`CleanData`派生列，該列獲取傳入流`MoviesYear`並創建兩個派生列。 第一個派生列將列`Rating`替換為評級的值作為整數類型。 第二個派生列是匹配名稱以"電影"開頭的每個列的模式。 對於每個匹配列，它創建一個`movie`列，該列等於以"movie_"為預綴的匹配列的值。 

在資料工廠 UX 中，此轉換類似于下圖：

![派生示例](media/data-flow/derive-script1.png "派生示例")

此轉換的資料流程腳本位於下面的程式碼片段中：

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

- 瞭解有關[映射資料流程運算式語言](data-flow-expression-functions.md)的更多資訊。
