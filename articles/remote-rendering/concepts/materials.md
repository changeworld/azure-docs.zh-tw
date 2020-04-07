---
title: 物料
description: 成像材質描述和材質屬性
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: conceptual
ms.openlocfilehash: 8551e17ddd71e76aca0c85b9768f564ae0e5f049
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681840"
---
# <a name="materials"></a>物料

材質是定義如何呈現[等同](meshes.md)的[分享資源](../concepts/lifetime.md)。 材質用於指定要應用的[紋理](textures.md)、是否使物件透明以及如何計算照明。

材料在[模型轉換](../how-tos/conversion/model-conversion.md)期間自動創建,並在運行時訪問。 您還可以從代碼創建自定義材料並替換現有材料。 如果要跨多個網點共用相同的材料,此方案尤其有意義。 由於材質的修改在引用材質的每個網格上都可見,因此此方法可用於輕鬆應用更改。

> [!NOTE]
> 某些用例(如突出顯示選取的物件)可以通過修改材質來完成,但通過[分層狀態覆蓋元件](../overview/features/override-hierarchical-state.md)更容易實現。

## <a name="material-types"></a>材料類型

Azure 遠端呈現有兩種不同的材質類型:

* [PBR 材質](../overview/features/pbr-materials.md)用於應盡可能呈現為物理正確曲面。 使用*基於物理的渲染*(PBR) 計算這些材料的真實照明。 為了充分利用這種材料類型,提供高品質的輸入數據(如粗糙度和法線貼圖)非常重要。

* [顏色材料](../overview/features/color-materials.md)用於不需要額外照明的情況。 這些材料總是充滿明亮,更容易設置。 顏色材料用於本應沒有照明或已包含靜態照明的數據,例如通過[攝影測量](https://en.wikipedia.org/wiki/Photogrammetry)獲得的模型。

## <a name="mesh-vs-meshcomponent-material-assignment"></a>網格與網格元件材料分配

[梅斯](meshes.md)有一個或多個子百年代。 每個子網格引用一個材質。 您可以更改材質以直接在網格上使用,也可以重寫要用於[網格元件](meshes.md#meshcomponent)上的子網格的材料。

直接在網格資源上修改材質時,此更改會影響該網格的所有實例。 但是,在網格元件上更改它僅影響該網格實例。 哪種方法更合適取決於所需的行為,但修改 Mesh 元件是更常見的方法。

## <a name="material-classes"></a>材料類

API 提供的所有材料都派生自基`Material`類 。 可透過`Material.MaterialSubType`或直接強制轉換它們來查詢其類型:

``` cs
void SetMaterialColorToGreen(Material material)
{
    if (material.MaterialSubType == MaterialType.Color)
    {
        ColorMaterial colorMaterial = material as ColorMaterial;
        colorMaterial.AlbedoColor = new Color4(0, 1, 0, 1);
        return;
    }

    PbrMaterial pbrMat = material as PbrMaterial;
    if( pbrMat!= null )
    {
        PbrMaterial pbrMaterial = material.PbrMaterial.Value;
        pbrMaterial.AlbedoColor = new Color4(0, 1, 0, 1);
        return;
    }
}
```

## <a name="next-steps"></a>後續步驟

* [PBR 材料](../overview/features/pbr-materials.md)
* [色彩材質](../overview/features/color-materials.md)
