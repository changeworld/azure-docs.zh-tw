---
title: 對應資料流中的樞紐轉換
description: 使用 Azure Data Factory 對應資料流樞紐轉換，將資料從資料列樞紐處理為資料行
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/17/2020
ms.openlocfilehash: e098182c000cbe05df533434a41c55b797ef876f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87086654"
---
# <a name="pivot-transformation-in-mapping-data-flow"></a>對應資料流中的樞紐轉換


[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

使用樞紐轉換，從單一資料行的唯一資料列值建立多個資料行。 樞紐是匯總轉換，您可以在其中選取群組依據資料行，並使用[彙總函式](data-flow-expression-functions.md#aggregate-functions)產生樞紐分析表。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4C4YN]

## <a name="configuration"></a>組態

樞紐轉換需要三個不同的輸入：群組依據資料行、樞紐索引鍵，以及如何產生已樞紐處理的資料行

### <a name="group-by"></a>群組依據

![依據選項分組](media/data-flow/pivot2.png "依據選項分組")

選取要將已樞紐處理的資料行匯總的資料行。 輸出資料會將具有相同群組依據值的所有資料列分組為一個資料列。 在經過樞紐處理的資料行中完成的匯總將出現在每個群組上。

此為選擇性區段。 如果未選取群組依據資料行，則會匯總整個資料流，而且只會輸出一個資料列。

### <a name="pivot-key"></a>樞紐索引鍵

![樞紐索引鍵](media/data-flow/pivot3.png "樞紐索引鍵")

樞紐索引鍵是資料列值樞紐處理為新資料行的資料行。 根據預設，樞紐轉換會針對每個唯一的資料列值建立新的資料行。

在標示為 [值] 的區段中，您可以輸入要樞紐處理的特定資料列值。 只有在此區段中輸入的資料列值才會予以樞紐處理。 啟用 **Null 值**會針對資料行中的 null 值建立樞紐處理的資料行。

### <a name="pivoted-columns"></a>已樞紐處理的資料行

![已樞紐處理的資料行](media/data-flow/pivot4.png "已樞紐處理的資料行")

針對每個成為資料行的唯一樞紐索引鍵值，為每個群組產生匯總的資料列值。 您可以為每個樞紐索引鍵建立多個資料行。 每個樞紐資料行都必須包含至少一個[彙總函式](data-flow-expression-functions.md#aggregate-functions)。

**資料行名稱模式：** 選取如何設定每個樞紐分析表資料行名稱的格式。 輸出的資料行名稱將會是樞紐索引鍵值、資料行前置詞和選擇性前置詞、足夠的中間字元組合。 

**資料行排列：** 如果您針對每個樞紐索引鍵產生一個以上的樞紐分析表，請選擇您想要如何排序資料行。 

**資料行前置詞：** 如果您針對每個樞紐索引鍵產生一個以上的樞紐分析表，請輸入每個資料行的資料行前置詞。 如果您只有一個已樞紐處理的資料行，這項設定是選擇性的。

## <a name="help-graphic"></a>說明圖形

下圖說明不同的樞紐元件彼此互動的方式

![樞紐說明圖形](media/data-flow/pivot5.png "樞紐說明圖形")

## <a name="pivot-metadata"></a>樞紐中繼資料

如果在樞紐索引鍵設定中未指定任何值，則會在執行階段動態產生經過樞紐處理的資料行。 已樞紐處理的資料行的數目會等於唯一的樞紐分析表索引鍵值數乘以樞紐資料行的數目。 由於這是可能變更的數位，UX 不會在 [檢查] 索引標籤中顯示資料行中繼資料，而且不會有任何資料行散佈。 若要轉換這些資料行，請使用對應資料流的[資料行模式](concepts-data-flow-column-pattern.md)功能。 

如果已設定特定的樞紐索引鍵值，則會在中繼資料中顯示已切換的資料行。 資料行名稱將可供您在「檢查」和「接收」對應中使用。

### <a name="generate-metadata-from-drifted-columns"></a>從漂移資料行產生中繼資料

樞紐會根據資料列值動態產生新的資料行名稱。 您可以將這些新的資料行加入中繼資料中，以便往後可在資料流程中參考。 若要這麼做，請使用[對應漂移](concepts-data-flow-schema-drift.md#map-drifted-columns-quick-action)資料預覽中的快速動作。 

![樞紐資料行](media/data-flow/newpivot1.png "對應漂移樞紐資料行")

### <a name="sinking-pivoted-columns"></a>接收經過樞紐處理的資料行

雖然已樞紐處理的資料行為動態，但仍可寫入目的地資料存放區。 在接收設定中啟用 [允許結構描述漂移]。 這可讓您撰寫未包含在中繼資料中的資料行。 您不會在資料行中繼資料中看到新的動態名稱，但是 [結構描述漂移] 選項可讓您放入資料。

### <a name="rejoin-original-fields"></a>重新加入原始欄位

樞紐轉換只會投影群組依據和已樞紐處理的資料行。 如果您想要讓輸出資料包含其他輸入資料行，請使用[自我聯結](data-flow-join.md#self-join)模式。

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

[設定] 區段中所顯示的畫面具有下列資料流程指令碼：

```
BasketballPlayerStats pivot(groupBy(Tm),
    pivotBy(Pos),
    {} = count(),
    columnNaming: '$V$N count',
    lateral: true) ~> PivotExample

```

## <a name="next-steps"></a>後續步驟

嘗試[取消樞紐轉換](data-flow-unpivot.md)，將資料行值轉換為資料列值。 
