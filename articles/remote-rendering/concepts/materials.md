---
title: 材質
description: 轉譯材質描述和材質屬性
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: conceptual
ms.openlocfilehash: 8551e17ddd71e76aca0c85b9768f564ae0e5f049
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681840"
---
# <a name="materials"></a>材質

材質是定義如何呈現[網格](meshes.md)的[共用資源](../concepts/lifetime.md)。 材質用來指定要套用的[材質](textures.md)、是否讓物件變成透明，以及如何計算光線。

材質會在[模型轉換](../how-tos/conversion/model-conversion.md)期間自動建立，並可在執行時間存取。 您也可以從程式碼建立自訂材料，並取代現有的資料。 如果您想要跨多個網格共用相同的資料，這種情況特別合理。 因為資料的修改會顯示在參考它的每個網格上，所以這個方法可以用來輕鬆地套用變更。

> [!NOTE]
> 某些使用案例（例如反白顯示的物件）可以藉由修改材質來完成，但更容易透過[HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md)來達成。

## <a name="material-types"></a>材質類型

Azure 遠端呈現有兩種不同的材質類型：

* [.Pbr 材質](../overview/features/pbr-materials.md)會用於應該盡可能呈現為實際正確的表面。 實際的光源會使用*實際*的轉譯（.pbr）來計算這些材質。 若要充分利用此材質類型，請務必提供高品質的輸入資料，例如粗糙度和一般地圖。

* [色彩材質](../overview/features/color-materials.md)用於不需要其他光源的情況。 這些資料永遠都是完整的，並且更容易設定。 色彩材質適用于不應該有任何光源的資料，或已經合併靜態光源（例如透過[攝影測量](https://en.wikipedia.org/wiki/Photogrammetry)取得的模型）。

## <a name="mesh-vs-meshcomponent-material-assignment"></a>網格與 MeshComponent 材質指派

[網格](meshes.md)有一或多個 submeshes。 每個 submesh 都會參考一個材質。 您可以將資料變更為直接在網格上使用，也可以覆寫要用於[MeshComponent](meshes.md#meshcomponent)上之 submesh 的材質。

當您直接在網格資源上修改材質時，這項變更會影響該網格的所有實例。 不過，在 MeshComponent 上進行變更時，只會影響一個網格實例。 哪一個方法比較合適，取決於所需的行為，但修改 MeshComponent 是較常見的方法。

## <a name="material-classes"></a>材質類別

API 所提供的所有資料都是衍生自基類`Material`。 您可以透過`Material.MaterialSubType`來查詢其類型，或直接將其轉換：

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

* [PBR 材質](../overview/features/pbr-materials.md)
* [色彩材質](../overview/features/color-materials.md)
