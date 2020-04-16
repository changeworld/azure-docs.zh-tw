---
title: 對應資料串流選擇轉換
description: Azure 資料工廠映射資料串流 選擇轉換
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/18/2020
ms.openlocfilehash: 498ce053e8a52897d9087091b92fa51a04e195a4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413260"
---
# <a name="select-transformation-in-mapping-data-flow"></a>在映射資料串轉出轉換

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

使用選擇轉換重新命名、刪除或重新排序列。 此轉換不會更改行數據,而是選擇向下游傳播的列。 

在選擇轉換中,用戶可以指定固定映射、使用模式執行基於規則的映射或啟用自動映射。 固定映射和基於規則的映射都可以在同一選擇轉換中使用。 如果列與定義的映射之一不匹配,則將刪除該列。

## <a name="fixed-mapping"></a>固定對應

如果投影中定義的列少於 50 列,則默認情況下所有定義的列都將具有固定映射。 固定映射採用定義的傳入列,並將其映射為確切名稱。

![固定對應](media/data-flow/fixedmapping.png "固定對應")

> [!NOTE]
> 無法使用固定映射或重新命名的分離欄位

### <a name="mapping-hierarchical-columns"></a>映射分層欄

固定映射可用於將分層列的子列映射到頂級列。 如果您有定義的層次結構,請使用列下拉列表來選擇子列。 選擇轉換將建立新欄,該欄的值和資料類型為子列。

![階層映射](media/data-flow/select-hierarchy.png "階層映射")

## <a name="rule-based-mapping"></a>規則型對應

如果要同時映射許多列或向下游傳遞漂移列,請使用基於規則的映射使用列模式定義映射。 根據`name`、、`type``stream`列`position`匹配。 可以具有固定映射和基於規則的映射的任意組合。 默認情況下,所有大於 50 列的預測將預設為基於規則的映射,該映射與每列匹配並輸出輸入的名稱。 

要新增基於規則的映射,請按**下 「新增映射」** 並選擇 **「 標準」**。

![基於規則的對應](media/data-flow/rule2.png "規則型對應")

每個基於規則的映射都需要兩個輸入:要匹配的條件以及每個映射列的名稱。 這兩個值都是通過[表達式生成器](concepts-data-flow-expression-builder.md)輸入的。 在左側運算式框中,輸入布爾匹配條件。 在正確的運算式框中,指定匹配列將映射到的內容。

![基於規則的對應](media/data-flow/rule-based-mapping.png "規則型對應")

使用`$$`語法引用匹配列的輸入名稱。 以上面的圖像為例,假設使用者希望匹配名稱短於六個字的所有字串列。 如果命名`test`了一個傳入欄,則`$$ + '_short'`表示式將重新命名 。`test_short` 如果這是唯一存在的映射,則不符合條件的所有列都將從輸出的數據中刪除。

模式匹配漂移列和定義的欄。 要查看規則映射的已定義的列,請單擊規則旁邊的眼鏡圖示。 使用數據預覽驗證輸出。

### <a name="regex-mapping"></a>雷格克斯對應

如果按一下向下的 V 形圖示,則可以指定正則表達式映射條件。 正規表示式對應條件與匹配指定正規表示式條件的所有列名稱匹配。 這可以與基於規則的標準映射結合使用。

![基於規則的對應](media/data-flow/regex-matching.png "規則型對應")

上述範例與 regex`(r)`模式 或包含小寫 r 的任何列名稱匹配。 與基於標準規則的映射類似,使用`$$`語法使用右側的條件會更改所有匹配列。

如果列名稱中有多個正則表達式匹配項,則可以使用"n"引用哪個匹配項`$n`引用特定匹配項。 例如,"$2"是指列名中的第二個匹配項。

### <a name="rule-based-hierarchies"></a>基於規則的層次結構

如果定義的投影具有層次結構,則可以使用基於規則的映射來映射層次結構子列。 指定要映射的匹配條件和要映射的子列的複雜列。 每個匹配的子列都將使用右側指定的"名稱為"規則進行輸出。

![基於規則的對應](media/data-flow/rule-based-hierarchy.png "規則型對應")

上述示例與複雜列的所有子列`a`匹配。 `a`包含兩個子列`b``c`與 。 輸出架構將包括兩欄`b`,`c`並且 「名稱為」`$$`條件為 。

### <a name="parameterization"></a>參數化

您可以使用基於規則的映射對列名稱進行參數化。 使用 關鍵```name```字將傳入列名稱與參數匹配。 例如,如果您有一個資料流參數```mycolumn```,則可以創建與等於的任何列名稱匹配的規則```mycolumn```。 您可以將匹配列重新命名為硬編碼字串(如"商務金鑰")並顯式引用它。 在此範例中,匹配條件是```name == $mycolumn```,名稱條件是「業務金鑰」。 

## <a name="auto-mapping"></a>自動映射

新增選擇轉換時,可以通過切換 **「自動映射**」滑動啟用自動映射。 使用自動映射,選擇轉換映射所有傳入列(不包括重複欄),其名稱與其輸入的名稱相同。 這將包括漂移列,這意味著輸出數據可能包含架構中未定義的列。 有關漂移列的詳細資訊,請參閱[架構漂移](concepts-data-flow-schema-drift.md)。

![自動映射](media/data-flow/automap.png "自動映射")

打開自動映射後,選擇轉換將遵循跳過重複設置,並為現有列提供新的別名。 在同一流和自聯接方案中執行多個聯接或查找時,別名非常有用。 

## <a name="duplicate-columns"></a>重覆欄

預設情況下,選擇轉換將刪除輸入和輸出投影中的重複列。 重複的輸入列通常來自聯接和查找轉換,其中列名稱在聯接的每一側重複。 如果將兩個不同的輸入列映射到同一名稱,則可能會出現重複的輸出列。 以切換複選框,選擇是刪除還是傳遞重複列。

![略過重複項目](media/data-flow/select-skip-dup.png "略過重複項目")

## <a name="ordering-of-columns"></a>欄位順序

映射的順序確定輸出列的順序。 如果多次映射輸入列,則僅遵守第一個映射。 對於任何重複的列刪除,將保留第一個匹配項。

## <a name="data-flow-script"></a>資料流程指令碼

### <a name="syntax"></a>語法

```
<incomingStream>
    select(mapColumn(
        each(<hierarchicalColumn>, match(<matchCondition>), <nameCondition> = $$), ## hierarchical rule-based matching
        <fixedColumn>, ## fixed mapping, no rename
        <renamedFixedColumn> = <fixedColumn>, ## fixed mapping, rename
        each(match(<matchCondition>), <nameCondition> = $$), ## rule-based mapping
        each(patternMatch(<regexMatching>), <nameCondition> = $$) ## regex mapping
    ),
    skipDuplicateMapInputs: { true | false },
    skipDuplicateMapOutputs: { true | false }) ~> <selectTransformationName>
```

### <a name="example"></a>範例

下面是選擇對應及資料串流文稿的範例:

![選擇文稿範例](media/data-flow/select-script-example.png "選擇文稿範例")

```
DerivedColumn1 select(mapColumn(
        each(a, match(true())),
        movie,
        title1 = title,
        each(match(name == 'Rating')),
        each(patternMatch(`(y)`),
            $1 + 'regex' = $$)
    ),
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> Select1
```

## <a name="next-steps"></a>後續步驟
* 使用 Select 重新命名、重新排序和別名列後,使用[「接收器」轉換](data-flow-sink.md)將資料放入資料儲存中。
