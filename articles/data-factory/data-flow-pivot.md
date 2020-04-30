---
title: 對應資料流程中的資料透視轉換
description: 使用 Azure Data Factory 對應資料流程樞紐分析表轉換，從資料列到資料行的樞紐分析表
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: a58444f81f60b48f9c2c76f13257a6a2431158a8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81686459"
---
# <a name="pivot-transformation-in-mapping-data-flow"></a>對應資料流程中的資料透視轉換


[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

使用 [資料透視] 轉換，從單一資料行的唯一資料列值建立多個資料行。 Pivot 是一個匯總轉換，您可以在其中選取 [群組依據資料行]，然後使用[彙總函式](data-flow-expression-functions.md#aggregate-functions)來產生樞紐分析表。

## <a name="configuration"></a>設定

「資料透視」轉換需要三個不同的輸入： [群組依據] 資料行、[pivot 索引鍵]，以及如何產生已切換的資料行

### <a name="group-by"></a>群組依據

![依據選項分組](media/data-flow/pivot2.png "[依選項分組")

選取要對其進行已切換資料行匯總的資料行。 輸出資料會將具有相同 group by 值的所有資料列分組成一個資料列。 在 [切換] 資料行中完成的匯總將會出現在每個群組上。

此為選擇性區段。 如果未選取 [群組依據] 資料行，則會匯總整個資料流程，而且只會輸出一個資料列。

### <a name="pivot-key"></a>Pivot 索引鍵

![Pivot 索引鍵](media/data-flow/pivot3.png "Pivot 索引鍵")

Pivot 索引鍵是資料列值會切換到新資料行的資料行。 根據預設，pivot 轉換會針對每個唯一的資料列值建立新的資料行。

在標示為 [**值**] 的區段中，您可以輸入要進行切換的特定資料列值。 只有在此區段中輸入的資料列值才會進行透視。 啟用**null 值**將會針對資料行中的 Null 值建立已切換的資料行。

### <a name="pivoted-columns"></a>透視資料行

![透視資料行](media/data-flow/pivot4.png "透視資料行")

針對每個成為資料行的唯一 pivot 索引鍵值，為每個群組產生匯總的資料列值。 您可以為每個 pivot 索引鍵建立多個資料行。 每個樞紐分析表都必須包含至少一個[彙總函式](data-flow-expression-functions.md#aggregate-functions)。

資料**行名稱模式：** 選取如何設定每個樞紐分析表資料行名稱的格式。 輸出的資料行名稱將會是 pivot 索引鍵值、資料行前置詞和選擇性前置詞、足夠的中間字元的組合。 

資料**行相片順序：** 如果您針對每個資料透視機碼產生一個以上的樞紐分析表，請選擇您想要如何排序資料行。 

資料**行前置詞：** 如果您針對每個 pivot 索引鍵產生一個以上的樞紐分析表，請輸入每個資料行的資料行前置詞。 如果您只有一個已切換的資料行，這項設定是選擇性的。

## <a name="help-graphic"></a>說明圖形

下圖說明不同的資料透視元件彼此互動的方式

![Pivot 說明圖形](media/data-flow/pivot5.png "Pivot 說明圖形")

## <a name="pivot-metadata"></a>Pivot 中繼資料

如果在 pivot 索引鍵設定中未指定任何值，則會在執行時間動態產生已切換的資料行。 已切換資料行的數目會等於唯一的樞紐分析表索引鍵值數乘以 pivot 資料行的數目。 因為這可能是變更的數位，UX 不會在 [**檢查**] 索引標籤中顯示資料行中繼資料，也不會有任何資料行傳播。 若要轉換這些資料行，請使用對應資料流程的資料[行模式](concepts-data-flow-column-pattern.md)功能。 

如果設定了特定的資料透視索引鍵值，則會在中繼資料中顯示已切換的資料行。 資料行名稱將可供您在檢查和接收對應中使用。

### <a name="generate-metadata-from-drifted-columns"></a>從漂移資料行產生中繼資料

Pivot 會根據資料列值動態產生新的資料行名稱。 您可以將這些新的資料行加入中繼資料中，稍後可在資料流程中參考。 若要這麼做，請在資料預覽中使用[map 漂移](concepts-data-flow-schema-drift.md#map-drifted-columns-quick-action)快速動作。 

![樞紐資料行](media/data-flow/newpivot1.png "Map 漂移 Pivot 資料行")

### <a name="sinking-pivoted-columns"></a>接收已旋轉的資料行

雖然切換資料行是動態的，但仍可寫入目的地資料存放區。 啟用接收設定中的 [**允許架構漂移**]。 這可讓您撰寫未包含在中繼資料中的資料行。 您的資料行中繼資料，但是 [架構漂移] 選項可讓您將資料放在其中。

### <a name="rejoin-original-fields"></a>重新加入原始欄位

「資料透視」轉換只會投影「分組依據」和「已切換」資料行。 如果您想要讓輸出資料包含其他輸入資料行，請使用[自我聯結](data-flow-join.md#self-join)模式。

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

[設定] 區段中所顯示的畫面具有下列資料流程腳本：

```
BasketballPlayerStats pivot(groupBy(Tm),
    pivotBy(Pos),
    {} = count(),
    columnNaming: '$V$N count',
    lateral: true) ~> PivotExample

```

## <a name="next-steps"></a>後續步驟

嘗試[unpivot 轉換](data-flow-unpivot.md)，將資料行值轉換成資料列值。 
