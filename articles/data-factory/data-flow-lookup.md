---
title: 對應資料流程中的查閱轉換
description: 使用對應資料流程中的查閱轉換，從另一個來源參考資料。
author: kromerm
ms.reviewer: daperlov
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/23/2020
ms.openlocfilehash: af4e33e2653aebe5d1c979aa314463e4beb7b0d7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82233392"
---
# <a name="lookup-transformation-in-mapping-data-flow"></a>對應資料流程中的查閱轉換

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

使用「查閱」轉換來參考資料流資料流程中另一個來源的資料。 「查閱」轉換會將資料行從相符的資料附加到您的來源資料。

「查閱」轉換類似于左方外部聯結。 主要資料流程中的所有資料列將會存在於輸出資料流程中，並具有查找資料流程的其他資料行。 

## <a name="configuration"></a>設定

![查閱轉換](media/data-flow/lookup1.png "查閱")

**主要資料流程：** 傳入的資料流程。 此資料流程相當於聯結的左側。

**查閱資料流程：** 附加至主要資料流程的資料。 要新增的資料是由查閱條件所決定。 這個資料流程相當於聯結的右邊。

**符合多個資料列：** 如果啟用，在主要資料流程中具有多個相符專案的資料列會傳回多個資料列。 否則，只會根據「相符」條件傳回單一資料列。

**符合：** 只有在已啟用 [符合多個資料列] 時才會顯示。 選擇要在任何資料列、第一個比對或最後一個相符項上進行比對。 建議使用任何資料列，因為它執行速度最快。 如果選取了 [第一個資料列] 或 [最後一個資料列]，您將需要指定排序條件。

**查閱條件：** 選擇要比對的資料行。 如果符合相等條件，則會將資料列視為相符。 將滑鼠暫留並選取 [計算資料行]，以使用[資料流程運算式語言](data-flow-expression-functions.md)將值解壓縮。

查閱轉換僅支援相等相符專案。 若要自訂查閱運算式以包含其他運算子，例如大於，建議您[在聯結轉換中使用交叉聯結](data-flow-join.md#custom-cross-join)。 交叉聯結可避免執行任何可能的笛卡兒產品錯誤。

這兩個數據流中的所有資料行都包含在輸出資料中。 若要卸載重複或不必要的資料行，請在您的查閱轉換之後加入[select 轉換](data-flow-select.md)。 您也可以在接收轉換中卸載或重新命名資料行。

## <a name="analyzing-matched-rows"></a>分析符合的資料列

在查閱轉換之後，函數`isMatch()`可以用來查看查閱是否符合個別資料列。

![查閱模式](media/data-flow/lookup111.png "查閱模式")

此模式的範例是使用「條件式分割」轉換來分割`isMatch()`函式。 在上述範例中，比對資料列會經過最上層資料流程，而非相符的```NoMatch```資料列會流經資料流程。

## <a name="testing-lookup-conditions"></a>測試查閱條件

在 [偵測] 模式中使用 [資料預覽] 測試查閱轉換時，請使用一小組已知資料。 從大型資料集取樣資料列時，您無法預測要讀取哪些資料列和索引鍵進行測試。 結果是不具決定性的，這表示您的聯結條件可能不會傳回任何相符專案。

## <a name="broadcast-optimization"></a>廣播優化

![廣播聯結](media/data-flow/broadcast.png "廣播聯結")

在聯結、查閱和存在轉換中，如果其中一個或兩個數據流符合背景工作節點記憶體，您可以藉由啟用**廣播**來優化效能。 根據預設，spark 引擎會自動決定是否要廣播一邊。 若要手動選擇要廣播的端，請選取 [**固定**]。

除非您的聯結遇到逾時錯誤，否則不建議使用 [**關閉**] 選項來停用廣播。

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

![查閱轉換](media/data-flow/lookup-dsl-example.png "查閱")

上述查閱設定的資料流程腳本位於下列程式碼片段中。

```
SQLProducts, DimProd lookup(ProductID == ProductKey,
    multiple: false,
    pickup: 'first',
    asc(ProductKey, true),
    broadcast: 'auto')~> LookupKeys
```
## 
後續步驟

* [聯結](data-flow-join.md)和[exists](data-flow-exists.md)轉換都採用多個資料流程輸入
* 搭配使用[條件式分割轉換](data-flow-conditional-split.md) ```isMatch()```來分割相符和不相符值的資料列
