---
title: 場景光源
description: Light 來源描述和屬性
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: e33e012480c876dc5befbb93404bdb131ea9329a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84022141"
---
# <a name="scene-lighting"></a>場景光源

根據預設，遠端呈現的物件會使用[天空光源](sky.md)來發亮。 對於大部分的應用程式而言，這已經足夠，但您可以將更多光源新增到場景中。

> [!IMPORTANT]
> 只有[.pbr](pbr-materials.md)的資料會受到光源的影響。 [色彩材質](color-materials.md)的外觀一律會完全明亮。

> [!NOTE]
> 目前不支援轉換陰影。 Azure 遠端轉譯已優化，可在需要時使用多個 Gpu，以轉譯大量的幾何。 在這種情況下，陰影轉換的傳統方法並不適用。

## <a name="common-light-component-properties"></a>一般光源元件屬性

所有光源類型都衍生自抽象基類 `LightComponent` 並共用這些屬性：

* **色彩：**[Gamma 空間](https://en.wikipedia.org/wiki/SRGB)中光源的色彩。 忽略 Alpha。

* **濃度：** 光線的亮度。 對於點和點燈，濃度也會定義光線的效果。

## <a name="point-light"></a>點光源

在 Azure 遠端轉譯中， `PointLightComponent` 不能只從單一點發出光線，也不能從小球體或小型管來模擬較柔和的光源。

### <a name="pointlightcomponent-properties"></a>PointLightComponent 屬性

* **Radius：** 預設半徑為零，在此情況下，光源會當做點光線。 如果半徑大於零，它會作為球面光源，這會變更反射醒目提示的外觀。

* **長度：** 如果 `Length` 和都 `Radius` 不是零，則光源會作為電子管光線。 這可以用來模擬霓虹燈型電子管。

* **AttenuationCutoff：** 如果從左到（0，0）光源的衰減只取決於其 `Intensity` 。 不過，您可以提供自訂的最小/最大距離，光源的濃度會以線性方式調整為0。 這項功能可以用來強制執行較小範圍的特定光線影響。

* **ProjectedCubemap：** 如果設定為有效的[立方體貼圖](../../concepts/textures.md)，則會將材質投射到光源周圍的幾何。 立方體貼圖的色彩是以光源的色彩來進行調製。

## <a name="spot-light"></a>點光源

`SpotLightComponent`類似于， `PointLightComponent` 但光線受限於錐形的形狀。 錐形的方向是由*擁有者實體的負 Z 軸*所定義。

### <a name="spotlightcomponent-properties"></a>SpotLightComponent 屬性

* **Radius：** 與相同 `PointLightComponent` 。

* **SpotAngleDeg：** 這個間隔會定義圓錐的內部和外部角度，以度為單位。 內部角度的所有內容都會以完整亮度亮起。 衰減會套用到外部角度，以產生類似 penumbra 的效果。

* **FalloffExponent：** 定義在內部和外部錐形角度之間的衰減轉換程度。 較高的值會產生更銳利的轉換。 1.0 的預設值會產生線性轉換。

* **AttenuationCutoff：** 與相同 `PointLightComponent` 。

* **Projected2dTexture：** 如果設定為有效的[2d 材質](../../concepts/textures.md)，影像就會投射到光線的幾何上。 材質的色彩會以光源的色彩來進行調製。

## <a name="directional-light"></a>方向淺色

`DirectionalLightComponent`會模擬無限遠的光源。 光是*擁有者實體的負 Z 軸*方向。 會忽略實體的位置。

沒有其他屬性。

## <a name="performance-considerations"></a>效能考量

光源對轉譯效能有顯著的影響。 只有在應用程式需要時，才謹慎使用。 任何靜態全域光源條件（包括靜態方向元件）都可以透過[自訂的天空材質](sky.md)達成，而無需額外的轉譯成本。

## <a name="next-steps"></a>後續步驟

* [材質](../../concepts/materials.md)
* [天空](sky.md)
