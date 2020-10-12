---
title: 材質
description: 轉譯材質描述和材質屬性
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: f8f3a0d0ec79624fb709bb80b8392e2ad8d6f7ce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89613873"
---
# <a name="materials"></a>材質

材質是[共用資源](../concepts/lifetime.md)，定義如何轉譯[網格](meshes.md)。 材質是用來指定要套用哪一個[紋理](textures.md)、是否要讓物件透明化，以及如何計算光線。

材質會在[模型轉換](../how-tos/conversion/model-conversion.md)期間自動建立，而且可在執行階段存取。 您也可以從程式碼建立自訂材質，並取代現有的材質。 如果您想要跨多個網格共用相同的材質，這種案例特別合理。 因為材質的修改會顯示在參考材質的每個網格上，所以這個方法可以用來輕鬆地套用變更。

> [!NOTE]
> 某些使用案例 (例如醒目提示挑選的物件) 可以藉由修改材質來完成，但是透過 [HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md) 更容易達成。

## <a name="material-types"></a>材質類型

Azure 遠端呈現有兩種不同的材質類型：

* [PBR 材質](../overview/features/pbr-materials.md)用於應該盡可能實際上正確轉譯的表面。 使用「實際型轉譯」(PBR) 來計算這些材質的真實感光源。 若要充分利用此材質類型，請務必提供高品質的輸入資料，例如粗糙度和一般地圖。

* [色彩材質](../overview/features/color-materials.md)用於不需要其他光源的情況。 這些材質永遠都是全亮的，並且更容易設定。 色彩材質用於應該完全沒有光源的資料，或已經合併靜態光源的資料，例如透過[攝影測量](https://en.wikipedia.org/wiki/Photogrammetry)取得的模型。

## <a name="mesh-vs-meshcomponent-material-assignment"></a>網格與MeshComponent 材質指派

[網格](meshes.md)有一或多個子網格。 每個子網格都會參考一個材質。 您可以將材質變更為直接在網格上使用，或者您可以覆寫要在 [MeshComponent](meshes.md#meshcomponent) 上用於子網格的材質。

當您直接在網格資源上修改材質時，這項變更會影響該網格的所有執行個體。 不過，在 MeshComponent 上進行變更時，只會影響一個網格執行個體。 哪一個方法比較合適，取決於所需的行為，但是修改 MeshComponent 是較常見的方法。

## <a name="material-de-duplication"></a>材質重複資料刪除

在轉換期間，具有相同屬性和紋理的多個材質會自動重複資料刪除成單一材質。 您可以在[轉換設定](../how-tos/conversion/configure-model-conversion.md)中停用這項功能，但是建議您將其保持開啟以獲得最佳效能。

## <a name="material-classes"></a>材質類別

API 所提供的所有材質都是衍生自基底類別 `Material`。 您可以透過 `Material.MaterialSubType` 或直接將其轉換來查詢其類型：

```cs
void SetMaterialColorToGreen(Material material)
{
    if (material.MaterialSubType == MaterialType.Color)
    {
        ColorMaterial colorMaterial = material as ColorMaterial;
        colorMaterial.AlbedoColor = new Color4(0, 1, 0, 1);
        return;
    }

    PbrMaterial pbrMat = material as PbrMaterial;
    if (pbrMat != null)
    {
        pbrMat.AlbedoColor = new Color4(0, 1, 0, 1);
        return;
    }
}
```

```cpp
void SetMaterialColorToGreen(ApiHandle<Material> material)
{
    if (material->GetMaterialSubType() == MaterialType::Color)
    {
        ApiHandle<ColorMaterial> colorMaterial = material.as<ColorMaterial>();
        colorMaterial->SetAlbedoColor({ 0, 1, 0, 1 });
        return;
    }

    if (material->GetMaterialSubType() == MaterialType::Pbr)
    {
        ApiHandle<PbrMaterial> pbrMat = material.as<PbrMaterial>();
        pbrMat->SetAlbedoColor({ 0, 1, 0, 1 });
        return;
    }
}
```

## <a name="api-documentation"></a>API 文件

* [C # 材質類別](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.material)
* [C # ColorMaterial 類別](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.colormaterial)
* [C # PbrMaterial 類別](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.pbrmaterial)
* [C # RemoteManager. CreateMaterial ( # B1 ](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.remotemanager.creatematerial)
* [C + + 材質類別](https://docs.microsoft.com/cpp/api/remote-rendering/material)
* [C + + ColorMaterial 類別](https://docs.microsoft.com/cpp/api/remote-rendering/colormaterial)
* [C + + PbrMaterial 類別](https://docs.microsoft.com/cpp/api/remote-rendering/pbrmaterial)
* [C + + RemoteManager：： CreateMaterial ( # B1 ](https://docs.microsoft.com/cpp/api/remote-rendering/remotemanager#creatematerial)

## <a name="next-steps"></a>後續步驟

* [PBR 材質](../overview/features/pbr-materials.md)
* [色彩材質](../overview/features/color-materials.md)
