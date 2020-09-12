---
title: 場景光源
description: 輕量來源描述和屬性
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: 2ddce7e2f073914e9393eabf12e8b0f4ef347828
ms.sourcegitcommit: f845ca2f4b626ef9db73b88ca71279ac80538559
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89613654"
---
# <a name="scene-lighting"></a>場景光源

根據預設，遠端轉譯的物件會使用 [天空燈](sky.md)亮著。 對於大部分的應用程式而言，這已經足夠，但您可以在場景中新增更多的燈光來源。

> [!IMPORTANT]
> 只有 [.pbr 材質](pbr-materials.md) 會受到光源的影響。 [色彩材質](color-materials.md) 永遠看起來完全亮。

> [!NOTE]
> 目前不支援轉換遮蔽。 Azure 遠端轉譯已優化，可在必要時利用多個 Gpu 來呈現大量幾何。 在這類情況下，陰影轉換的傳統方法無法正常運作。

## <a name="common-light-component-properties"></a>一般的燈光元件屬性

所有光源類型都衍生自抽象基類 `LightComponent` ，並共用這些屬性：

* **色彩：**[Gamma 空間](https://en.wikipedia.org/wiki/SRGB)中的淺色色彩。 已忽略 Alpha。

* **濃度：** 光線的亮度。 若為點和點燈，濃度也會定義光線效果的程度。

## <a name="point-light"></a>點燈

在 Azure 遠端轉譯中，不僅 `PointLightComponent` 能從單一點發出燈光，也可以從小球體或小型管，來模擬較輕量的燈光來源。

### <a name="pointlightcomponent-properties"></a>PointLightComponent 屬性

* **Radius：** 預設半徑為零，在這種情況下，燈光會作為點燈。 如果半徑大於零，則會作為球形燈來源，以變更高光的外觀。

* **長度：** 如果 `Length` 和 `Radius` 都不是零，則光線會作為電子管光線。 這可以用來模擬霓虹燈電子管。

* **AttenuationCutoff：** 如果從左到 (0，0) 光的衰減只取決於其 `Intensity` 。 不過，您可以提供自訂的最小/最大距離，讓光線的濃度以線性方式向下調整為0。 這項功能可以用來強制較小範圍的特定光線影響。

* **ProjectedCubemap：** 如果設定為有效的 [立方體貼圖](../../concepts/textures.md)，則會將材質投射至光線周圍幾何的周圍。 立方體貼圖的色彩會以光源的色彩進行調製。

## <a name="spot-light"></a>點燈

`SpotLightComponent`類似于， `PointLightComponent` 但燈光會限制為錐形的形狀。 錐形的方向是由 *擁有者實體的負 Z 軸*所定義。

### <a name="spotlightcomponent-properties"></a>SpotLightComponent 屬性

* **Radius：** 與相同 `PointLightComponent` 。

* **SpotAngleDeg：** 此間隔會定義錐形的內部和外部角度（以度為單位）。 內部角度內的所有專案都會以完整的亮度來發亮。 會將衰減套用至產生類似 penumbra 效果的外部角度。

* **FalloffExponent：** 定義在內部和外部錐形角度之間的衰減轉換程度。 較高的值會產生更清晰的轉換。 1.0 的預設值會產生線性轉換。

* **AttenuationCutoff：** 與相同 `PointLightComponent` 。

* **Projected2dTexture：** 如果設定為有效的 [2d 材質](../../concepts/textures.md)，影像就會投射在光線的幾何上。 材質的色彩會以淺色的色彩進行調製。

## <a name="directional-light"></a>定向光線

`DirectionalLightComponent`會模擬無限遠的燈光來源。 光是 *擁有者實體之負 Z 軸*的方向。 忽略實體的位置。

沒有其他屬性。

## <a name="performance-considerations"></a>效能考量

輕量來源對轉譯效能有顯著的影響。 請小心使用，並只在應用程式需要時使用。 任何靜態全域光源條件（包括靜態方向元件）都可以使用 [自訂天空紋理](sky.md)來達成，而不需要額外的轉譯成本。

## <a name="api-documentation"></a>API 文件

* [C # LightComponentBase 類別](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.lightcomponentbase)
* [C # PointLightComponent 類別](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.pointlightcomponent)
* [C # SpotLightComponent 類別](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.spotlightcomponent)
* [C # DirectionalLightComponent 類別](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.directionallightcomponent)
* [C + + LightComponentBase 類別](https://docs.microsoft.com/cpp/api/remote-rendering/lightcomponentbase)
* [C + + PointLightComponent 類別](https://docs.microsoft.com/cpp/api/remote-rendering/pointlightcomponent)
* [C + + SpotLightComponent 類別](https://docs.microsoft.com/cpp/api/remote-rendering/spotlightcomponent)
* [C + + DirectionalLightComponent 類別](https://docs.microsoft.com/cpp/api/remote-rendering/directionallightcomponent)

## <a name="next-steps"></a>後續步驟

* [材質](../../concepts/materials.md)
* [天空](sky.md)
