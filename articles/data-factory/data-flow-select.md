---
title: 在對應資料流程中選取轉換
description: Azure Data Factory 對應資料流程選取轉換
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/18/2020
ms.openlocfilehash: a90a2def874c7f081f83a34aea956083eb72879a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81686505"
---
# <a name="select-transformation-in-mapping-data-flow"></a>在對應資料流程中選取轉換

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

使用 [選取轉換] 來重新命名、卸載或重新排列資料行。 這種轉換並不會改變數據列資料，而是會選擇下游傳播的資料行。 

在 [選取] 轉換中，使用者可以指定固定對應、使用模式來執行以規則為基礎的對應，或啟用自動對應。 固定和以規則為基礎的對應可同時在相同的 select 轉換中使用。 如果資料行不符合其中一個已定義的對應，就會將它卸載。

## <a name="fixed-mapping"></a>固定對應

如果您的投影中定義了少於50個數據行，則所有定義的資料行預設都會有固定的對應。 固定對應接受已定義的傳入資料行，並將它對應到確切的名稱。

![固定對應](media/data-flow/fixedmapping.png "固定對應")

> [!NOTE]
> 您無法使用固定對應來對應或重新命名漂移資料行

### <a name="mapping-hierarchical-columns"></a>對應階層式資料行

固定對應可以用來將階層式資料行的 subcolumn 對應至最上層資料行。 如果您有已定義的階層，請使用 [資料行] 下拉式清單來選取 subcolumn。 [選取] 轉換會建立新的資料行，其中包含 subcolumn 的值和資料類型。

![階層式對應](media/data-flow/select-hierarchy.png "階層式對應")

## <a name="rule-based-mapping"></a>規則型對應

如果您想要一次對應許多資料行，或傳遞漂移的下游資料行，請使用以規則為基礎的對應來定義使用資料行模式的對應。 根據`name`、 `type`、 `stream`和資料行進行`position`比對。 您可以有固定和以規則為基礎之對應的任意組合。 根據預設，具有大於50資料行的所有投影都會預設為以規則為基礎的對應，這會在每個資料行上符合，並輸出輸入的名稱。 

若要新增以規則為基礎的對應，請按一下 [**新增對應**]，然後選取 [以**規則為主的對應**]。

![以規則為基礎的對應](media/data-flow/rule2.png "規則型對應")

每個以規則為基礎的對應都需要兩個輸入：要比對的條件和每個對應資料行的名稱。 這兩個值都是透過[運算式](concepts-data-flow-expression-builder.md)產生器來輸入。 在 [左側運算式] 方塊中，輸入您的布林值比對條件。 在 [右運算式] 方塊中，指定符合的資料行將對應的目標。

![以規則為基礎的對應](media/data-flow/rule-based-mapping.png "規則型對應")

使用`$$`語法來參考相符資料行的輸入名稱。 使用上述影像做為範例，假設使用者想要比對名稱少於六個字元的所有字串資料行。 如果其中一個傳入資料行`test`名為， `$$ + '_short'`則運算式會重新`test_short`命名資料行。 如果這是唯一存在的對應，則不符合條件的所有資料行都會從輸出資料中卸載。

模式同時符合漂移和已定義的資料行。 若要查看哪些定義的資料行是由規則所對應，請按一下規則旁的眼鏡圖示。 使用資料預覽來驗證您的輸出。

### <a name="regex-mapping"></a>Regex 對應

如果您按一下向下的 v 形圖示，就可以指定 RegEx 對應條件。 Regex 對應條件符合所有符合指定 RegEx 條件的資料行名稱。 這可與以標準規則為基礎的對應搭配使用。

![以規則為基礎的對應](media/data-flow/regex-matching.png "規則型對應")

上述範例符合 RegEx 模式`(r)`或任何包含小寫 r 的資料行名稱。 與標準規則型對應類似，所有相符的資料行都是由右邊的條件使用`$$`語法來改變。

如果您的資料行名稱中有多個 RegEx 相符專案，您可以使用`$n` （其中 ' n ' 所參考的相符專案）來參考特定的相符專案。 例如，' $2 ' 指的是資料行名稱中的第二個相符項。

### <a name="rule-based-hierarchies"></a>以規則為基礎的階層

如果您定義的投射具有階層，您可以使用以規則為基礎的對應來對應階層個子。 指定要對應其個子的比對條件和複雜資料行。 每個相符的 subcolumn 都會使用右側指定的 [名稱 as] 規則來輸出。

![以規則為基礎的對應](media/data-flow/rule-based-hierarchy.png "規則型對應")

上述範例符合所有個子的複雜資料行`a`。 `a`包含兩個`b`個子`c`和。 輸出架構會包含兩個數據`b`行`c` ，而 [名稱為] 條件為`$$`。

### <a name="parameterization"></a>參數化

您可以使用以規則為基礎的對應來參數化資料行名稱。 使用關鍵字```name```來比對傳入的資料行名稱與參數。 例如，如果您有一個資料流程參數```mycolumn```，您可以建立符合任何資料行名稱的規則。 ```mycolumn``` 您可以將相符的資料行重新命名為硬式編碼的字串，例如「商務索引鍵」並明確地參考它。 在此範例中，比對條件```name == $mycolumn```為，且名稱條件為「商務索引鍵」。 

## <a name="auto-mapping"></a>自動對應

新增 [選取] 轉換時，可以藉由切換 [自動對應] 滑杆來啟用**自動對應**。 使用自動對應時，[選取] 轉換會對應具有與輸入相同名稱的所有傳入資料行（不包括重複專案）。 這將包含漂移資料行，這表示輸出資料可能包含您的架構中未定義的資料行。 如需有關漂移資料行的詳細資訊，請參閱[架構漂移](concepts-data-flow-schema-drift.md)。

![自動對應](media/data-flow/automap.png "自動對應")

使用自動對應時，select 轉換會接受略過重複的設定，並為現有的資料行提供新的別名。 在相同資料流程和自我聯結案例中執行多個聯結或查閱時，別名會很有用。 

## <a name="duplicate-columns"></a>重複的資料行

根據預設，[選取] 轉換會在輸入和輸出投影中卸載重複的資料行。 重複的輸入資料行通常來自聯結和查閱轉換，其中資料行名稱會在聯結的每一端重複。 如果您將兩個不同的輸入資料行對應到相同的名稱，就可能發生重複的輸出資料行。 切換核取方塊，選擇是否要卸載或傳遞重複的資料行。

![略過重複專案](media/data-flow/select-skip-dup.png "略過重複專案")

## <a name="ordering-of-columns"></a>資料行的排序

對應的順序會決定輸出資料行的順序。 如果輸入資料行多次對應，則只會接受第一個對應。 若為任何重複的資料行卸載，則會保留第一個相符項。

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

以下是選取對應和其資料流程腳本的範例：

![選取腳本範例](media/data-flow/select-script-example.png "選取腳本範例")

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
* 使用 [選取] 來重新命名、重新排列和建立別名資料行之後，請使用 [[接收] 轉換](data-flow-sink.md)，將您的資料放入資料存放區中。
