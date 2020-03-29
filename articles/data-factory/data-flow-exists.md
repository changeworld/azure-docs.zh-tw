---
title: 映射資料流程中存在轉換
description: 使用 Azure 資料工廠映射資料流程中存在轉換檢查現有行
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
ms.openlocfilehash: efcc45dcf3565b70305323701810c49c4a720394
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930417"
---
# <a name="exists-transformation-in-mapping-data-flow"></a>映射資料流程中存在轉換

存在轉換是一個行篩選轉換，用於檢查資料是否存在於其他源或流中。 輸出流包括左流中存在於右流中或不存在的所有行。 存在的轉換類似于```SQL WHERE EXISTS```和```SQL WHERE NOT EXISTS```。

## <a name="configuration"></a>組態

1. 在**右流**下拉下下下下下選擇要檢查的是否存在的資料流程。
1. 指定要查找是否存在"**存在"類型**設置中是否存在的資料。
1. 選擇是否要自訂**運算式**。
1. 選擇要比較為存在條件的關鍵列。 預設情況下，資料流程在每個流中查找一列之間的相等性。 要通過計算值進行比較，請將滑鼠懸停在列下拉清單上，然後選擇 **"計算"列**。

![存在設置](media/data-flow/exists.png "存在 1")

### <a name="multiple-exists-conditions"></a>多個存在條件

要比較每個流中的多個列，請按一下現有行旁邊的加號圖示來添加新存在條件。 每個附加條件都由"和"語句聯接。 比較兩列與以下運算式相同：

`source1@column1 == source2@column1 && source1@column2 == source2@column2`

### <a name="custom-expression"></a>自訂表格達式

要創建包含"和"和"等於"以外的運算子的自由格式運算式，請選擇 **"自訂表格達式"** 欄位。 通過資料流程運算式產生器通過按一下藍色框輸入自訂表格達式。

![存在自訂設置](media/data-flow/exists1.png "存在自訂")

## <a name="data-flow-script"></a>資料流程指令碼

### <a name="syntax"></a>語法

```
<leftStream>, <rightStream>
    exists(
        <conditionalExpression>,
        negate: { true | false },
        broadcast: {'none' | 'left' | 'right' | 'both'}
    ) ~> <existsTransformationName>
```

### <a name="example"></a>範例

下面的示例是一個稱為"`checkForChanges`左流"和"`NameNorm2`右流`TypeConversions`"的存在的轉換。  如果存在條件，則在每個流`NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region`中`EMPID`和`Region`列都匹配時返回 true 的運算式。 當我們檢查存在時，`negate`是虛假的。 我們不啟用任何廣播在優化選項卡，所以`broadcast`有價值。 `'none'`

在資料工廠 UX 中，此轉換類似于下圖：

![存在示例](media/data-flow/exists-script.png "存在示例")

此轉換的資料流程腳本位於下面的程式碼片段中：

```
NameNorm2, TypeConversions
    exists(
        NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region,
        negate:false,
        broadcast: 'none'
    ) ~> checkForChanges
```

## <a name="next-steps"></a>後續步驟

類似的轉換是[查找](data-flow-lookup.md)和[聯接](data-flow-join.md)。
