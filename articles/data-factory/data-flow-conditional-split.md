---
title: 對應資料串流的條件分割轉換
description: 使用 Azure 資料工廠映射資料串流中的條件分割轉換將資料分割到不同的流中
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
ms.openlocfilehash: 20def8ca51f21d914e7090999e8284244c5f3ec7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416500"
---
# <a name="conditional-split-transformation-in-mapping-data-flow"></a>對應資料串流的條件分割轉換

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

條件拆分轉換根據匹配條件將數據行路由到不同的流。 條件拆分轉換類似於程式設計語言中的 CASE 決策結構。 轉換會評估運算式，並根據結果將資料列導向特定資料流。

## <a name="configuration"></a>組態

「**分割」** 設定確定資料行是流向第一個符合流還是與其符合的每個流。

使用數據流表達式生成器為分割條件輸入運算式。 要添加新條件,請單擊現有行中的加號圖示。 對於與任何條件不匹配的行,也可以添加預設流。

![條件分割](media/data-flow/conditionalsplit1.png "條件分割選項")

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

下面的範例是一個條件拆分轉換,`SplitByYear`該轉換名為,`CleanData`用於傳入流。 此轉換有兩個分割`year < 1960`條件與`year > 1980`。 `disjoint`為 false,因為數據轉到第一個匹配條件。 匹配第一個條件的每一行都轉到`moviesBefore1960`輸出流。 匹配第二個條件的所有剩餘行都轉到輸出`moviesAFter1980`流。 所有其他行流經預設流`AllOtherMovies`。

在資料工廠的一個數字的數字, 此轉換類似於下圖:

![條件分割](media/data-flow/conditionalsplit1.png "條件分割選項")

此轉換的資料串流文稿位於下面的代碼段中:

```
CleanData
    split(
        year < 1960,
        year > 1980,
        disjoint: false
    ) ~> SplitByYear@(moviesBefore1960, moviesAfter1980, AllOtherMovies)
```

## <a name="next-steps"></a>後續步驟

與條件分離的資料串流轉換,[並轉換](data-flow-join.md), 並轉換,[並](data-flow-lookup.md)[選擇轉換](data-flow-select.md)
