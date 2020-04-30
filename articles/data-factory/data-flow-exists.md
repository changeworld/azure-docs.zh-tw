---
title: 對應資料流程中的 Exists 轉換
description: 使用 Azure Data Factory 對應資料流程中的 exists 轉換來檢查現有的資料列
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
ms.openlocfilehash: 9c43b141608e5a9051499fdfb2adb5d8b0b593df
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82232460"
---
# <a name="exists-transformation-in-mapping-data-flow"></a>對應資料流程中的 Exists 轉換

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Exists 轉換是一個資料列篩選轉換，它會檢查您的資料是否存在於另一個來源或資料流程中。 輸出資料流程包含左側資料流程中的所有資料列，其存在或不存在於正確的資料流程中。 Exists 轉換類似于```SQL WHERE EXISTS```和。 ```SQL WHERE NOT EXISTS```

## <a name="configuration"></a>設定

1. 在 [**正確資料流程**] 下拉式清單中，選擇您要檢查哪一個資料流程是否存在。
1. 指定您要尋找的資料是否存在，或不存在於 [**存在類型**] 設定中。
1. 選取您是否想要**自訂表格達式**。
1. 選擇您想要比較哪一個索引鍵資料行做為現有的條件。 根據預設，資料流程會在每個資料流程中的一個資料行之間尋找是否相等。 若要透過計算值進行比較，請將滑鼠停留在資料行下拉式清單中，然後選取 [**計算資料行**]

![存在設定](media/data-flow/exists.png "存在1")

### <a name="multiple-exists-conditions"></a>多個存在條件

若要比較每個資料流程中的多個資料行，請按一下現有資料列旁的加號圖示，以加入新的 exists 條件。 每個額外的條件都是由 "and" 語句聯結。 比較兩個數據行與下列運算式相同：

`source1@column1 == source2@column1 && source1@column2 == source2@column2`

### <a name="custom-expression"></a>自訂表格達式

若要建立包含「and」和「equals to」以外之運算子的自由形式運算式，請選取 [**自訂表格達式**] 欄位。 在 [資料流程運算式產生器] 中按一下藍色方塊，以輸入自訂表格達式。

![已存在自訂設定](media/data-flow/exists1.png "存在自訂")

## <a name="broadcast-optimization"></a>廣播優化

![廣播聯結](media/data-flow/broadcast.png "廣播聯結")

在聯結、查閱和存在轉換中，如果其中一個或兩個數據流符合背景工作節點記憶體，您可以藉由啟用**廣播**來優化效能。 根據預設，spark 引擎會自動決定是否要廣播一邊。 若要手動選擇要廣播的端，請選取 [**固定**]。

除非您的聯結遇到逾時錯誤，否則不建議使用 [**關閉**] 選項來停用廣播。

## <a name="data-flow-script"></a>資料流程指令碼

### <a name="syntax"></a>語法

```
<leftStream>, <rightStream>
    exists(
        <conditionalExpression>,
        negate: { true | false },
        broadcast: { 'auto' | 'left' | 'right' | 'both' | 'off' }
    ) ~> <existsTransformationName>
```

### <a name="example"></a>範例

下列範例是名為`checkForChanges`的 exists 轉換，它會採用`NameNorm2`左邊的資料流程`TypeConversions`和正確的資料流程。  Exists 條件是當每個`NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region`資料流程中的`EMPID`和`Region`資料行都相符時，傳回 true 的運算式。 當我們要檢查是否存在時`negate` ，是 false。 我們不會在 [優化] 索引標籤`broadcast`中啟用`'none'`任何廣播，因此具有值。

在 Data Factory UX 中，這項轉換看起來如下圖所示：

![Exists 範例](media/data-flow/exists-script.png "Exists 範例")

此轉換的資料流程腳本位於下列程式碼片段中：

```
NameNorm2, TypeConversions
    exists(
        NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region,
        negate:false,
        broadcast: 'auto'
    ) ~> checkForChanges
```

## <a name="next-steps"></a>後續步驟

類似的轉換是[查閱](data-flow-lookup.md)和[聯結](data-flow-join.md)。
