---
title: 映射資料流程的篩選器轉換
description: 使用 Azure 資料工廠映射資料串流的篩選器轉換篩選出行
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
ms.openlocfilehash: 3460c789909c6acd0537ef89a9ec8509bf6e135d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413738"
---
# <a name="filter-transformation-in-mapping-data-flow"></a>映射資料流程的篩選器轉換

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

篩選器轉換允許基於條件進行行篩選。 輸出流包括與篩選條件匹配的所有行。 篩選器轉換類似於 SQL 中的 WHERE 子句。

## <a name="configuration"></a>組態

使用數據流運算式生成器為篩選器條件輸入運算式。 要打開表達式生成器,請單擊藍色框。 過濾器條件必須為布爾類型。 有關如何創建表達式的詳細資訊,請參閱[表達式生成器](concepts-data-flow-expression-builder.md)文件。

![過濾器轉換](media/data-flow/filter1.png "過濾器轉換")

## <a name="data-flow-script"></a>資料流程指令碼

### <a name="syntax"></a>語法

```
<incomingStream>
    filter(
        <conditionalExpression>
    ) ~> <filterTransformationName>
```

### <a name="example"></a>範例

下面的範例是一個篩選器轉換,`FilterBefore1960`該轉換名為,`CleanData`用於傳入流。 篩選器條件是表示式`year <= 1960`。

在資料工廠的一個數字的數字, 此轉換類似於下圖:

![過濾器轉換](media/data-flow/filter1.png "過濾器轉換")

此轉換的資料串流文稿位於下面的代碼段中:

```
CleanData
    filter(
        year <= 1960
    ) ~> FilterBefore1960

```

## <a name="next-steps"></a>後續步驟

使用[選擇轉換](data-flow-select.md)篩選出欄位
