---
title: 對應資料流程中的衍生資料行轉換
description: 了解如何使用對應資料流程的衍生資料行轉換，在 Azure Data Factory 中大規模地轉換資料。
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/14/2020
ms.openlocfilehash: 2e90a8779322cf8967ca9a194c6cc760f7c8b8f5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90531957"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>對應資料流程中的衍生資料行轉換

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

使用衍生的資料行轉換可在資料流程中產生新資料行或修改現有欄位。

## <a name="create-and-update-columns"></a>建立和更新資料行

建立衍生的資料行時，您可以產生新的資料行，或更新現有的資料行。 在 [資料 **行** ] 文字方塊中，輸入您要建立的資料行。 若要覆寫架構中的現有資料行，您可以使用資料行下拉式清單。 若要建立衍生資料行的運算式，請按一下 [ **輸入運算式** ] 文字方塊。 您可以開始鍵入運算式，或開啟運算式產生器來建立邏輯。

![衍生的資料行設定](media/data-flow/create-derive-column.png "衍生的資料行設定")

若要加入更多衍生的資料行，請按一下資料行清單上方的 [ **加入** ]，或現有衍生資料行旁邊的加號圖示。 選擇 [新增資料行] 或 [新增資料行模式]。

![新的衍生資料行選項](media/data-flow/add-derived-column.png "新的衍生資料行選項")

### <a name="column-patterns"></a>資料行模式

如果您的架構未明確定義，或您想要大量更新一組資料行，則您會想要建立資料行模式。 資料行模式可讓您使用根據資料行中繼資料的規則來比對資料行，並為每個相符的資料行建立衍生的資料行。 如需詳細資訊，請瞭解如何在「衍生的資料行」轉換中 [建立資料行模式](concepts-data-flow-column-pattern.md#column-patterns-in-derived-column-and-aggregate) 。

![資料行模式](media/data-flow/column-pattern-derive.png "資料行模式")

## <a name="building-schemas-using-the-expression-builder"></a>使用 expression builder 建立架構

使用「對應資料流程 [運算式](concepts-data-flow-expression-builder.md)產生器」時，您可以在 [衍生的資料 **行** ] 區段中建立、編輯和管理衍生的資料行。 系統會列出轉換中建立或變更的所有資料行。 按一下資料行名稱，以互動方式選擇您要編輯的資料行或模式。 若要加入其他資料行，請選取 [ **建立新** 的]，然後選擇您要新增單一資料行或模式。

![建立新的資料行](media/data-flow/derive-add-column.png "建立新的資料行")

使用複雜資料行時，您可以建立個子。 若要這樣做，請按一下任何資料行旁邊的加號圖示，然後選取 [ **新增 subcolumn**]。 如需處理資料流程中複雜類型的詳細資訊，請參閱[對應資料流程中的 JSON 處理](format-json.md#mapping-data-flow-properties)。

![新增子資料行](media/data-flow/derive-add-subcolumn.png "新增子資料行")

如需處理資料流程中複雜類型的詳細資訊，請參閱[對應資料流程中的 JSON 處理](format-json.md#mapping-data-flow-properties)。

![新增複雜資料行](media/data-flow/derive-complex-column.png "新增資料行")

### <a name="locals"></a>本機

如果您要跨多個資料行共用邏輯，或想要可區隔您的邏輯，您可以在「衍生的資料行」轉換中建立本機。 本機是一組邏輯，不會傳播至下列轉換。 您可以在運算式產生器中建立區域變數，方法是前往 **運算式** 專案並選取 [ **區域變數**]。 選取 [ **建立新**的]，以建立一個新的。

![建立本機](media/data-flow/create-local.png "建立本機")

區域變數可以參考任何運算式元素，其中包含函式、輸入架構、參數和其他區域變數的衍生資料行。 參考其他區域變數時，訂單的意義很重要，因為參考的本機必須是目前的「以上」。

![建立本機2](media/data-flow/create-local-2.png "建立本機2")

若要在衍生的資料行中參考本機，請在 [ **運算式** 專案] 視圖中按一下 [本機]，或使用其名稱前面的冒號來參考它。 例如，會參考名為 local1 的本機 `:local1` 。 若要編輯本機定義，請將滑鼠停留在 [運算式元素] 視圖中，然後按一下鉛筆圖示。

![使用區域變數](media/data-flow/using-locals.png "使用區域變數")

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

![衍生範例](media/data-flow/derive-script.png "衍生範例")

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
