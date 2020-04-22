---
title: 對應資料串流的軸轉換
description: 使用 Azure 資料工廠映射資料串流資料分析轉換,從行到列透視資料
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: a58444f81f60b48f9c2c76f13257a6a2431158a8
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686459"
---
# <a name="pivot-transformation-in-mapping-data-flow"></a>對應資料串流的軸轉換


[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

使用數據透視變換從單個列的唯一行值創建多個列。 透視是一個聚合變換,用於按列分組並使用[聚合函數](data-flow-expression-functions.md#aggregate-functions)生成數據透視列。

## <a name="configuration"></a>設定

透視轉換需要三個不同的輸入:按列分組、透視鍵以及如何生成透視列

### <a name="group-by"></a>群組依據

![依據選項分組](media/data-flow/pivot2.png "*按選項群組")

選擇要聚合透視列的列。 輸出數據將按值按值將具有相同組的所有行分組到一行中。 在透視列中完成的聚合將發生在每個組中。

此為選擇性區段。 如果未按列選擇組,則將聚合整個數據流,並且將只輸出一行。

### <a name="pivot-key"></a>樞軸鍵

![樞軸鍵](media/data-flow/pivot3.png "樞軸鍵")

樞軸鍵是行值被透視到新列的列。 默認情況下,透視變換將為每個唯一行值創建新列。

在標記為 **「值」** 的部分中,可以輸入要透視的特定行值。 只有在此節中輸入的行值才會旋轉。 開啟**Null 值**將為列中的空值建立一個透視列。

### <a name="pivoted-columns"></a>旋轉欄位

![旋轉欄位](media/data-flow/pivot4.png "旋轉欄位")

對於每個成為列的唯一透視鍵值,為每個組生成聚合行值。 您可以建立每個資料透視鍵的多個欄。 每個資料透視列必須至少包含一個[彙總函數](data-flow-expression-functions.md#aggregate-functions)。

**欄位名稱模式:** 選擇如何設置每個數據透視列的欄名稱的格式。 輸出的列名稱將是透視鍵值、列首碼和可選首碼(足夠的中間字元)的組合。 

**欄位:** 如果每個數據透視鍵生成多個透視列,請選擇您希望對列進行排序的方式。 

**列前置字串:** 如果每個數據透視鍵生成多個數據透視列,請為每個列輸入列首碼。 如果只有一個透視列,則此設置是可選的。

## <a name="help-graphic"></a>說明圖

下圖顯示了不同資料透視元件如何相互互動

![樞紐分析圖形](media/data-flow/pivot5.png "透視說明圖形")

## <a name="pivot-metadata"></a>樞紐分析中繼資料

如果在樞軸鍵配置中未指定值,則在運行時將動態生成透視列。 透視列數將等於唯一透視鍵值數乘以透視列數。 由於這可能是一個不斷變化的數位,UX將不會在 **「檢查」** 選項卡中顯示列元數據,也不會顯示列傳播。 要轉換這些列,請使用映射資料串流的[列模式](concepts-data-flow-column-pattern.md)功能。 

如果設置了特定的透視鍵值,則透視列將顯示在元數據中。 列名稱將在「檢查和接收器」映射中可供您使用。

### <a name="generate-metadata-from-drifted-columns"></a>從漂移列產生中繼資料

數據透視基於行值動態生成新的列名稱。 您可以將這些新列添加到元資料中,這些元數據可在資料流中稍後引用。 為此,請使用數據預覽中的[地圖漂移](concepts-data-flow-schema-drift.md#map-drifted-columns-quick-action)快速操作。 

![樞紐資料行](media/data-flow/newpivot1.png "映射漂移樞軸列")

### <a name="sinking-pivoted-columns"></a>下沉旋轉列

儘管透視列是動態的,但它們仍然可以寫入目標數據存儲。 開啟**允許在接收器設定中漂移的架構**。 這將允許您編寫元資料中未包括的欄。 列元數據,但架構漂移選項將允許您登陸數據。

### <a name="rejoin-original-fields"></a>重新加入原始欄位

透視轉換將僅按和透視列投影組。 如果希望輸出數據包含其他輸入列,請使用[自聯接](data-flow-join.md#self-join)模式。

## <a name="data-flow-script"></a>資料流程指令碼

### <a name="syntax"></a>語法

```
<incomingStreamName>
    pivot(groupBy(Tm),
        pivotBy(<pivotKeyColumn, [<specifiedColumnName1>,...,<specifiedColumnNameN>]),
        <pivotColumnPrefix> = <pivotedColumnValue>,
        columnNaming: '< prefix >< $N | $V ><middle >< $N | $V >< suffix >',
        lateral: { 'true' | 'false'}
    ) ~> <pivotTransformationName
```
### <a name="example"></a>範例

設定部分中顯示的螢幕具有以下資料串流文稿:

```
BasketballPlayerStats pivot(groupBy(Tm),
    pivotBy(Pos),
    {} = count(),
    columnNaming: '$V$N count',
    lateral: true) ~> PivotExample

```

## <a name="next-steps"></a>後續步驟

嘗試[取消透視變換](data-flow-unpivot.md)將列值轉換為行值。 
