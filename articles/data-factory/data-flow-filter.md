---
title: 映射資料流程的篩選器轉換
description: 使用 Azure 資料工廠映射資料流程的篩選器轉換篩選出行
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
ms.openlocfilehash: 84b935d1646ea4f13d5926fc68797fd8efd03dea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930362"
---
# <a name="filter-transformation-in-mapping-data-flow"></a>映射資料流程的篩選器轉換

篩選器轉換允許基於條件進行行篩選。 輸出流包括與篩選準則匹配的所有行。 篩選器轉換類似于 SQL 中的 WHERE 子句。

## <a name="configuration"></a>組態

使用資料流程運算式產生器為篩選器條件輸入運算式。 要打開運算式產生器，請按一下藍色框。 篩檢程式條件必須為布林類型。 有關如何創建運算式的詳細資訊，請參閱[運算式產生器](concepts-data-flow-expression-builder.md)文檔。

![篩檢程式轉換](media/data-flow/filter1.png "篩檢程式轉換")

## <a name="data-flow-script"></a>資料流程指令碼

### <a name="syntax"></a>語法

```
<incomingStream>
    filter(
        <conditionalExpression>
    ) ~> <filterTransformationName>
```

### <a name="example"></a>範例

下面的示例是一個篩選器轉換，`FilterBefore1960`該轉換名為，用於`CleanData`傳入流。 篩選器條件是運算式`year <= 1960`。

在資料工廠 UX 中，此轉換類似于下圖：

![篩檢程式轉換](media/data-flow/filter1.png "篩檢程式轉換")

此轉換的資料流程腳本位於下面的程式碼片段中：

```
CleanData
    filter(
        year <= 1960
    ) ~> FilterBefore1960

```

## <a name="next-steps"></a>後續步驟

使用[選擇變換](data-flow-select.md)篩選出列
