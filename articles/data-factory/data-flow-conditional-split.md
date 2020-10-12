---
title: 對應資料流中的條件式分割轉換
description: 使用 Azure Data Factory 對應資料流中的條件式分割轉換，將資料分割成不同的資料流
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/21/2020
ms.openlocfilehash: eece6f97e82f3800d4f59ac1849b34c2a1e4635b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "83800076"
---
# <a name="conditional-split-transformation-in-mapping-data-flow"></a>對應資料流中的條件式分割轉換

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

條件式分割轉換會根據比對條件，將資料列路由至不同的資料流。 條件式分割轉換類似於程式設計語言中的 CASE 決策結構。 轉換會評估運算式，並根據結果將資料列導向特定資料流。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4wKCX]

## <a name="configuration"></a>組態

**分割**設定會決定資料列是否流向第一個相符的資料流，還是要流向其符合的每個資料流。

可使用資料流運算式建立器來輸入分割條件的運算式。 若要加入新的條件，請按一下現有資料列的加號圖示。 您也可以針對不符合任何條件的資料列新增預設資料流。

![條件式分割](media/data-flow/conditionalsplit1.png "條件式分割輸入選項")

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

下列範例是一個名為 `SplitByYear` 的條件式分割轉換，會接受傳入的資料流 `CleanData`。 這項轉換有兩個分割條件：`year < 1960` 和 `year > 1980`。 `disjoint` 為 false，因為資料會流往最初相符的條件。 每個符合第一個條件的資料列都會流向輸出資料流 `moviesBefore1960`。 所有符合第二個條件的剩餘資料列都會流向輸出資料流 `moviesAFter1980`。 所有其他資料列會流經預設資料流 `AllOtherMovies`。

在 Data Factory UX 中，這項轉換看起來如下圖所示：

![條件式分割](media/data-flow/conditionalsplit1.png "條件式分割輸入選項")

此轉換的資料流指令碼位於下列程式碼片段中：

```
CleanData
    split(
        year < 1960,
        year > 1980,
        disjoint: false
    ) ~> SplitByYear@(moviesBefore1960, moviesAfter1980, AllOtherMovies)
```

## <a name="next-steps"></a>後續步驟

經常搭配條件式分割使用的資料流轉換包括[聯結轉換](data-flow-join.md)、[查閱轉換](data-flow-lookup.md)和[選取轉換](data-flow-select.md)
