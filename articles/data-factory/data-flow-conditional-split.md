---
title: 映射資料流程中的條件拆分轉換
description: 使用 Azure 資料工廠映射資料流程中的條件拆分轉換將資料拆分到不同的流中
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
ms.openlocfilehash: d7e2af6c98951e685192656b37226716e4340bfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930436"
---
# <a name="conditional-split-transformation-in-mapping-data-flow"></a>映射資料流程中的條件拆分轉換

條件拆分轉換根據匹配條件將資料行路由到不同的流。 條件拆分轉換類似于程式設計語言中的 CASE 決策結構。 轉換會評估運算式，並根據結果將資料列導向特定資料流。

## <a name="configuration"></a>組態

"**拆分"** 設置確定資料行是流向第一個匹配流還是與其匹配的每個流。

使用資料流程運算式產生器為拆分條件輸入運算式。 要添加新條件，請按一下現有行中的加號圖示。 對於與任何條件不匹配的行，也可以添加預設流。

![條件拆分](media/data-flow/conditionalsplit1.png "條件拆分選項")

## <a name="data-flow-script"></a>資料流程指令碼

### <a name="syntax"></a>語法

```
<incomingStream>
    split(
        <conditionalExpression1>
        <conditionalExpression2>
        ...
        disjoint: {true | false}
    ) ~> <splitTx>@(stream1, stream2, ..., <defaultStream>)
```

### <a name="example"></a>範例

下面的示例是一個條件拆分轉換，`SplitByYear`該轉換名為，用於`CleanData`傳入流。 此轉換有兩個拆分`year < 1960`條件和`year > 1980`。 `disjoint`為 false，因為資料轉到第一個匹配條件。 匹配第一個條件的每一行都轉到`moviesBefore1960`輸出流。 匹配第二個條件的所有剩餘行都轉到輸出`moviesAFter1980`流。 所有其他行流經預設流`AllOtherMovies`。

在資料工廠 UX 中，此轉換類似于下圖：

![條件拆分](media/data-flow/conditionalsplit1.png "條件拆分選項")

此轉換的資料流程腳本位於下面的程式碼片段中：

```
CleanData
    split(
        year < 1960,
        year > 1980,
        disjoint: false
    ) ~> SplitByYear@(moviesBefore1960, moviesAfter1980, AllOtherMovies)
```

## <a name="next-steps"></a>後續步驟

與條件拆分一起使用的常見資料流程轉換是[聯接轉換](data-flow-join.md)、[查找轉換](data-flow-lookup.md)和[選擇轉換](data-flow-select.md)
