---
title: PBR 材質
description: 描述 .PBR 材質類型。
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: e4ee6abe7481fef4d56c980da80e319624975384
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84021308"
---
# <a name="pbr-materials"></a>PBR 材質

*.Pbr 材質*是 Azure 遠端呈現中支援的其中一種資料[類型](../../concepts/materials.md)。 它們會用於應該會收到實際光源的[網格](../../concepts/meshes.md)。

.PBR 代表**P**hysically **B**機型 gateway **R**endering，表示材質會以實體合理情況的方式描述介面的視覺屬性，因此在所有光源條件下可能會有實際的結果。 大部分的新式遊戲引擎和內容建立工具都支援 .PBR 資料，因為它們被視為即時轉譯的真實世界案例的最佳近似值。

![ARR 所轉譯的 Helmet glTF 範例模型](media/helmet.png)

不過，.PBR 材料並不是通用的解決方案。 有一些資料會以不同的角度來反映色彩。 例如，某些網狀架構或汽車會繪製。 標準的 .PBR 模型不會處理這些類型的資料，Azure 遠端轉譯目前不支援這類資料。 這包括 .PBR 延伸模組，例如*細電影*（多層式表面）和*Coat* （適用于汽車繪製）。

## <a name="common-material-properties"></a>一般材質屬性

這些屬性通用於所有材質：

* **albedoColor：** 此色彩會乘以其他色彩，例如*albedoMap*或* :::no-loc text="vertex "::: 色彩*。 如果已啟用材質的*透明度*，Alpha 色板會用來調整不透明度，其 `1` 意義完全不透明且 `0` 意義完全透明。 預設為白色。

  > [!NOTE]
  > 當 .PBR 材質完全透明時，像是完美的半透明部分，它仍會反映環境。 像 sun 之類的明亮點仍會顯示在反映中。 這對[色彩材質](color-materials.md)而言是不同的。

* **albedoMap：** 每圖元 albedo 值的[2d 材質](../../concepts/textures.md)。

* **AlphaClipEnabled**和**AlphaClipThreshold：** 如果*AlphaClipEnabled*為 true，則不會繪製 albedo Alpha 值低於*AlphaClipThreshold*的所有圖元。 即使沒有啟用透明度，也可以使用 Alpha 裁剪，而且呈現速度會更快。 不過，Alpha 裁剪材質的轉譯速度仍然會比完全不透明的材質更慢。 預設會停用 Alpha 裁剪。

* **textureCoordinateScale**和**textureCoordinateOffset：** 尺規會乘以 UV 材質座標，而位移會加入其中。 可以用來延展和轉換材質。 預設尺規為（1，1），而 offset 為（0，0）。

* **useVertexColor：** 如果網格包含 :::no-loc text="vertex"::: 色彩，而且已啟用此選項，則網格 :::no-loc text="vertex"::: 色彩會乘以*albedoColor*和*albedoMap*。 預設會停用*useVertexColor* 。

* **isDoubleSided：** 如果 sidedness 設定為 true，則會轉譯具有此材質的三角形，即使相機正在查看其背景表面也一樣。 對於後端臉部也會正確計算的 .PBR 資料光源。 預設會停用此選項。 另請參閱[ :::no-loc text="Single-sided"::: 呈現](single-sided-rendering.md)。

## <a name="pbr-material-properties"></a>.PBR 材質屬性

實體式轉譯的核心概念是，使用*BaseColor*、 *Metalness*和*粗糙度*屬性來模擬各式各樣的真實世界素材。 有關 .PBR 的詳細說明已超出本文的範圍。 如需有關 .PBR 的詳細資訊，請參閱[其他來源](http://www.pbr-book.org)。 下列屬性是針對 .PBR 資料所特有：

* **baseColor：** 在 .PBR 材質中， *albedo 色彩*稱為「*基底色彩*」。 在 Azure 遠端轉譯中， *albedo color*屬性已經透過一般材質屬性呈現，因此沒有其他的基本色彩屬性。

* **粗糙度**和**roughnessMap：** 粗糙度會定義表面的粗略或平滑程度。 粗略的表面散佈光線的方向遠高於平滑表面，使反射變得模糊，而不是清晰。 值範圍是從 `0.0` 到 `1.0` 。 當 `roughness` 等於時 `0.0` ，反射會很銳利。 當 `roughness` 等於時 `0.5` ，反射會變得模糊。

  如果同時提供了粗糙度值和粗糙度對應，最後的值會是兩者的乘積。

* **metalness**和**metalnessMap：** 在物理中，此屬性會對應至介面為導電或 dielectric。 「導電材質」具有不同的反射屬性，而且通常會反映不 albedo 的色彩。 在 .PBR 材質中，此屬性會影響表面反映周圍環境的程度。 值的範圍從 `0.0` 到 `1.0` 。 當 metalness 為時 `0.0` ，albedo 色彩會完全可見，而且材質看起來像是塑膠或 ceramics。 當 metalness 為時 `0.5` ，它看起來像是 [繪製金屬]。 當 metalness 為時 `1.0` ，表面幾乎完全失去其 albedo 色彩，而且只會反映周圍的環境。 例如，如果 `metalness` 是 `1.0` ，而 `roughness` 是，則 `0.0` 表面看起來就像真實世界的鏡像。

  如果同時提供 metalness 值和 metalness 對應，最後的值會是兩者的乘積。

  ![metalness 和粗糙度](./media/metalness-roughness.png)

  在上圖中，右下角的球面看起來像是一個真實的金屬材料，左下方看起來像 ceramic 或塑膠。 Albedo 色彩也會根據實體屬性而變更。 隨著粗糙度的增加，材質會失去反映清晰度。

* **normalMap：** 若要模擬精細的詳細資料，可以提供[一般對應](https://en.wikipedia.org/wiki/Normal_mapping)。

* **occlusionMap**和**aoScale：** [環境遮蔽](https://en.wikipedia.org/wiki/Ambient_occlusion)藉由將陰影新增至 pixels occluded 區域，讓具有 crevices 的物件看起來更逼真。 遮蔽值的範圍從 `0.0` 到 `1.0` ，其中 `0.0` 表示暗度（pixels occluded）， `1.0` 表示沒有遮蔽。 如果將2D 材質當做遮蔽地圖提供，就會啟用效果，而*aoScale*會作為乘數。

  ![遮蔽地圖](./media/boom-box-ao2.gif)

* **透明：** 若是 .PBR 材質，只有一個透明度設定：已啟用或不是。 不透明度是由 albedo 色彩的 Alpha 色板所定義。 啟用時，會叫用較複雜的呈現管線，以繪製半透明表面。 Azure 遠端呈現會執行真正的[順序獨立透明度](https://en.wikipedia.org/wiki/Order-independent_transparency)（OIT）。

  透明幾何的呈現成本很高。 如果您只需要介面中的洞（例如，針對樹狀結構的葉），最好改用 Alpha 裁剪。

  ![](./media/transparency.png)上圖中的透明度注意事項、最右邊的球體如何完全透明，但反映仍然可見。

  > [!IMPORTANT]
  > 如果在執行時間應該將任何材質從不透明切換為透明，轉譯器就必須使用*TileBasedComposition*轉譯[模式](../../concepts/rendering-modes.md)。 這項限制不適用於轉換為透明材質一開始的材質。

## <a name="technical-details"></a>技術詳細資訊

Azure 遠端轉譯會使用 Torrance 的微 facet BRDF 搭配 GGX NDF、Schlick Fresnel 和 GGX Smith 相互關聯的可見度詞彙與 Lambert 擴散詞彙。 這個模型是目前事實上的業界標準。 如需更深入的詳細資料，請參閱這篇文章：以實體為[基礎的轉譯-Torrance](http://www.codinglabs.net/article_physically_based_rendering_cook_torrance.aspx)

 在 Azure 遠端轉譯中使用的*Metalness-粗糙度*.pbr 模型的替代方式，是*反射-光澤*的 .pbr 模型。 此模型可以代表更廣泛的素材。 不過，它的成本較高，而且通常不適合用于即時案例。
不一定可以從*反射-光澤*轉換成*Metalness-粗糙度*，因為有 *（擴散，鏡面）* 值組無法轉換為 *（BaseColor，Metalness）*。 另一個方向的轉換比較簡單且更精確，因為 all *（BaseColor，Metalness）* 配對會對應到妥善定義的 *（擴散、反射）* 配對。

## <a name="next-steps"></a>後續步驟

* [色彩材質](color-materials.md)
* [紋理](../../concepts/textures.md)
* [網格](../../concepts/meshes.md)
