---
title: 模型格式的材料對應
description: 描述從模型來源格式到 PBR 材質的預設轉換
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: ce287ed94066aac4b900d2ddb02579a54b8550f6
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680384"
---
# <a name="material-mapping-for-model-formats"></a>模型格式的材料對應

當源資產[轉換為模型時](../how-tos/conversion/model-conversion.md),轉換器為每個[網格](../concepts/meshes.md)建立[材質](../concepts/materials.md)。 材料的建立方式可以[被覆寫](../how-tos/conversion/override-materials.md)。 但是,預設情況下,轉換將創建[PBR 材料](../overview/features/pbr-materials.md)。 由於每個源檔格式(如FBX)都使用自己的約定來定義材質,因此這些約定必須映射到 Azure 遠端呈現的 PBR 材質參數。 

本文列出了用於將材質從源資產轉換為運行時材質的精確映射。

## <a name="gltf"></a>glTF

Azure 遠端渲染中幾乎支援 glTF 2.0 規範的所有內容,但*EeeeFactor*和*Eeee 紋理*除外。

下表顯示對應：

| glTF | Azure 遠端轉譯 |
|:-------------------|:--------------------------|
|   基本顏色因數   |   阿貝多色              |
|   基色紋理  |   阿爾貝多地圖                |
|   金屬因數    |   金屬性                |
|   金屬紋理   |   金屬度圖             |
|   粗糙度因數   |   粗糙度                |
|   粗糙紋理  |   粗糙度地圖             |
|   遮擋因數   |   閉塞                |
|   遮擋紋理  |   遮擋對應             |
|   正常紋理     |   正常地圖                |
|   阿爾法庫夫       |   阿爾法剪輯閾值       |
|   阿爾法模式.OPAQUE  |   AlphaClip 啟用 = false,透明 = 假 |
|   阿爾法模式.MASK    |   AlphaClip 啟用 = true,透明 = 假  |
|   阿爾法模式.BLEND   |   透明 = 真實     |
|   雙面       |   是雙面            |
|   發光因數    |   -                        |
|   電子紋理   |   -                        |

glTF 中的每個紋理都可以有`texCoord`一 個值,Azure 遠端呈現材質中也支援該值。

### <a name="embedded-textures"></a>內嵌式紋理

支援嵌入*\*在 .bin*或*\*.glb*檔中的紋理。

### <a name="supported-gltf-extension"></a>支援的 glTF 延伸

除了基本功能集外,Azure 遠端呈現還支援以下 glTF 擴展:

* [MSFT_packing_occlusionRoughnessMetallic](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Vendor/MSFT_packing_occlusionRoughnessMetallic/README.md)
* [MSFT_texture_dds](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Vendor/MSFT_texture_dds/README.md)
* [KHR_materials_unlit:](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Khronos/KHR_materials_unlit/README.md)對應於[顏色材料](../overview/features/color-materials.md)。 對於*放流*材料,建議使用此擴展。
* [KHR_materials_pbrSpecularGlossiness:](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Khronos/KHR_materials_pbrSpecularGlossiness/README.md)而不是金屬粗糙紋理,你可以提供漫反射鏡光澤紋理。 Azure 遠端呈現實現直接遵循擴展中的轉換公式。

## <a name="fbx"></a>Fbx

FBX 格式是閉源的,FBX 材料通常與 PBR 材料不相容。 FBX 使用有許多唯一參數與屬性的曲面的複雜描述,**並非所有曲面都由 Azure 遠端呈現導管使用**。

> [!IMPORTANT]
> Azure 遠端呈現模型轉換管道僅支援**FBX 2011 及更高**版本。

FBX 格式定義了材料的保守方法,官方 FBX 規範中只有兩種類型:

* *蘭伯特*- 不是常用相當一段時間已經,但它仍然支援轉換為Phong在轉換時間。
* *Phong* - 幾乎所有的材料和大多數內容工具都使用這種類型。

Phong 模型更加精確,用作 FBX 材料*的唯*一模型。 下面將稱為*FBX 材料*。

> Maya 通過定義材質的 PBR 和 Stingray 類型的自定義屬性,為 FBX 使用兩個自定義擴展。 這些詳細資訊不包括在 FBX 規範中,因此 Azure 遠端呈現當前不支援它。

FBX 材料使用漫反射-鏡面-鏡面水準概念,因此,要從漫反射紋理轉換為反光度貼圖,我們需要計算其他參數以從漫反射中減去它們。

> FBX 中的所有顏色和紋理都位於 sRGB 空間(也稱為 Gamma 空間),但 Azure 遠端渲染在可視化期間使用線性空間,在幀末尾將所有內容轉換回 sRGB 空間。 Azure 遠端呈現資產管道將所有內容轉換為線性空間,將其作為準備的數據發送到呈現器。

下表顯示了如何將紋理從FBX材質映射到Azure遠端渲染材質。 其中一些不是直接使用,而是與參與公式的其他紋理(例如漫反射紋理)結合使用:

| Fbx | Azure 遠端轉譯 |
|:-----|:----|
| 環境色 | 遮擋貼圖   |
| 漫反射顏色 | *用來阿爾貝多, 金屬度* |
| 透明色 | *用來阿爾貝多的阿爾法通道* |
| 透明度因素 | *用來阿爾貝多的阿爾法通道* |
| 不透明度 | *用來阿爾貝多的阿爾法通道* |
| 鏡面顏色 | *用來阿爾貝多, 金屬性, 粗糙度* |
| 鏡面因數| *用來阿爾貝多, 金屬性, 粗糙度* |
| 光澤指數 | *用來阿爾貝多, 金屬性, 粗糙度* |
| 法線圖 | 法線圖 |
| 撞 | *轉換為普通地圖* |
| 色色顏色 | - |
| 發射因數 | - |
| 反射顏色 | - |
| 置換顏色 | - |

由於必須做出許多假設,上述映射是材料轉換中最複雜的部分。 我們將在下面討論這些假設。

下面使用一些定義:

* `Specular` =  `SpecularColor` * `SpecularFactor`
* `SpecularIntensity` = `Specular`.紅色 = 0.2125 = `Specular`.綠色 = 0.7154 * `Specular`藍色 0.0721
* `DiffuseBrightness`[ 0.299 `Diffuse`] 。紅色<sup>2</sup> = 0.587 * `Diffuse`。綠色<sup>2</sup> = 0.114 * `Diffuse`。藍色<sup>2</sup>
* `SpecularBrightness`[ 0.299 `Specular`] 。紅色<sup>2</sup> = 0.587 * `Specular`。綠色<sup>2</sup> = 0.114 * `Specular`。藍色<sup>2</sup>
* `SpecularStrength`=`Specular`最大 (。紅色`Specular`, .綠色, `Specular`.藍色)

鏡面強度公式[從這裏](https://en.wikipedia.org/wiki/Luma_(video))獲得。
亮度公式在此[規範](http://www.itu.int/dms_pubrec/itu-r/rec/bt/R-REC-BT.601-7-201103-I!!PDF-E.pdf)中描述。

### <a name="roughness"></a>粗糙度

`Roughness`計算與使用`Specular``ShininessExponent`[此公式](https://www.cs.cornell.edu/~srm/publications/EGSR07-btdf.pdf)。 該公式是 Phong 鏡面指數的粗糙度近似值:

```Cpp
Roughness = sqrt(2 / (ShininessExponent * SpecularIntensity + 2))
```

### <a name="metalness"></a>金屬性

`Metalness`從[glTF](https://github.com/bghgary/glTF/blob/gh-pages/convert-between-workflows-bjs/js/babylon.pbrUtilities.js)`Diffuse`規範 中`Specular`計算並使用 此公式。

這裡的想法是,我們求解方程:Ax<sup>2</sup> + Bx + C = 0。
基本上,電介質表面以鏡面方式反射大約4%的光,其餘是漫反射的。 金屬表面以漫反射方式反射沒有光線,但全部以鏡面方式反射。
此配方有幾個缺點,因為無法區分光澤塑膠和光澤金屬表面。 我們假設大多數時候表面具有金屬特性,因此光滑的塑膠/橡膠表面可能看起來不按預期。
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

`Albedo`從`Diffuse``Specular`和計算。 `Metalness`

如"金屬度"部分所述,電介質表面反射大約 4% 的光。  
這裡的想法是使用`Dielectric``Metal``Metalness`值作為因數在顏色之間線性插值。 如果金屬性為`0.0`,則根據鏡面的不同,它將是深色(如果鏡面高)或漫反射不會更改(如果沒有鏡面)。 如果金屬度值較大,則漫反射顏色將消失,轉而採用鏡面顏色。

```Cpp
dielectricSpecularReflectance = 0.04
oneMinusSpecularStrength = 1 - SpecularStrength

dielectricColor = diffuseColor * (oneMinusSpecularStrength / (1.0f - dielectricSpecularReflectance) / max(1e-4, 1.0 - metalness))
metalColor = (Specular - dielectricSpecularReflectance * (1.0 - metalness)) * (1.0 / max(1e-4, metalness))
albedoRawColor = lerpColors(dielectricColor, metalColor, metalness * metalness)
AlbedoRGB = clamp(albedoRawColor, 0.0, 1.0);
```

`AlbedoRGB`已按上述公式計算,但 Alpha 通道需要額外的計算。 FBX 格式對透明度含糊不清,並且有許多方法可以定義它。 不同的內容工具使用不同的方法。 這裡的想法是將它們統一到一個公式中。 但是,如果某些資產不是以通用方式創建的,則會使某些資產錯誤地顯示為透明。

這是從計算的`TransparentColor``TransparencyFactor` `Opacity` :

如果`Opacity`被定義,然後直接使用它`AlbedoAlpha` = `Opacity`:  
如果`TransparencyColor`已定義,`AlbedoAlpha`則 =`TransparentColor`1.0 - (()。紅色`TransparentColor`= 。綠色`TransparentColor`= .藍色 / 3.0) 其他  
如果`TransparencyFactor``AlbedoAlpha`= 1.0 -`TransparencyFactor`

最終`Albedo`顏色有四個通道,`AlbedoRGB`與`AlbedoAlpha`結合 。

### <a name="summary"></a>摘要

在這裏總結`Albedo`, 將非常接近`Diffuse`原來的`Specular`,如果 接近零。 否則,表面將看起來像金屬表面,並失去漫反射的顏色。 如果`ShininessExponent`足夠大且明亮,表面看起來更拋光`Specular`和反射。 否則,表面看起來粗糙,幾乎不能反映環境。

### <a name="known-issues"></a>已知問題

* 當前公式不適用於簡單的彩色幾何體。 如果`Specular`足夠明亮,則所有幾何體都變為無顏色的反射金屬表面。 這裡的解決方法是從原來的下降到`Specular`30%,或使用轉換設定[fbx 假設金屬](../how-tos/conversion/configure-model-conversion.md#converting-from-older-fbx-formats-with-a-phong-material-model)。
* PBR 材料最近`Maya`被 添加`3DS Max`到和 內容創建工具中。 他們使用自定義使用者定義的黑盒屬性將其傳遞給FBX。 Azure 遠端呈現不會讀取這些其他屬性,因為它們沒有記錄,並且格式是閉源的。

## <a name="next-steps"></a>後續步驟

* [模型轉換](../how-tos/conversion/model-conversion.md)
* [模型轉換期間的覆寫材料](../how-tos/conversion/override-materials.md)
