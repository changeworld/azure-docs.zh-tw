---
title: Fresnel 效果
description: Fresnel 材質效果的功能說明頁面
author: jumeder
ms.author: jumeder
ms.date: 11/09/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 0596d118d1f3c09bc295891f023fe9990f3f1e05
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94557590"
---
# <a name="fresnel-effect"></a>Fresnel 效果

Fresnel 效果材質功能是一種不實際正確的臨機操作效果。 這項功能是以物件的實體觀察為基礎，而這些角度會變得更反射。 Fresnel 反射率本身已實際併入 Azure 遠端轉譯中使用的 [.pbr 材質模型](../../overview/features/pbr-materials.md) 。 相反地，fresnel 效果材質功能只是一個加總的色彩效果，不會相依于 [燈光](../../overview/features/lights.md) 或 [天空環境](../../overview/features/sky.md)。

Fresnel 效果可讓受影響的物件在其邊緣周圍具有彩色的照射。 您可以在下列各節中找到效果自訂和轉譯結果範例的相關資訊。

## <a name="enabling-the-fresnel-effect"></a>啟用 fresnel 效果

若要使用 fresnel 效果功能，必須在有問題的資料上啟用。 您可以藉由在[.pbr 材質](../../overview/features/pbr-materials.md)上設定[PbrMaterialFeatures](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.pbrmaterialfeatures)的 FresnelEffect 位來啟用它。 相同的模式適用于 [ColorMaterialFeatures](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering) 和 [色彩材質](../../overview/features/color-materials.md)。 如需使用方式示範，請參閱程式碼範例一節。

啟用之後，fresnel 效果就會立即顯示。 根據預設，會有白色 (1，1，1，1) ，而指數為1。 您可以使用以下的參數 setter 自訂這些設定。

## <a name="customizing-the-effect-appearance"></a>自訂效果外觀

目前，您可以使用下列屬性，針對每個材質自訂 fresnel 效果：

| 材質屬性 | 類型 | 說明 |
|-------------------|------|-------------|
| FresnelEffectColor | Color4 | 最常新增的色彩，會隨著 fresnel 的亮度。 目前忽略 Alpha 色板。 |
| FresnelEffectExponent | FLOAT | Fresnel 的擴張。 範圍從 0.01 (散佈在所有物件) 到 10 (只) 最 gracing 的角度。 |

在實務上，不同的色彩和指數設定看起來會像這樣：

![Fresnel 效果範例](./media/fresnel-effect-examples.png)

針對每個色彩資料列，fresnel 效果的指數會逐漸增加為1到10。 這樣做會逐漸將 fresnel 的亮度拉到已觀看物件的邊緣。 Fresnel 效果也不會受到透明度影響，如下列範例所示：

![Fresnel 效果透明範例](./media/fresnel-effect-transparent-examples.png)

如圖所示，對角線上的物件是完全透明的，但仍有 fresnel 的閃光。 此效果會模擬該方面的實際 fresnel，也會出現在這些螢幕擷取畫面中。

## <a name="code-samples"></a>程式碼範例

下列程式碼範例會示範如何啟用和自訂 [.pbr 材質](../../overview/features/pbr-materials.md) 和 [色彩材質](../../overview/features/color-materials.md)的 fresnel 效果：

```cs
    void SetFresnelEffect(AzureSession session, Material material)
    {
        if (material.MaterialSubType == MaterialType.Pbr)
        {
            var pbrMaterial = material as PbrMaterial;
            pbrMaterial.PbrFlags |= PbrMaterialFeatures.FresnelEffect;
            pbrMaterial.FresnelEffectColor = new Color4(1.0f, 0.5f, 0.1f, 1.0f);
            pbrMaterial.FresnelEffectExponent = 3.141592f;
        }
        else if (material.MaterialSubType == MaterialType.Color)
        {
            var colorMaterial = material as ColorMaterial;
            colorMaterial.ColorFlags |= ColorMaterialFeatures.FresnelEffect;
            colorMaterial.FresnelEffectColor = new Color4(0.25f, 1.0f, 0.25f, 1.0f);
            colorMaterial.FresnelEffectExponent = 7.654321f;
        }
    }
```

```cpp
void SetFresnelEffect(ApiHandle<AzureSession> session, ApiHandle<Material> material)
{
    if (material->GetMaterialSubType() == MaterialType::Pbr)
    {
        auto pbrMaterial = material.as<PbrMaterial>();
        auto featureFlags = PbrMaterialFeatures((int32_t)pbrMaterial->GetPbrFlags() | (int32_t)PbrMaterialFeatures::FresnelEffect);
        pbrMaterial->SetPbrFlags(featureFlags);
        pbrMaterial->SetFresnelEffectColor(Color4{ 1.f, 0.5f, 0.1f, 1.f });
        pbrMaterial->SetFresnelEffectExponent(3.141592f);

    }
    else if (material->GetMaterialSubType() == MaterialType::Color)
    {
        auto colorMaterial = material.as<ColorMaterial>();
        auto featureFlags = ColorMaterialFeatures((int32_t)colorMaterial->GetColorFlags() | (int32_t)ColorMaterialFeatures::FresnelEffect);
        colorMaterial->SetColorFlags(featureFlags);
        colorMaterial->SetFresnelEffectColor(Color4{ 0.25f, 1.f, 0.25f, 1.f });
        colorMaterial->SetFresnelEffectExponent(7.654321f);
    }
}
```

## <a name="api-documentation"></a>API 文件

* [C # PbrMaterialFeatures](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.pbrmaterialfeatures)
* [C + + PbrMaterialFeatures](https://docs.microsoft.com/cpp/api/remote-rendering/pbrmaterialfeatures)
* [C # ColorMaterialFeatures](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.colormaterialfeatures)
* [C + + ColorMaterialFeatures](https://docs.microsoft.com/cpp/api/remote-rendering/colormaterialfeatures)

## <a name="next-steps"></a>後續步驟

* [材質](../../concepts/materials.md)
* [.PBR 材質](../../overview/features/pbr-materials.md)
* [色彩材質](../../overview/features/color-materials.md)