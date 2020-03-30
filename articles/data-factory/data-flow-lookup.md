---
title: 映射資料流程的查找轉換
description: 在映射資料流程時使用查找轉換從其他源參考資料。
author: kromerm
ms.reviewer: daperlov
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/23/2020
ms.openlocfilehash: 78c6c1363af011a90865770d88c0037e50e958c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240392"
---
# <a name="lookup-transformation-in-mapping-data-flow"></a>映射資料流程的查找轉換

使用查找轉換引用來自資料流程中另一個源的資料。 查找轉換將列從匹配的資料追加到來源資料。

查找轉換類似于左外部聯結。 主流中的所有行都將存在於輸出流中，並包含來自查找流的其他列。 

## <a name="configuration"></a>組態

![查閱轉換](media/data-flow/lookup1.png "查閱")

**主流：** 傳入的資料流程。 此流等效于聯接的左側。

**查找流：** 追加到主流的資料。 添加哪些資料由查找條件決定。 此流等效于聯接的右側。

**匹配多行：** 如果啟用，主流中具有多個匹配項的行將返回多行。 否則，將僅根據"匹配"條件返回一行。

**匹配：** 僅當啟用了"匹配多行"時，才可見。 選擇是匹配任何行、第一個匹配項還是最後一個匹配項。 建議任何行執行最快。 如果選擇了第一行或最後一行，則需要指定排序條件。

**查找條件：** 選擇要匹配的列。 如果滿足相等條件，則行將被視為匹配項。 懸停並選擇"計算列"以使用[資料流程運算式語言](data-flow-expression-functions.md)提取值。

查找轉換僅支援相等匹配。 要自訂查找運算式以包括其他運算子（如大於，建議[在聯接轉換中使用交叉聯接](data-flow-join.md#custom-cross-join)）。 交叉聯接將避免執行時可能出現的點菜產品錯誤。

輸出資料中包含來自兩個流的所有列。 要刪除重複列或不需要的列，請在查找轉換後添加[選擇轉換](data-flow-select.md)。 列也可以在接收器轉換中刪除或重命名。

## <a name="analyzing-matched-rows"></a>分析匹配的行

查找轉換後，該函數`isMatch()`可用於查看查找是否匹配單個行。

![查找模式](media/data-flow/lookup111.png "查找模式")

此模式的一個示例是使用條件拆分轉換在`isMatch()`函數上拆分。 在上面的示例中，匹配的行通過頂部流，不匹配的行流通過```NoMatch```流。

## <a name="testing-lookup-conditions"></a>測試查找條件

在偵錯模式下使用資料預覽測試查找轉換時，請使用一小組已知資料。 從大型資料集對行進行採樣時，無法預測將讀取哪些行和鍵進行測試。 結果是非確定性的，這意味著您的聯結條件可能不會返回任何匹配項。

## <a name="broadcast-optimization"></a>廣播優化

在 Azure 資料工廠中，映射資料流程在橫向擴展的 Spark 環境中執行。 如果資料集可以適合輔助節點記憶體空間，則可以通過啟用廣播來優化查找性能。

![廣播加入](media/data-flow/broadcast.png "廣播加入")

啟用廣播會將整個資料集推送到記憶體中。 對於僅包含幾千行的小型資料集，廣播可以大大提高查找性能。 對於大型資料集，此選項可能導致記憶體不足異常。

## <a name="data-flow-script"></a>資料流程指令碼

### <a name="syntax"></a>語法

```
<leftStream>, <rightStream>
    lookup(
        <lookupConditionExpression>,
        multiple: { true | false },
        pickup: { 'first' | 'last' | 'any' },  ## Only required if false is selected for multiple
        { desc | asc }( <sortColumn>, { true | false }), ## Only required if 'first' or 'last' is selected. true/false determines whether to put nulls first
        broadcast: { 'none' | 'left' | 'right' | 'both' }
    ) ~> <lookupTransformationName>
```
### <a name="example"></a>範例

![查閱轉換](media/data-flow/lookup-dsl-example.png "查閱")

上述查找配置的資料流程腳本位於下面的程式碼片段中。

```
SQLProducts, DimProd lookup(ProductID == ProductKey,
    multiple: false,
    pickup: 'first',
    asc(ProductKey, true),
    broadcast: 'none')~> LookupKeys
```
## 
後續步驟

* [聯接](data-flow-join.md)和[存在](data-flow-exists.md)轉換都採用多個流輸入
* 使用[條件拆分轉換](data-flow-conditional-split.md)與```isMatch()```在匹配值和非匹配值上拆分行
