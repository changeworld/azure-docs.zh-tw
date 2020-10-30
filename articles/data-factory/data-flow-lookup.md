---
title: 對應資料流程中的查閱轉換
description: 使用對應資料流程中的查閱轉換，參考另一個來源的資料。
author: kromerm
ms.reviewer: daperlov
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/28/2020
ms.openlocfilehash: 70787f1d918064b48d37ce051bfdd2aba49472ea
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93040182"
---
# <a name="lookup-transformation-in-mapping-data-flow"></a>對應資料流程中的查閱轉換

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

使用查閱轉換在資料流程串流中參考另一個來源的資料。 查閱轉換會將相符資料的資料行附加到您的來源資料。

查閱轉換類似於左方外部聯結。 主要串流中的所有資料列將會存在於輸出串流中，並具有來自查閱串流的其他資料行。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4xsVT]

## <a name="configuration"></a>組態

![螢幕擷取畫面顯示 [查閱設定] 索引標籤，其中包含下列文字所述的標籤。](media/data-flow/lookup1.png "查閱")

**主要串流：** 傳入的資料串流。 此串流相當於聯結的左側。

**查閱串流：** 附加至主要串流的資料。 要新增的資料會由查閱條件決定。 這個串流相當於聯結的右側。

**符合多個資料列：** 如果啟用，在主要串流中具有多個相符項目的資料列就會傳回多個資料列。 否則，只會根據「相符」條件傳回單一資料列。

**相符：** 只有在未選取 [符合多個資料列] 時才會顯示。 選擇要符合任何資料列、最初相符還是最後相符。 建議使用任何資料列，因為其執行速度最快。 如果選取了第一個資料列或最後一個資料列，您就必須指定排序條件。

**查閱條件：** 選擇要符合的資料行。 如果符合相等條件，則會將資料列視為相符。 暫留游標並選取 [計算資料行]，以使用[資料流程運算式語言](data-flow-expression-functions.md)來擷取值。

查閱轉換僅支援相等相符。 若要自訂查閱運算式以包含其他運算子 (例如大於)，建議您使用[聯結轉換中的交叉聯結](data-flow-join.md#custom-cross-join)。 交叉聯結可避免執行時發生任何可能的笛卡兒乘積錯誤。

這兩個串流中的所有資料行都會包含在輸出資料中。 若要捨棄重複或不必要的資料行，請在查閱轉換之後新增[選取轉換](data-flow-select.md)。 您也可以在接收轉換中捨棄或重新命名資料行。

### <a name="non-equi-joins"></a>不相等聯結

若要在查閱條件中使用條件運算子，例如不相等 (!=) 或大於 (>)，請變更兩個資料行之間的運算子下拉式清單。 不相等聯結需要至少廣播兩個串流的其中一個，方法為使用 [最佳化] 索引標籤中的 [固定] 廣播。

![不相等查閱](media/data-flow/non-equi-lookup.png "不相等查閱")

## <a name="analyzing-matched-rows"></a>分析符合的資料列

在查閱轉換之後，可使用函式 `isMatch()` 來查看查閱是否符合個別資料列。

![查閱模式](media/data-flow/lookup111.png "查閱模式")

此模式的範例是使用條件式分割轉換來分割 `isMatch()` 函式。 在上述範例中，相符資料列會通過最上方的串流，而不符合的資料列則會流經 ```NoMatch``` 串流。

## <a name="testing-lookup-conditions"></a>測試查閱條件

在偵錯模式中使用資料預覽來測試查閱轉換時，請使用小型的已知資料集。 從大型資料集中取樣資料列時，您無法預測將讀取哪些資料列和索引鍵進行測試。 結果是不具確定性的，這表示您的聯結條件可能不會傳回任何相符項。

## <a name="broadcast-optimization"></a>廣播最佳化

![廣播聯結](media/data-flow/broadcast.png "廣播聯結")

在聯結、查閱和存在轉換中，如果其中一個或兩個資料流納入背景工作角色節點記憶體中，您可以藉由啟用 [廣播] 來最佳化效能。 根據預設，Spark 引擎會自動決定是否要廣播一邊。 若要手動選擇廣播哪一邊，請選取 [固定]。

除非您的聯結遇到逾時錯誤，否則不建議透過 [關閉] 選項停用廣播。

## <a name="data-flow-script"></a>資料流程指令碼

### <a name="syntax"></a>語法

```
<leftStream>, <rightStream>
    lookup(
        <lookupConditionExpression>,
        multiple: { true | false },
        pickup: { 'first' | 'last' | 'any' },  ## Only required if false is selected for multiple
        { desc | asc }( <sortColumn>, { true | false }), ## Only required if 'first' or 'last' is selected. true/false determines whether to put nulls first
        broadcast: { 'auto' | 'left' | 'right' | 'both' | 'off' }
    ) ~> <lookupTransformationName>
```
### <a name="example"></a>範例

![螢幕擷取畫面：顯示下列程式碼的 [查閱設定] 索引標籤。](media/data-flow/lookup-dsl-example.png "查閱")

上述查閱設定的資料流程指令碼位於下列程式碼片段中。

```
SQLProducts, DimProd lookup(ProductID == ProductKey,
    multiple: false,
    pickup: 'first',
    asc(ProductKey, true),
    broadcast: 'auto')~> LookupKeys
```
## 
後續步驟

* [聯結](data-flow-join.md)和[存在](data-flow-exists.md)轉換都會接受多個串流輸入
* 使用[條件式分割轉換](data-flow-conditional-split.md)搭配 ```isMatch()``` 來分割相符和不相符值的資料列
