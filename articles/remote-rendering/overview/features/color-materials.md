---
title: 色彩材質
description: 描述色彩材質類型。
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 7cbcaefcc087c9f1c7c09668a27fbdef9a4802d3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681073"
---
# <a name="color-materials"></a>色彩材質

*色彩材質*是 Azure 遠端轉譯中支援的其中一種[材質類型](../../concepts/materials.md)。 它們是用於不應接收任何類型光源的[網格](../../concepts/meshes.md)，而是隨時都是完全亮度。 這可能是「光亮」資料的情況，例如汽車儀表板、light 燈泡，或已納入靜態光源的資料，例如透過[攝影測量](https://en.wikipedia.org/wiki/Photogrammetry)取得的模型。

因為色彩材質較簡單的陰影模型，所以轉譯為非[.pbr 材質](pbr-materials.md)會更有效率。 它們也支援不同的透明度模式。

## <a name="common-material-properties"></a>一般材質屬性

這些屬性通用於所有材質：

* **albedoColor：** 此色彩會乘以其他色彩，例如*albedoMap*或*頂點色彩*。 如果已啟用材質的*透明度*，Alpha 色板會用來調整不透明度，其`1`意義完全不透明且`0`意義完全透明。 預設為白色。

  > [!NOTE]
  > 因為色彩材質不會反映環境，所以完全透明的色彩材質會變成不可見。 這對於[.pbr 材質](pbr-materials.md)而言是不同的。

* **albedoMap：** 每圖元 albedo 值的[2d 材質](../../concepts/textures.md)。

* **AlphaClipEnabled**和**AlphaClipThreshold：** 如果*AlphaClipEnabled*為 true，則不會繪製 albedo Alpha 值低於*AlphaClipThreshold*的所有圖元。 即使沒有啟用透明度，也可以使用 Alpha 裁剪，而且呈現速度會更快。 不過，Alpha 裁剪材質的轉譯速度仍然會比完全不透明的材質更慢。 預設會停用 Alpha 裁剪。

* **textureCoordinateScale**和**textureCoordinateOffset：** 尺規會乘以 UV 材質座標，而位移會加入其中。 可以用來延展和轉換材質。 預設尺規為（1，1），而 offset 為（0，0）。

* **useVertexColor：** 如果網格包含頂點色彩，而且已啟用此選項，則網格的頂點色彩會乘至*albedoColor*和*albedoMap*。 預設會停用頂點色彩。

* **isDoubleSided：** 如果 sidedness 設定為 true，則會轉譯具有此材質的三角形，即使相機正在查看其背景表面也一樣。 預設會停用此選項。 另請參閱[單側](single-sided-rendering.md)轉譯。

## <a name="color-material-properties"></a>色彩材質屬性

下列屬性專屬於色彩材質：

* **vertexMix：** 和`0` `1`之間的這個值會指定[網格](../../concepts/meshes.md)中的頂點色彩對最終色彩的高度。 在預設值為1時，頂點色彩會與 albedo 色彩完全相乘。 值為0時，會完全忽略頂點色彩。

* **transparencyMode：** 與[.pbr 材質](pbr-materials.md)相反，色彩材質會區分不同的透明度模式：

  1. 不**透明：** 預設模式會停用透明度。 不過，Alpha 裁剪仍然可以，而且應該是慣用的（如果已足夠的話）。
  
  1. **AlphaBlended：** 此模式類似于 .PBR 材質的透明度模式。 它應該用於觀看材質這類資料，例如玻璃。

  1. **加法：** 此模式是最簡單且最有效率的透明度模式。 材質的比重會加入轉譯的影像中。 此模式可用來模擬發光（但仍是透明）物件，例如用來反白顯示重要物件的標記。

## <a name="next-steps"></a>後續步驟

* [PBR 材質](pbr-materials.md)
* [紋理](../../concepts/textures.md)
* [網狀](../../concepts/meshes.md)
