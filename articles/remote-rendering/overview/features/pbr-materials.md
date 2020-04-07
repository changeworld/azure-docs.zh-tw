---
title: PBR 材料
description: 描述 PBR 材料類型。
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 64553506f75451c50a87932904f00a7275ea9286
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680254"
---
# <a name="pbr-materials"></a>PBR 材料

*PBR 材質*是 Azure 遠端呈現中支援[的材料類型](../../concepts/materials.md)之一。 它們用於應接收逼真的照明[的網。](../../concepts/meshes.md)

PBR 代表**P**hysy **B**ased **R**端端,意味著材料以物理上合理的方式描述表面的視覺屬性,因此在所有照明條件下都能夠產生實際結果。 大多數現代遊戲引擎和內容創建工具都支援 PBR 材料,因為它們被認為是即時渲染的真實場景的最佳近似值。

![由 ARR 渲染的頭盔 glTF 範例模型](media/helmet.png)

不過,PBR材料並不是通用解決方案。 有些材質根據視角的不同反射顏色不同。 例如,一些織物或汽車塗料。 這些類型的材料不由標準 PBR 模型處理,並且當前不受 Azure 遠端呈現的支援。 這包括 PBR 擴展,如*薄膜*(多層表面)和*透明塗層*(用於汽車塗料)。

## <a name="common-material-properties"></a>常見材料屬性

這些屬性是所有材料的通用屬性:

* **阿貝多顏色:** 此顏色與其他顏色(如*反色映射*或*頂點顏色*)相乘。 如果在材質上啟用*了透明度*,則 Alpha 通道用於調整`1`不透明度, 其含`0`義完全不透明, 含義完全透明。 默認值為白色。

  > [!NOTE]
  > 當 PBR 材料完全透明時,就像一塊完全乾淨的玻璃一樣,它仍然反映了環境。 像太陽這樣的亮點在反射中仍然可見。 對於[顏色材料](color-materials.md)來說,這是不同的。

* **阿爾貝多地圖:** 每像素反貝度值的[2D 紋理](../../concepts/textures.md)。

* **AlphaClip 啟用**和**alphaClip 閾值:** 如果*AlphaClip 啟用*為 true,則不會繪製所有白數 alpha 值低於*AlphaClip 閾值*的圖元。 即使不啟用透明度,也可以使用 Alpha 裁剪,渲染速度也快得多。 不過,Alpha 剪切材質渲染的速度仍然比完全不透明的材料慢。 默認情況下,禁用 Alpha 剪輯。

* **紋理座標和****紋理座標偏移:** 比例乘以 UV 紋理座標,偏移量添加到其中。 可用於拉伸和移動紋理。 預設比例為 (1,1),偏移量為 (0, 0)。

* **使用VertexColor:** 如果格線包含頂點顏色並啟用這個選項,則格格的頂點顏色將乘以*反色*與*反反多對應*。 默認情況下,頂點顏色被禁用。

* **是雙面:** 如果雙面設置為 true,則即使攝像機正在查看其背面,也會渲染具有此材質的三角形。 對於 PBR 材料,也為後面正確計算照明。 默認情況下,此選項被禁用。 另請參閱[單面渲染](single-sided-rendering.md)。

## <a name="pbr-material-properties"></a>PBR 材料屬性

基於物理的渲染的核心思想是使用*BaseColor、**金屬度*和*粗糙度*屬性來類比各種真實材料。 PBR 的詳細描述超出了本文的範圍。 有關 PBR 的詳細資訊,請參閱[其他來源](http://www.pbr-book.org)。 以下屬性特定於 PBR 材料:

* **基色:** 在 PBR 材料中,*色底色*稱為*底色*。 在 Azure 遠端渲染中,通過公共材質屬性已存在*色色*屬性,因此沒有其他基本顏色屬性。

* **粗糙度**和**粗糙度地圖:** 粗糙度定義表面的粗糙度或平滑度。 粗糙的表面將光以更平滑的方向散射,而不是平滑表面,這使得反射變得模糊而不是鋒利。 值範圍是從到`0.0``1.0`。 當`roughness`相等`0.0`時 ,反射將是尖銳的。 當`roughness`相等`0.5`時 ,反射將變得模糊。

  如果同時提供粗糙度值和粗糙度圖,則最終值將是兩者的產物。

* **金屬性和****金屬度Map:** 在物理學中,此屬性對應於表面是導電的還是介電的。 導電材料具有不同的反射性能,並且往往反射無反光色。 在 PBR 材質中,此屬性會影響曲面反映周圍環境的頻率。 值範圍從`0.0`到`1.0`。 當金屬性時`0.0`,二貝多色是完全可見的,材料看起來像塑膠或陶瓷。 當金屬性時`0.5`,它看起來像是漆金屬。 當金屬性時`1.0`,表面幾乎完全失去其白度顏色,只反映周圍環境。 `metalness`例如,如果`1.0`是,`roughness``0.0`則曲面看起來像真實世界的鏡像。

  如果同時提供金屬度值和金屬度貼圖,則最終值將是兩者的產品。

  ![金屬性和粗糙度](./media/metalness-roughness.png)

  在上圖中,右下角的球體看起來像真正的金屬材料,左下角看起來像陶瓷或塑膠。 色白性顏色也根據物理屬性而變化。 隨著粗糙度的增加,材料會失去反射銳度。

* **正常映射:** 要模擬細粒度細節,可以提供[法貼圖](https://en.wikipedia.org/wiki/Normal_mapping)。

* **遮擋貼圖**和**aoScale:**[環境遮擋](https://en.wikipedia.org/wiki/Ambient_occlusion)通過將陰影添加到遮擋區域,使具有縫隙的對象看起來更逼真。 `0.0`遮擋值範圍從`1.0`到`0.0`,其中 表示黑暗(遮擋`1.0`),表示 沒有遮擋。 如果 2D 紋理作為遮擋貼圖提供,則啟用效果 *,aoScale*充當乘數。

  ![遮擋貼圖](./media/boom-box-ao2.gif)

* **透明:** 對於 PBR 材質,只有一個透明度設置:是否啟用。 不一定是由色光的Alpha通道定義的。 啟用後,將調用更複雜的渲染管道來繪製半透明曲面。 Azure 遠端呈現實現真實[順序獨立透明度](https://en.wikipedia.org/wiki/Order-independent_transparency)(OIT)。

  透明幾何圖形的渲染成本很高。 如果只需要曲面上的孔,例如樹的葉子,最好改用 Alpha 裁剪。

  ![透明度](./media/transparency.png)通知在上面的圖像中,最右邊的球體是如何完全透明的,但反射仍然可見。

  > [!IMPORTANT]
  > 如果任何材質在執行時應從不透明切換到透明,則渲染器必須使用*基於 Tile 的合成*[呈現模式](../../concepts/rendering-modes.md)。 此限制不適用於首先轉換為透明材料的材料。

## <a name="technical-details"></a>技術詳細資訊

Azure 遠端渲染使用帶有 GGX NDF 的庫克-托倫斯微面 BRDF、Schlick Fresnel 和 GGX Smith 相關可見性術語,帶有蘭伯特漫反射術語。 這種模式是目前事實上的行業標準。 有關更深入的詳細資訊,請參閱本文:[基於物理的渲染 - 烹飪托倫斯](http://www.codinglabs.net/article_physically_based_rendering_cook_torrance.aspx)

 Azure 遠端渲染中使用的*金屬粗糙度*PBR 模型的替代是*鏡面光澤*PBR 模型。 此模型可以代表更廣泛的材料範圍。 但是,它更昂貴,通常不適用於即時案例。
並不總是可以轉換從*鏡面光澤*度到*金屬粗糙度*,因為有 *(漫反射,鏡面)* 值對不能轉換為 *(基礎顏色,金屬度)。* 其他方向的轉換更簡單、更精確,因為所有 *(BaseColor、金屬度)* 對都對應於定義良好的 *(漫反射、鏡面)* 對。

## <a name="next-steps"></a>後續步驟

* [色彩材質](color-materials.md)
* [紋理](../../concepts/textures.md)
* [格](../../concepts/meshes.md)
