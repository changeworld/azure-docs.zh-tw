---
title: 模型格式的材質對應
description: 描述從模型來源格式到 .PBR 材質的預設轉換
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: f1ae8ca1ef940e45c2d32adc9a002b349f9e1b44
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84783005"
---
# <a name="material-mapping-for-model-formats"></a>模型格式的材質對應

當來源資產轉換成[模型](../how-tos/conversion/model-conversion.md)時，轉換器會建立每個[網格](../concepts/meshes.md)的[材質](../concepts/materials.md)。 可以覆[寫](../how-tos/conversion/override-materials.md)材料的建立方式。 不過，轉換預設會建立[.pbr](../overview/features/pbr-materials.md)資料。 由於每個來源檔案格式（例如 FBX）都會使用自己的慣例來定義材質，因此這些慣例必須對應至 Azure 遠端轉譯的 .PBR 材質參數。 

本文列出用來將資料從來源資產轉換成執行時間材質的確切對應。

## <a name="gltf"></a>glTF

除了*EmissiveFactor*和*EmissiveTexture*，Azure 遠端轉譯中幾乎支援來自 glTF 2.0 規格的所有專案。

下表顯示對應：

| glTF | Azure 遠端轉譯 |
|:-------------------|:--------------------------|
|   baseColorFactor   |   albedoColor              |
|   baseColorTexture  |   albedoMap                |
|   metallicFactor    |   metalness                |
|   metallicTexture   |   metalnessMap             |
|   roughnessFactor   |   粗糙度                |
|   roughnessTexture  |   roughnessMap             |
|   occlusionFactor   |   遮蔽                |
|   occlusionTexture  |   occlusionMap             |
|   normalTexture     |   normalMap                |
|   AlphaCutoff       |   AlphaClipThreshold       |
|   AlphaMode 不透明  |   AlphaClipEnabled = false，isTransparent = false |
|   AlphaMode. MASK    |   AlphaClipEnabled = true，isTransparent = false  |
|   AlphaMode BLEND   |   isTransparent = true     |
|   doubleSided       |   isDoubleSided            |
|   emissiveFactor    |   -                        |
|   emissiveTexture   |   -                        |

GlTF 中的每個材質都可以有一個 `texCoord` 值，這在 Azure 遠端轉譯材料中也受到支援。

### <a name="embedded-textures"></a>內嵌紋理

支援內嵌在* \* bin*或* \* . glb*檔案中的紋理。

### <a name="supported-gltf-extension"></a>支援的 glTF 擴充功能

除了基本功能集之外，Azure 遠端轉譯還支援下列 glTF 延伸模組：

* [MSFT_packing_occlusionRoughnessMetallic](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Vendor/MSFT_packing_occlusionRoughnessMetallic/README.md)
* [KHR_materials_unlit](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Khronos/KHR_materials_unlit/README.md)：對應至 [[色彩材質](../overview/features/color-materials.md)]。 針對*放射*材質，建議使用此延伸模組。
* [KHR_materials_pbrSpecularGlossiness](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Khronos/KHR_materials_pbrSpecularGlossiness/README.md)：您可以提供漫射反射材質紋理，而不是金屬粗糙材質。 Azure 遠端轉譯的執行直接遵循延伸模組的轉換公式。

## <a name="fbx"></a>FBX

FBX 格式是已關閉的來源，而 FBX 的材料一般不會與 .PBR 資料相容。 FBX 會使用具有許多唯一參數和屬性之介面的複雜描述，而**不是由 Azure 遠端呈現管線使用**。

> [!IMPORTANT]
> Azure 遠端轉譯模型轉換管線僅支援**FBX 2011 和更高版本**。

FBX 格式定義了保守的材料方法，官方 FBX 規格中只有兩種類型：

* *Lambert* -不常用於過去一段時間，但仍支援在轉換時轉換為 Phong。
* *Phong* -幾乎所有的素材和大部分的內容工具都使用這種類型。

Phong 模型更為精確，而且是用來作為 FBX 材質的*唯一*模型。 其底下會稱為*FBX 材質*。

> Maya 使用兩個自訂延伸模組來進行 FBX，方法是定義資料的 .PBR 和 Stingray 類型的自訂屬性。 這些詳細資料不包含在 FBX 規格中，因此 Azure 遠端轉譯目前不支援。

FBX 材質使用擴散-反射 SpecularLevel 概念，因此若要從擴散材質轉換為 albedo 對應，我們需要計算其他參數，以將它們從擴散中減去。

> FBX 中的所有色彩和材質都是在 sRGB 空間（也稱為 Gamma 空間）中，但在視覺效果期間，Azure 遠端轉譯會使用線性空間，而在框架結尾會將所有專案轉換回 sRGB 空間。 Azure 遠端轉譯資產管線會將所有專案轉換成線性空間，以將它當做備妥的資料傳送至轉譯器。

下表顯示材質如何從 FBX 材質對應至 Azure 遠端轉譯材料。 其中有些不會直接使用，而是與參與公式的其他材質結合（例如，擴散材質）：

| FBX | Azure 遠端轉譯 |
|:-----|:----|
| AmbientColor | 遮蔽地圖   |
| DiffuseColor | *用於 Albedo、Metalness* |
| TransparentColor | *用於 Albedo 的 Alpha 色板* |
| TransparencyFactor | *用於 Albedo 的 Alpha 色板* |
| 不透明度 | *用於 Albedo 的 Alpha 色板* |
| SpecularColor | *用於 Albedo、Metalness、粗糙度* |
| SpecularFactor| *用於 Albedo、Metalness、粗糙度* |
| ShininessExponent | *用於 Albedo、Metalness、粗糙度* |
| NormalMap | NormalMap |
| 貼圖 | *轉換成 NormalMap* |
| EmissiveColor | - |
| EmissiveFactor | - |
| ReflectionColor | - |
| DisplacementColor | - |

上述對應是材質轉換中最複雜的部分，因為必須進行許多假設。 我們將在下面討論這些假設。

以下使用的定義如下：

* `Specular` =  `SpecularColor` * `SpecularFactor`
* `SpecularIntensity` = `Specular`.Red ∗ 0.2125 + `Specular` 。綠色∗ 0.7154 + `Specular` 。Blue ∗0.0721
* `DiffuseBrightness`= 0.299 * `Diffuse` 。紅色<sup>2</sup> + 0.587 * `Diffuse` 。綠色<sup>2</sup> + 0.114 * `Diffuse` 。藍色<sup>2</sup>
* `SpecularBrightness`= 0.299 * `Specular` 。紅色<sup>2</sup> + 0.587 * `Specular` 。綠色<sup>2</sup> + 0.114 * `Specular` 。藍色<sup>2</sup>
* `SpecularStrength`= 最大值（ `Specular` 。紅色， `Specular` 。綠色， `Specular` 。藍天

SpecularIntensity 公式是從[這裡](https://en.wikipedia.org/wiki/Luma_(video))取得。
此[規格](http://www.itu.int/dms_pubrec/itu-r/rec/bt/R-REC-BT.601-7-201103-I!!PDF-E.pdf)中會描述亮度公式。

### <a name="roughness"></a>粗糙度

`Roughness`是從計算 `Specular` ，並 `ShininessExponent` 使用[此公式](https://www.cs.cornell.edu/~srm/publications/EGSR07-btdf.pdf)。 此公式是 Phong 反射指數中的粗糙度近似值：

```Cpp
Roughness = sqrt(2 / (ShininessExponent * SpecularIntensity + 2))
```

### <a name="metalness"></a>Metalness

`Metalness`是從 `Diffuse` `Specular` [glTF 規格](https://github.com/bghgary/glTF/blob/gh-pages/convert-between-workflows-bjs/js/babylon.pbrUtilities.js)計算並使用此公式。

這裡的想法是，我們要解決方程式： Ax<sup>2</sup> + Bx + C = 0。
基本上，dielectric 表面會以反射方式反映大約4% 的燈，而其餘的則是擴散。 金屬表面不會以擴散方式反映任何光線，但全都以反射的方式呈現。
此公式有一些缺點，因為沒有任何方法可以區別光澤塑膠和光澤金屬表面。 我們假設大部分的情況下，表面都具有金屬屬性，因此光澤塑膠/橡膠表面看起來可能不如預期。
```cpp
dielectricSpecularReflectance = 0.04
oneMinusSpecularStrength = 1 - SpecularStrength

A = dielectricSpecularReflectance
B = (DiffuseBrightness * (oneMinusSpecularStrength / (1 - A)) + SpecularBrightness) - 2 * A
C = A - SpecularBrightness
squareRoot = sqrt(max(0.0, B * B - 4 * A * C))
value = (-B + squareRoot) / (2 * A)
Metalness = clamp(value, 0.0, 1.0);
```

### <a name="albedo"></a>Albedo

`Albedo`是從 `Diffuse` 、 `Specular` 和計算而來 `Metalness` 。

如 Metalness 一節所述，dielectric 表面會反映大約4% 的光線。  
這裡的概念是使用值做為因素，以線性插補 `Dielectric` 和 `Metal` 色彩 `Metalness` 。 如果 metalness 為 `0.0` ，則視反射而定，它會是暗色（如果反射很高）或擴散不會變更（如果沒有反射）。 如果 metalness 是較大的值，則擴散色彩將會消失，而改用反射色彩。

```Cpp
dielectricSpecularReflectance = 0.04
oneMinusSpecularStrength = 1 - SpecularStrength

dielectricColor = diffuseColor * (oneMinusSpecularStrength / (1.0f - dielectricSpecularReflectance) / max(1e-4, 1.0 - metalness))
metalColor = (Specular - dielectricSpecularReflectance * (1.0 - metalness)) * (1.0 / max(1e-4, metalness))
albedoRawColor = lerpColors(dielectricColor, metalColor, metalness * metalness)
AlbedoRGB = clamp(albedoRawColor, 0.0, 1.0);
```

`AlbedoRGB`已由上述公式計算，但 Alpha 色板需要額外的計算。 FBX 格式與透明度無關，有許多方式可以定義它。 不同的內容工具會使用不同的方法。 這裡的想法是將它們整合成一個公式。 不過，如果某些資產不是以常見的方式建立，則會使其不正確地顯示為透明。

這是從 `TransparentColor` 、 `TransparencyFactor` 、 `Opacity` ：

如果 `Opacity` 已定義，則直接使用： `AlbedoAlpha`  =  `Opacity` else  
如果已 `TransparencyColor` 定義，則 `AlbedoAlpha` = 1.0-（ `TransparentColor` 。紅色 + `TransparentColor` 。綠色 + `TransparentColor` 。藍色）/3.0）其他  
若 `TransparencyFactor` 為，則 `AlbedoAlpha` = 1.0-`TransparencyFactor`

最終 `Albedo` 色彩有四個通道，並將 `AlbedoRGB` 與結合 `AlbedoAlpha` 。

### <a name="summary"></a>摘要

總而言之， `Albedo` `Diffuse` 如果接近零，會非常接近原始 `Specular` 。 否則，表面看起來會像是金屬表面，而失去擴散色彩。 如果 `ShininessExponent` 夠大而且很明亮，表面看起來會更精美，而且也更具反射 `Specular` 。 否則，表面看起來會很粗糙，而且幾乎不會反映環境。

### <a name="known-issues"></a>已知問題

* 目前的公式不適用於簡單的彩色幾何。 如果 `Specular` 夠亮，則所有幾何都會變成反射金屬表面，而不會有任何色彩。 此處的因應措施是 `Specular` 從原始或使用轉換設定[fbxAssumeMetallic](../how-tos/conversion/configure-model-conversion.md#converting-from-older-fbx-formats-with-a-phong-material-model)，以降低至30%。
* .PBR 材料最近已新增至 `Maya` 和 `3DS Max` 內容建立工具。 他們會使用自訂的使用者定義的黑盒屬性，將它傳遞給 FBX。 Azure 遠端轉譯不會讀取這些額外的屬性，因為它們並未記載，且格式為已關閉的來源。

## <a name="next-steps"></a>後續步驟

* [模型轉換](../how-tos/conversion/model-conversion.md)
* [在模型轉換期間覆寫材質](../how-tos/conversion/override-materials.md)
