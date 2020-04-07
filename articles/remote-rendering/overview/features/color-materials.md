---
title: 色彩材質
description: 描述顏色材質類型。
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 7cbcaefcc087c9f1c7c09668a27fbdef9a4802d3
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681073"
---
# <a name="color-materials"></a>色彩材質

*顏色材質*是 Azure 遠端渲染中支援[的材料類型](../../concepts/materials.md)之一。 它們用於不應接收任何照明的[網,](../../concepts/meshes.md)而是隨時充滿亮度。 對於「發光」材料(如汽車儀錶板、燈泡)或已經集成了靜態照明的數據(例如通過[攝影測量](https://en.wikipedia.org/wiki/Photogrammetry)獲得的模型)來說,情況可能如此。

顏色材質的渲染效率比[PBR 材質](pbr-materials.md)更有效,因為它們的著色模型更簡單。 它們還支援不同的透明度模式。

## <a name="common-material-properties"></a>常見材料屬性

這些屬性是所有材料的通用屬性:

* **阿貝多顏色:** 此顏色與其他顏色(如*反色映射*或*頂點顏色*)相乘。 如果在材質上啟用*了透明度*,則 Alpha 通道用於調整`1`不透明度, 其含`0`義完全不透明, 含義完全透明。 默認值為白色。

  > [!NOTE]
  > 由於顏色材料不反映環境,因此完全透明的彩色材質變得不可見。 對於[PBR 材料](pbr-materials.md),這是不同的。

* **阿爾貝多地圖:** 每像素反貝度值的[2D 紋理](../../concepts/textures.md)。

* **AlphaClip 啟用**和**alphaClip 閾值:** 如果*AlphaClip 啟用*為 true,則不會繪製所有白數 alpha 值低於*AlphaClip 閾值*的圖元。 即使不啟用透明度,也可以使用 Alpha 裁剪,渲染速度也快得多。 不過,Alpha 剪切材質渲染的速度仍然比完全不透明的材料慢。 默認情況下,禁用 Alpha 剪輯。

* **紋理座標和****紋理座標偏移:** 比例乘以 UV 紋理座標,偏移量添加到其中。 可用於拉伸和移動紋理。 預設比例為 (1,1),偏移量為 (0, 0)。

* **使用VertexColor:** 如果格線包含頂點顏色並啟用這個選項,則格格的頂點顏色將乘以*反色*與*反反多對應*。 默認情況下,頂點顏色被禁用。

* **是雙面:** 如果雙面設置為 true,則即使攝像機正在查看其背面,也會渲染具有此材質的三角形。 默認情況下,此選項被禁用。 另請參閱[單面渲染](single-sided-rendering.md)。

## <a name="color-material-properties"></a>色彩材質屬性

以下屬性特定於顏色材質:

* **頂點混合:** 值之間的`0`此`1`值 指定[網格](../../concepts/meshes.md)中的頂點顏色對最終顏色的貢獻程度。 在預設值 1 時,頂點顏色將完全乘以反搜尋度顏色。 值為 0 時,將完全忽略頂點顏色。

* **透明度模式:** 與[PBR 材料](pbr-materials.md)相反,顏色材料區分不同的透明度模式:

  1. **不透明:** 默認模式禁用透明度。 但是,Alpha 剪輯仍然是可能的,如果足夠,應該優先。
  
  1. **阿爾法混合:** 此模式類似於 PBR 材料的透明度模式。 它應該用於玻璃等透鏡材料。

  1. **新增劑:** 此模式是最簡單、最有效的透明度模式。 材質的貢獻將添加到渲染的圖像中。 此模式可用於類比發光(但仍透明)物件,例如用於突出顯示重要物件的標記。

## <a name="next-steps"></a>後續步驟

* [PBR 材料](pbr-materials.md)
* [紋理](../../concepts/textures.md)
* [格](../../concepts/meshes.md)
