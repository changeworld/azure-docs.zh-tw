---
title: PBR 材質
description: 描述 .PBR 材質類型。
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: a1fedb637bee9d98fb09d8fc3fa133b2992ce86e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89613660"
---
# <a name="pbr-materials"></a>PBR 材質

*.Pbr* 材質是 Azure 遠端轉譯中的其中一種支援的 [材質類型](../../concepts/materials.md) 。 它們是用於應該會收到實際光源的 [網格](../../concepts/meshes.md) 。

.PBR 代表 **P**hysically **B**機型 gateway **R**endering，表示材質會以實際的合理方式描述介面的視覺屬性，因此在所有光源條件下都可能有實際的結果。 大部分新式遊戲引擎和內容建立工具都支援 .PBR 材質，因為它們被視為即時轉譯的真實世界案例的最佳近似值。

![ARR 所呈現的 Helmet glTF 範例模型](media/helmet.png)

但是，.PBR 材質並非通用的解決方案。 有不同的材質會根據觀賞角度來反映色彩。 例如，某些網狀架構或汽車會繪製。 標準的 .PBR 模型不會處理這些類型的材質，Azure 遠端轉譯目前不支援這些類型的材質。 這包括了 .PBR 的延伸模組，例如 *細膠捲* (多層式表面) ，以及適用于汽車繪製) 的 *清晰 Coat* (。

## <a name="common-material-properties"></a>一般材質屬性

這些屬性通用於所有材質：

* **albedoColor：** 此色彩會乘以其他色彩，例如*albedoMap*或* :::no-loc text="vertex "::: 色彩*。 如果在材質上啟用 *透明度* ，則會使用 Alpha 色板來調整不透明度，其 `1` 意義完全不透明，而且 `0` 意義完全透明。 預設值為白色。

  > [!NOTE]
  > 當 .PBR 材質完全透明時（例如完美的半透明部分），它仍會反映環境。 視覺效果等明亮的點仍然可以在反映中看到。 這對 [色彩材質](color-materials.md)而言是不同的。

* **albedoMap：** 每圖元 >albedo 值的 [2d 材質](../../concepts/textures.md) 。

* **AlphaClipEnabled** 和 **AlphaClipThreshold：** 如果 *AlphaClipEnabled* 為 true，則不會繪製 >albedo Alpha 值低於 *AlphaClipThreshold* 的所有圖元。 即使沒有啟用透明度，也可以使用 Alpha 裁剪，而且呈現速度會更快。 不過，Alpha 剪下的材質仍會比完全不透明的材質更慢呈現。 預設會停用 Alpha 剪輯。

* **textureCoordinateScale** 和 **textureCoordinateOffset：** 尺規會乘以 UV 材質座標，並將位移加入其中。 可以用來延展和移出紋理。 預設小數位數為 (1，1) ，位移為 (0，0) 。

* **useVertexColor：** 如果網格包含 :::no-loc text="vertex"::: 色彩，且已啟用此選項，網格的 :::no-loc text="vertex"::: 色彩會乘以 *albedoColor* 和 *albedoMap*。 預設會停用 *useVertexColor* 。

* **isDoubleSided：** 如果雙 sidedness 是設定為 true，則即使攝影機正在查看其背面的臉部，也會轉譯具有此材質的三角形。 針對 .PBR 材質，也會針對背面的臉部正確地計算。 預設會停用此選項。 另請參閱[ :::no-loc text="Single-sided"::: 呈現](single-sided-rendering.md)。

## <a name="pbr-material-properties"></a>.PBR 材質屬性

實際轉譯的核心概念是使用 *BaseColor*、 *Metalness*和 *粗糙度* 屬性來模擬各種不同的真實世界材質。 如需有關 .PBR 的詳細說明，請參閱本文的討論範圍。 如需有關 .PBR 的詳細資訊，請參閱 [其他來源](http://www.pbr-book.org)。 下列是針對 .PBR 材質所特有的屬性：

* **baseColor：** 在 .PBR 資料中， *>albedo 色彩* 稱為「 *基底色彩*」。 在 Azure 遠端轉譯 *>albedo color* 屬性已透過一般材質屬性呈現，因此沒有其他基底色彩屬性。

* **粗糙度** 和 **roughnessMap：** 粗糙度定義介面的粗略或平滑程度。 粗略表面會以比平滑表面更多的方向散佈光，讓反射模糊而非清晰。 值範圍是從 `0.0` 到 `1.0` 。 當 `roughness` 等於時 `0.0` ，反射會很清晰。 當 `roughness` 等於時 `0.5` ，反映將會變得模糊。

  如果同時提供了粗糙度值和粗糙度對應，最後的值將會是這兩者的乘積。

* **metalness** 和 **metalnessMap：** 在物理中，此屬性會對應至介面為導電或 dielectric。 導電材質具有不同的反射屬性，而且通常會反映未 >albedo 的色彩。 在 .PBR 資料中，此屬性會影響介面反映周圍環境的程度。 值的範圍從 `0.0` 到 `1.0` 。 當 metalness 為時 `0.0` ，>albedo 色彩會是完全可見的，而材質看起來像塑膠或 ceramics。 當 metalness 為時 `0.5` ，它看起來就像是繪製的裸機。 當 metalness 為時 `1.0` ，表面幾乎完全失去其 >albedo 色彩，而且只會反映周圍的面。 比方說，如果 `metalness` 是，則 `1.0` `roughness` `0.0` 表面看起來像真實世界的鏡像。

  如果同時提供 metalness 值和 metalness 對應，最後的值將會是這兩者的乘積。

  ![使用不同的 metalness 和粗糙度值呈現球體](./media/metalness-roughness.png)

  在上圖中，右下角的球體看起來像是真實的金屬材質，左下角看起來像是 ceramic 或塑膠。 >albedo 色彩也會根據實體屬性進行變更。 如果有越來越多的粗糙度，材質就會失去反映的清晰度。

* **normalMap：** 若要模擬更細緻的詳細資料，可以提供 [一般地圖](https://en.wikipedia.org/wiki/Normal_mapping) 。

* **occlusionMap** 和 **aoScale：** [環境遮蔽](https://en.wikipedia.org/wiki/Ambient_occlusion) 將遮蔽加入至 pixels occluded 區域，讓具有 crevices 的物件看起來更逼真。 遮蔽值範圍從 `0.0` 到到 `1.0` ，其中 `0.0` 表示暗度 (pixels occluded) ，而 `1.0` 不是遮蔽。 如果以遮蔽地圖的形式提供2D 材質，則會啟用效果，而 *aoScale* 會作為乘數。

  ![以和不具環境遮蔽呈現的物件](./media/boom-box-ao2.gif)

* **透明：** 針對 .PBR 材質，只有一個透明設定：已啟用。 不透明度是由 >albedo 色彩的 Alpha 色板所定義。 啟用時，會叫用更複雜的轉譯管線來繪製半透明表面。 Azure 遠端轉譯 (OIT) 來實行真正的 [順序獨立透明度](https://en.wikipedia.org/wiki/Order-independent_transparency) 。

  透明幾何的呈現成本很高。 如果您只需要介面中的孔洞（例如，樹狀結構的離開），最好改為使用 Alpha 剪輯。

  ![在上圖中，以零到完全透明的提示轉譯 ](./media/transparency.png) ，最右邊的球體是完全透明的，但反映仍然可見。

  > [!IMPORTANT]
  > 如果在執行時間應該將任何材質從不透明切換為透明，則轉譯器必須使用 *TileBasedComposition*轉譯 [模式](../../concepts/rendering-modes.md)。 這項限制並不適用于轉換成透明材質的材質。

## <a name="technical-details"></a>技術詳細資訊

Azure 遠端轉譯使用 Cook-Torrance 的微 facet BRDF 搭配 GGX NDF、Schlick Fresnel，以及 GGX Smith 關聯可見度詞彙與 Lambert 擴散詞彙。 此模型是目前的產業標準。 如需更深入的詳細資料，請參閱這篇文章： [實際呈現-Torrance](http://www.codinglabs.net/article_physically_based_rendering_cook_torrance.aspx)

 Azure 遠端轉譯中所使用之 *Metalness 的非等式* .pbr 模型的替代方式，就是 *反光* 的 .pbr 模型。 此模型可以代表更廣泛的材質。 但是，它的成本較高，而且通常不適用於即時案例。
由於有* (擴散、反光) *值組無法轉換成* (BaseColor、Metalness) *，因此不一定可以從*反光*到*Metalness 的粗糙度*進行轉換。 另一個方向的轉換更簡單且更精確，因為所有 * (BaseColor、Metalness) * 組都對應至妥善定義的 * (擴散、反射) * 配對。

## <a name="api-documentation"></a>API 文件

* [C # PbrMaterial 類別](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.pbrmaterial)
* [C # RemoteManager. CreateMaterial ( # B1 ](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.remotemanager.creatematerial)
* [C + + PbrMaterial 類別](https://docs.microsoft.com/cpp/api/remote-rendering/pbrmaterial)
* [C + + RemoteManager：： CreateMaterial ( # B1 ](https://docs.microsoft.com/cpp/api/remote-rendering/remotemanager#creatematerial)

## <a name="next-steps"></a>後續步驟

* [色彩材質](color-materials.md)
* [紋理](../../concepts/textures.md)
* [網格](../../concepts/meshes.md)
