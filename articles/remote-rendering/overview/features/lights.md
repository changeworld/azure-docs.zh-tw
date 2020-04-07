---
title: 車燈
description: 光源描述與屬性
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: 0a4a226af1347b5302b0c3964889fc072f89e7f8
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680943"
---
# <a name="lights"></a>車燈

默認情況下,遠端渲染的物件使用[天光](sky.md)點亮。 對於大多數應用程式,這已經足夠了,但您可以向場景添加更多光源。

> [!IMPORTANT]
> 只有[PBR 材料](pbr-materials.md)受光源影響。 [顏色材料](color-materials.md)總是顯得完全明亮。

> [!NOTE]
> 當前不支援投射陰影。 Azure 遠端呈現經過優化,可根據需要使用多個 GPU 來呈現大量幾何體。 在這種情形下,傳統的陰影投射方法效果不好。

## <a name="common-light-component-properties"></a>常見的光元件屬性

所有光類型都派生自抽象基類`LightComponent`並共用這些屬性:

* **顏色:**[伽瑪空間](https://en.wikipedia.org/wiki/SRGB)中光的顏色。 阿爾法將被忽略。

* **強度:** 光的亮度。 對於點燈和聚光燈,強度也定義光線的照射程度。

## <a name="point-light"></a>點燈

在 Azure`PointLightComponent`遠端 渲染中,不僅可以從單個點發出光,還可以從小球體或小管中發出光,以類比較柔和的光源。

### <a name="pointlightcomponent-properties"></a>點光元件屬性

* **半徑:** 默認半徑為零,在這種情況下,燈光充當點光。 如果半徑大於零,則充當球形光源,從而更改鏡面高光的外觀。

* **長度:** 如果兩`Length``Radius`者 均為非零,則光充當管燈。 這可用於類比霓虹燈管。

* **衰減截止:** 如果留給 (0,0) 則光的衰減僅`Intensity`取決於其 。 但是,您可以提供自定義的最小/最大距離,光的強度會線性縮小到 0。 此功能可用於強制特定光的影響範圍較小。

* **投影立方體圖:** 如果設置為有效的[立方體貼圖](../../concepts/textures.md),則紋理將投影到燈光的周圍幾何體上。 立方體貼圖的顏色使用燈光的顏色進行調製。

## <a name="spot-light"></a>聚光燈

`SpotLightComponent`與類似,`PointLightComponent`但光線被約束到圓錐體的形狀。 圓錐的方向由*所有者實體的負 z 軸*定義。

### <a name="spotlightcomponent-properties"></a>聚光燈元件屬性

* **半徑:** 與相同`PointLightComponent`。

* **點角:** 此間隔定義錐體的內角和外角,以度為單位進行測量。 內角內的所有內容都以全亮度亮起。 衰落應用於產生五邊形效果的外角。

* **衰降指數:** 定義內錐角和外錐角之間的衰落過渡的劇烈程度。 值越高,轉換越清晰。 默認值 1.0 會導致線性轉換。

* **衰減截止:** 與相同`PointLightComponent`。

* **投影2d紋理:** 如果設置為有效的[2D 紋理](../../concepts/textures.md),則圖像將投影到光線照射的幾何體上。 紋理的顏色使用燈光的顏色進行調製。

## <a name="directional-light"></a>定向光

類`DirectionalLightComponent`比 無限遠的光源。 光線照射到*所有者實體的負 z 軸*的方向。 實體的位置將被忽略。

沒有其他屬性。

## <a name="performance-considerations"></a>效能考量

光源對渲染性能有顯著影響。 僅在應用程式需要時才謹慎使用它們。 任何靜態全域照明條件(包括靜態方向元件)都可以通過[自定義天空紋理](sky.md)來實現,無需額外的渲染成本。

## <a name="next-steps"></a>後續步驟

* [物料](../../concepts/materials.md)
* [天空](sky.md)
