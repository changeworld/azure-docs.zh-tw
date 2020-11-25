---
title: 色彩材質
description: 描述色彩材質類型。
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 26ac1714330bba06c01d33b47105f04c600c7729
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96024090"
---
# <a name="color-materials"></a>色彩材質

*色彩材質* 是 Azure 遠端轉譯中的其中一種支援的 [材質類型](../../concepts/materials.md) 。 它們用於不應該接收任何光源的 [網格](../../concepts/meshes.md) ，而是在所有時間都是完全的亮度。 這可能是「光亮」材質（例如汽車儀表板、light 燈泡或已合併靜態光源的資料，例如透過 [攝影測量](https://en.wikipedia.org/wiki/Photogrammetry)取得的模型）的情況。

色彩材質比 [.pbr 材質](pbr-materials.md) 更有效率，因為它們較簡單的陰影模型。 它們也支援不同的透明度模式。

## <a name="common-material-properties"></a>一般材質屬性

這些屬性通用於所有材質：

* **albedoColor：** 此色彩會乘以其他色彩，例如 *albedoMap* 或 *:::no-loc text="vertex"::: 色彩*。 如果在材質上啟用 *透明度* ，則會使用 Alpha 色板來調整不透明度，其 `1` 意義完全不透明，而且 `0` 意義完全透明。 預設值為白色。

  > [!NOTE]
  > 由於色材質不會反映環境，因此完全透明的色彩材質會變成不可見。 這與 [.pbr 材質](pbr-materials.md)不同。

* **albedoMap：** 每圖元 >albedo 值的 [2d 材質](../../concepts/textures.md) 。

* **AlphaClipEnabled** 和 **AlphaClipThreshold：** 如果 *AlphaClipEnabled* 為 true，則不會繪製 >albedo Alpha 值低於 *AlphaClipThreshold* 的所有圖元。 即使沒有啟用透明度，也可以使用 Alpha 裁剪，而且呈現速度會更快。 不過，Alpha 剪下的材質仍會比完全不透明的材質更慢呈現。 預設會停用 Alpha 剪輯。

* **textureCoordinateScale** 和 **textureCoordinateOffset：** 尺規會乘以 UV 材質座標，並將位移加入其中。 可以用來延展和移出紋理。 預設小數位數為 (1，1) ，位移為 (0，0) 。

* **useVertexColor：** 如果網格包含 :::no-loc text="vertex"::: 色彩，且已啟用此選項，網格的 :::no-loc text="vertex"::: 色彩會乘以 *albedoColor* 和 *albedoMap*。 預設會停用 *useVertexColor* 。

* **isDoubleSided：** 如果雙 sidedness 是設定為 true，則即使攝影機正在查看其背面的臉部，也會轉譯具有此材質的三角形。 預設會停用此選項。 另請參閱[ :::no-loc text="Single-sided"::: 呈現](single-sided-rendering.md)。

* **TransparencyWritesDepth：** 如果在材質上設定 TransparencyWritesDepth 旗標，且材質是透明的，使用此材質的物件也會對最後的深度緩衝區造成影響。 請參閱下一節中的色彩材質屬性 *transparencyMode* 。 如果您的使用案例需要更合理的最高 [階段 reprojection](late-stage-reprojection.md) 完全透明的場景，建議啟用這項功能。 若為混合的不透明/透明場景，此設定可能會導致 implausible reprojection 行為或 reprojection 成品。 基於這個理由，一般使用案例的預設和建議的設定是停用此旗標。 寫入的深度值取自最接近相機之物件的每個圖元深度層級。

* **FresnelEffect：** 此材質旗標會對個別的資料啟用加法 [fresnel 效果](../../overview/features/fresnel-effect.md) 。 效果的外觀是由下列所述的其他 fresnel 參數所控制。 

* **FresnelEffectColor：** 用於此材質的 fresnel 色彩。 只有在此材質上已設定 fresnel 效果位時才重要 (請參閱上述) 。 這個屬性會控制 fresnel 的基本色彩 (如需完整說明) ，請參閱 [fresnel 效果](../../overview/features/fresnel-effect.md) 。 目前只有 rgb 通道值是很重要的，而且 Alpha 值會被忽略。

* **FresnelEffectExponent：** 用於此材質的 fresnel 指數。 只有在此材質上已設定 fresnel 效果位時才重要 (請參閱上述) 。 這個屬性會控制 fresnel 的照射範圍。 最小值0.01 會導致整個物件的散佈。 最大值10.0 限制只會顯示最 gracing 的邊緣。

## <a name="color-material-properties"></a>色彩材質屬性

下列是色彩材質特有的屬性：

* **vertexMix：** 和之間的這個值會 `0` `1` 指定 :::no-loc text="vertex"::: [網格](../../concepts/meshes.md) 中的色彩對最終色彩有何高度。 若為預設值1，則 :::no-loc text="vertex"::: 會將色彩完全乘以 >albedo 色彩。 若值為0，則 :::no-loc text="vertex"::: 會完全忽略色彩。

* **transparencyMode：** 相對於 [.pbr 材質](pbr-materials.md)，色彩材質會區分不同的透明度模式：

  1. 不 **透明：** 預設模式會停用透明度。 不過，您仍然可以進行 Alpha 裁剪，而且如果已足夠，則應該偏好。
  
  1. **AlphaBlended：** 這種模式類似于 .PBR 材質的透明模式。 它應該用於查看資料，例如玻璃。

  1. **加法：** 此模式是最簡單且最有效率的透明度模式。 材質的比重會新增至轉譯的影像。 這個模式可用來模擬發光的 (但仍然是透明的) 物件，例如用於反白顯示重要物件的標記。

## <a name="api-documentation"></a>API 文件

* [C # ColorMaterial 類別](/dotnet/api/microsoft.azure.remoterendering.colormaterial)
* [C # RemoteManager. CreateMaterial ( # B1 ](/dotnet/api/microsoft.azure.remoterendering.remotemanager.creatematerial)
* [C + + ColorMaterial 類別](/cpp/api/remote-rendering/colormaterial)
* [C + + RemoteManager：： CreateMaterial ( # B1 ](/cpp/api/remote-rendering/remotemanager#creatematerial)

## <a name="next-steps"></a>後續步驟

* [PBR 材質](pbr-materials.md)
* [紋理](../../concepts/textures.md)
* [網格](../../concepts/meshes.md)