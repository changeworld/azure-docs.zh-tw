---
title: 模型格式的材質對應
description: 描述從模型來源格式到的 .PBR 材質預設轉換
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: 8313243bf680ea1a1d63f2719b647149a04935a9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88893081"
---
# <a name="material-mapping-for-model-formats"></a>模型格式的材質對應

當來源資產轉換成[模型](../how-tos/conversion/model-conversion.md)時，轉換器會建立每個[網格](../concepts/meshes.md)的[材質](../concepts/materials.md)。 您可以覆 [寫](../how-tos/conversion/override-materials.md)材質的建立方式。 不過，根據預設，轉換將會建立 [.pbr 材質](../overview/features/pbr-materials.md)。 由於每個來源檔案格式（例如 FBX）都會使用自己的慣例來定義材質，因此這些慣例必須對應至 Azure 遠端轉譯的 .PBR 材質參數。 

本文列出用來將材質從來源資產轉換成執行時間材質的確切對應。

## <a name="gltf"></a>glTF

GlTF 2.0 規格中幾乎所有的專案都支援在 Azure 遠端轉譯中， *EmissiveFactor* 和 *EmissiveTexture*除外。

下表顯示對應：

| glTF | Azure 遠端轉譯 |
|:-------------------|:--------------------------|
|   baseColorFactor   |   albedoColor              |
|   baseColorTexture  |   albedoMap                |
|   metallicFactor    |   metalness                |
|   metallicTexture   |   metalnessMap             |
|   roughnessFactor   |   粗糙 度                |
|   roughnessTexture  |   roughnessMap             |
|   occlusionFactor   |   閉塞                |
|   occlusionTexture  |   occlusionMap             |
|   normalTexture     |   normalMap                |
|   AlphaCutoff       |   AlphaClipThreshold       |
|   AlphaMode 透明  |   AlphaClipEnabled = false，isTransparent = false |
|   AlphaMode MASK    |   AlphaClipEnabled = true，isTransparent = false  |
|   AlphaMode BLEND   |   isTransparent = true     |
|   doubleSided       |   isDoubleSided            |
|   emissiveFactor    |   -                        |
|   emissiveTexture   |   -                        |

GlTF 中的每個材質都可以有一個 `texCoord` 值，Azure 遠端轉譯的材質也有支援。

### <a name="embedded-textures"></a>內嵌紋理

支援內嵌于* \* bin*或* \* glb*檔案中的材質。

### <a name="supported-gltf-extension"></a>支援的 glTF 延伸模組

除了基本功能集之外，Azure 遠端轉譯還支援下列 glTF 延伸模組：

* [MSFT_packing_occlusionRoughnessMetallic](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Vendor/MSFT_packing_occlusionRoughnessMetallic/README.md)
* [KHR_materials_unlit](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Khronos/KHR_materials_unlit/README.md)：對應于 [色彩材質](../overview/features/color-materials.md)。 針對 *放射* 材質，建議使用此延伸模組。
* [KHR_materials_pbrSpecularGlossiness](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Khronos/KHR_materials_pbrSpecularGlossiness/README.md)：您可以提供擴散-反光紋理，而不是金屬粗糙度紋理。 Azure 遠端轉譯的執行會直接遵循延伸模組的轉換公式。

## <a name="fbx"></a>Fbx

FBX 格式是封閉來源，FBX 材質一般都不與 .PBR 材質相容。 FBX 會使用具有許多唯一參數和屬性之介面的複雜描述，而 **不是所有這些介面都是由 Azure 遠端轉譯管線使用**。

> [!IMPORTANT]
> Azure 遠端轉譯模型轉換管線只支援 **FBX 2011 和更高版本**。

FBX 格式定義了保守的材質方法，官方 FBX 規格中只有兩種類型：

* *Lambert* -目前尚未使用過一段時間，但是在轉換時轉換成 Phong 仍支援此功能。
* *Phong* -幾乎所有的材質和大部分的內容工具都使用這種類型。

Phong 模型更為精確，而且會當做 FBX 材質的 *唯一* 模型使用。 它將會被稱為 *FBX 材質*。

> Maya 藉由為數據的 .PBR 和 Stingray 類型定義自訂屬性，以使用兩個 FBX 的自訂擴充功能。 這些詳細資料不會包含在 FBX 規格中，因此目前 Azure 遠端轉譯不支援。

FBX 材質使用漫射 SpecularLevel 概念，因此若要從擴散材質轉換成 >albedo 地圖，我們需要計算其他參數，以將它們從擴散中減去。

> FBX 中的所有色彩和紋理都位於 sRGB 空間 (也稱為 Gamma 空間) 但 Azure 遠端轉譯在視覺效果期間與線性空間搭配使用，而在框架結束時會將所有內容轉換回 sRGB 空間。 Azure 遠端轉譯資產管線會將所有專案轉換為線性空間，以將其作為準備的資料傳送到轉譯器。

下表說明材質如何從 FBX 材質對應到 Azure 遠端轉譯的材質。 其中有些不是直接使用，而是與其他參與公式的材質組合 (例如，擴散材質) ：

| Fbx | Azure 遠端轉譯 |
|:-----|:----|
| AmbientColor | 遮蔽對應   |
| DiffuseColor | *用於 >albedo、Metalness* |
| TransparentColor | *用於 >albedo 的 Alpha 通道* |
| TransparencyFactor | *用於 >albedo 的 Alpha 通道* |
| 不透明度 | *用於 >albedo 的 Alpha 通道* |
| SpecularColor | *用於 >albedo、Metalness、粗糙度* |
| SpecularFactor| *用於 >albedo、Metalness、粗糙度* |
| ShininessExponent | *用於 >albedo、Metalness、粗糙度* |
| NormalMap | NormalMap |
| 撞 | *轉換成 NormalMap* |
| EmissiveColor | - |
| EmissiveFactor | - |
| ReflectionColor | - |
| DisplacementColor | - |

上述對應是材質轉換中最複雜的部分，因為有許多假設需要進行。 我們將在下面討論這些假設。

以下是一些使用的定義：

* `Specular` =  `SpecularColor` * `SpecularFactor`
* `SpecularIntensity` = `Specular`.Red ∗ 0.2125 +  `Specular` 。綠色∗ 0.7154 + `Specular` 。藍色∗0.0721
* `DiffuseBrightness` = 0.299 * `Diffuse` 。紅色<sup>2</sup> + 0.587 * `Diffuse` 。綠色<sup>2</sup> + 0.114 * `Diffuse` 。藍色<sup>2</sup>
* `SpecularBrightness` = 0.299 * `Specular` 。紅色<sup>2</sup> + 0.587 * `Specular` 。綠色<sup>2</sup> + 0.114 * `Specular` 。藍色<sup>2</sup>
* `SpecularStrength` = 最大 (`Specular` 。紅色， `Specular` 。綠色 `Specular` 。藍色) 

您可以從 [這裡](https://en.wikipedia.org/wiki/Luma_(video))取得 SpecularIntensity 公式。
亮度公式會在此 [規格](http://www.itu.int/dms_pubrec/itu-r/rec/bt/R-REC-BT.601-7-201103-I!!PDF-E.pdf)中說明。

### <a name="roughness"></a>粗糙度

`Roughness` 是 `Specular` `ShininessExponent` 使用 [此公式](https://www.cs.cornell.edu/~srm/publications/EGSR07-btdf.pdf)來計算。 此公式是 Phong 反光指數中的粗糙度近似值：

```cpp
Roughness = sqrt(2 / (ShininessExponent * SpecularIntensity + 2))
```

### <a name="metalness"></a>Metalness

`Metalness` 是從 `Diffuse` glTF 規格計算，並 `Specular` 使用這個 [公式](https://github.com/bghgary/glTF/blob/gh-pages/convert-between-workflows-bjs/js/babylon.pbrUtilities.js)。

這裡的概念是，我們要解決方程式： Ax<sup>2</sup> + Bx + C = 0。
基本上，dielectric 介面會以反射的方式反映大約4% 的燈光，其餘的則是擴散。 金屬表面不會以擴散方式反映任何光線，但全都以反射的方式呈現。
這個公式有一些缺點，因為沒有辦法區分光澤塑膠圖和光澤金屬表面。 我們假設大部分的情況下，表面都有金屬屬性，因此光澤塑膠/橡膠表面看起來可能不會如預期般顯示。

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

### <a name="albedo"></a>反照 率

`Albedo` 是從、和計算而來 `Diffuse` `Specular` `Metalness` 。

如 Metalness 一節中所述，dielectric 表面反映大約4% 的光線。  
此處的概念是 `Dielectric` `Metal` 使用 `Metalness` 值作為因素，以線性方式在和色彩之間插補。 如果 metalness 為 `0.0` ，則根據反射的不同，它會是深色 (如果反光很高) 或擴散不會變更 (如果沒有反射存在) 。 如果 metalness 是較大的值，則擴散色彩將會消失，以利反射色彩。

```cpp
dielectricSpecularReflectance = 0.04
oneMinusSpecularStrength = 1 - SpecularStrength

dielectricColor = diffuseColor * (oneMinusSpecularStrength / (1.0f - dielectricSpecularReflectance) / max(1e-4, 1.0 - metalness))
metalColor = (Specular - dielectricSpecularReflectance * (1.0 - metalness)) * (1.0 / max(1e-4, metalness))
albedoRawColor = lerpColors(dielectricColor, metalColor, metalness * metalness)
AlbedoRGB = clamp(albedoRawColor, 0.0, 1.0);
```

`AlbedoRGB` 已由上述公式計算，但是 Alpha 通道需要額外的計算。 FBX 格式對透明度而言是模糊的，有許多方式可以定義它。 不同的內容工具會使用不同的方法。 此處的概念是將它們合併成一個公式。 但是，如果不是以一般方式建立，就會使某些資產不正確地顯示為透明。

這是從 `TransparentColor` 、 `TransparencyFactor` 、 `Opacity` ：

如果 `Opacity` 已定義，則直接使用它： `AlbedoAlpha`  =  `Opacity` else  
如果 `TransparencyColor` 已定義，則 `AlbedoAlpha` = 1.0- ( # B1 `TransparentColor` 。紅色 + `TransparentColor` 。綠色 + `TransparentColor` 。藍色) /3.0) 其他  
如果 `TransparencyFactor` 為，則 `AlbedoAlpha` = 1.0- `TransparencyFactor`

最終 `Albedo` 色彩有四個通道，其中結合了 `AlbedoRGB` `AlbedoAlpha` 。

### <a name="summary"></a>摘要

總而言之， `Albedo` `Diffuse` 如果接近零，將會非常接近原來的 `Specular` 。 否則介面看起來會像金屬表面，並失去擴散色彩。 如果 `ShininessExponent` 夠大且很亮，表面看起來會更美觀且更具反射 `Specular` 。 否則，表面看起來會很粗糙，而且只會反映環境。

### <a name="known-issues"></a>已知問題

* 目前的公式無法正常適用于簡單的彩色幾何。 如果 `Specular` 夠亮了，則所有幾何都會變成反射金屬表面，沒有任何色彩。 這裡的因應措施是 `Specular` 從原始的降低為30%，或使用轉換設定 [fbxAssumeMetallic](../how-tos/conversion/configure-model-conversion.md#converting-from-older-fbx-formats-with-a-phong-material-model)。
* 最近新增了 .PBR 材質 `Maya` 和 `3DS Max` 內容建立工具。 他們使用自訂使用者定義的黑箱屬性，將它傳遞給 FBX。 Azure 遠端轉譯不會讀取這些額外的屬性，因為它們未記載且格式為封閉來源。

## <a name="next-steps"></a>後續步驟

* [模型轉換](../how-tos/conversion/model-conversion.md)
* [在模型轉換期間覆寫材質](../how-tos/conversion/override-materials.md)
