---
title: 映射資料流程樞軸轉換
description: 使用 Azure 資料工廠映射資料流程資料透視轉換，從行到列透視資料
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: 8f23b5e61e1aee83172a12466fac8d5b5003fea8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930294"
---
# <a name="azure-data-factory-pivot-transformation"></a>Azure 資料工廠資料透視轉換


使用 ADF 資料流程中的樞紐做為彙總，其中一個或多個群組資料行都有個別的資料行值會轉換成相異資料列。 基本上，您可以將資料列值樞紐處理為新的資料行 (將資料轉換成中繼資料)。

![透視選項](media/data-flow/pivot1.png "樞軸 1")

## <a name="group-by"></a>群組依據

![透視選項](media/data-flow/pivot2.png "樞軸 2")

首先，設定您想要對於樞紐彙總分組所依據的資料行。 您可以在這裡使用資料行清單旁邊的 + 號設定 1 個以上資料行。

## <a name="pivot-key"></a>樞軸鍵

![透視選項](media/data-flow/pivot3.png "樞軸 3")

樞紐索引鍵是 ADF 從資料列樞紐處理為資料行的資料行。 根據預設，此欄位的資料集之內的每個唯一值都會樞紐處理為資料行。 不過，您可以選擇從資料集輸入您想要樞紐處理為資料行值的數值。 這是確定將創建的新列的列。

## <a name="pivoted-columns"></a>旋轉列

![透視選項](media/data-flow/pivot4.png "樞軸 4")

最後，選擇您想要對已樞紐的值所使用的彙總，以及您要如何在轉換的新輸出投影中顯示資料行。

(選擇性) 您可以設定對於每個新資料行名稱從資料列值新增前置詞、中置詞及後置詞的命名模式。

例如，將"銷售"旋轉為"區域"將導致每個銷售值產生新的列值，即"25"、"50"、"1000"等。但是，如果設置首碼值"Sales-"，則每個列值將添加"Sales-"到該值的開頭。

![透視選項](media/data-flow/pivot5.png "樞軸 5")

設定為「一般」資料行的排列方式，會使用彙總值將已樞紐的資料行分組在一起。 將資料行排列方式設定為「橫向」會在資料行和值之間交替。

### <a name="aggregation"></a>彙總

若要對於樞紐值設定您想要使用的彙總，請按一下「已樞紐的資料行」窗格底部的欄位。 您會進入 ADF 資料流程運算式產生器，您可以在其中建立彙總運算式，並對於新的彙總值提供描述性的別名名稱。

使用 ADF 資料流程運算式語言在運算式產生器描述已樞紐的資料行轉換：https://aka.ms/dataflowexpressions。

## <a name="pivot-metadata"></a>資料透視中繼資料

資料透視轉換將生成基於傳入資料的動態的新列名稱。 資料透視鍵生成每個新列名稱的值。 如果不指定單個值，並希望為數據透視鍵中的每個唯一值創建動態列名稱，則 UI 將不會在"檢查"中顯示中繼資料，並且不會向 Sink 轉換傳播列。 如果為數據透視鍵設置值，則 ADF 可以確定新的列名稱，這些列名稱將在"檢查和接收器"映射中可供您使用。

### <a name="generate-a-new-model-from-dynamic-columns"></a>從動態列生成新模型

資料透視基於行值動態生成新的列名稱。 您可以將這些新列轉換為中繼資料，可在資料流程中稍後引用。 為此，請按一下"資料預覽"選項卡。透視轉換生成的所有新列都顯示在表標題中，並帶有"漂移"圖示。 按一下"地圖漂移"按鈕，將這些新列轉換為中繼資料，使其成為資料流程模型的一部分。

![樞紐資料行](media/data-flow/newpivot1.png "映射漂移樞軸列")

### <a name="landing-new-columns-in-sink"></a>在沉陷中登陸新列

即使使用資料透視中的動態列名稱，您仍可以將新的列名稱和值沉入目標存儲中。 只需在"接收器"設置中設置"允許架構漂移"。 您將不會在列中繼資料中看到新的動態名稱，但架構漂移選項將允許您登陸資料。

### <a name="view-metadata-in-design-mode"></a>在設計模式下查看中繼資料

如果要在"檢查"中將新列名稱視為中繼資料，並且希望看到列顯式傳播到"接收器"轉換，然後在"透視鍵"選項卡中設置顯式值。

### <a name="how-to-rejoin-original-fields"></a>如何重新加入原始欄位
樞紐轉換只會投出使用在彙總、分組和中樞動作中的資料行。 如果要在流中包括上一步中的其他列，請使用上一步中的新分支，並使用自聯接模式將流與原始元資料連線。

## <a name="next-steps"></a>後續步驟

嘗試[取消透視變換](data-flow-unpivot.md)將列值轉換為行值。 
