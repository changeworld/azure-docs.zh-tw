---
title: 天空反射
description: 說明如何設定天空反射的環境對應
author: florianborn71
ms.author: flborn
ms.date: 02/07/2020
ms.topic: article
ms.openlocfilehash: 7316df7bcf78e3a154510e69116c288b2b293d4c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80680605"
---
# <a name="sky-reflections"></a>天空反射

在 Azure 遠端轉譯中，會使用天空材質來實際地淺物件。 針對增強的現實應用程式，此材質應類似于您的真實世界周圍，讓物件看起來更有說服力。 本文說明如何變更天空材質。

> [!NOTE]
> 天空材質也稱為「*環境對應*」。 這些詞彙可交換使用。

## <a name="object-lighting"></a>物件光源

Azure 遠端呈現採用*實際的呈現*（.pbr）來進行實際的光源計算。 雖然您可以將[光源](lights.md)新增至場景，但使用良好的天空材質會產生最大的影響。

下列影像顯示光源的結果，而不是使用天空材質的不同表面：

| 粗糙度  | 0                                        | 0.25                                          | 0.5                                          | 0.75                                          | 1                                          |
|:----------:|:----------------------------------------:|:---------------------------------------------:|:--------------------------------------------:|:---------------------------------------------:|:------------------------------------------:|
| 非金屬  | ![Dielectric0](media/dielectric-0.png)   | ![GreenPointPark](media/dielectric-0.25.png)  | ![GreenPointPark](media/dielectric-0.5.png)  | ![GreenPointPark](media/dielectric-0.75.png)  | ![GreenPointPark](media/dielectric-1.png)  |
| Metal      | ![GreenPointPark](media/metallic-0.png)  | ![GreenPointPark](media/metallic-0.25.png)    | ![GreenPointPark](media/metallic-0.5.png)    | ![GreenPointPark](media/metallic-0.75.png)    | ![GreenPointPark](media/metallic-1.png)    |

如需光源模型的詳細資訊，請參閱[材質](../../concepts/materials.md)一章。

> [!IMPORTANT]
> Azure 遠端呈現僅針對光源模型使用天空材質。 它不會將天空轉譯為背景，因為已增強的現實應用程式已經有適當的背景，也就是真實世界。

## <a name="changing-the-sky-texture"></a>變更天空材質

若要變更環境對應，您只需要[載入材質](../../concepts/textures.md)並變更會話的`SkyReflectionSettings`：

``` cs
LoadTextureAsync _skyTextureLoad = null;
void ChangeEnvironmentMap(AzureSession session)
{
    _skyTextureLoad = session.Actions.LoadTextureFromSASAsync(new LoadTextureFromSASParams("builtin://VeniceSunset", TextureType.CubeMap));

    _skyTextureLoad.Completed += (LoadTextureAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                try
                {
                    session.Actions.SkyReflectionSettings.SkyReflectionTexture = res.Result;
                }
                catch (RRException exception)
                {
                    System.Console.WriteLine($"Setting sky reflection failed: {exception.Message}");
                }
            }
            else
            {
                System.Console.WriteLine("Texture loading failed!");
            }
        };
}
```

請注意， `LoadTextureFromSASAsync`因為已載入內建材質，所以上面會使用 variant。 如果是從[連結的 Blob 儲存體](../../how-tos/create-an-account.md#link-storage-accounts)載入，請使用 `LoadTextureAsync` 變體。

## <a name="sky-texture-types"></a>天空材質類型

您可以同時使用*[cubemaps](https://en.wikipedia.org/wiki/Cube_mapping)* 和*2d 材質*做為環境對應。

所有材質都必須使用支援的[材質格式](../../concepts/textures.md#supported-texture-formats)。 您不需要為天空材質提供 mipmap。

### <a name="cube-environment-maps"></a>Cube 環境對應

如需參考，以下是未包裝的立方體貼圖：

![未包裝的立方體貼圖](media/Cubemap-example.png)

`AzureSession.Actions.LoadTextureAsync` / 使用`LoadTextureFromSASAsync`搭配`TextureType.CubeMap`來載入立方體貼圖紋理。

### <a name="sphere-environment-maps"></a>球體環境對應

使用2D 材質做為環境對應時，影像必須在[球面座標空間](https://en.wikipedia.org/wiki/Spherical_coordinate_system)中。

![球形座標中的天空影像](media/spheremap-example.png)

使用`AzureSession.Actions.LoadTextureAsync`搭配`TextureType.Texture2D`來載入球面環境對應。

## <a name="built-in-environment-maps"></a>內建的環境對應

Azure 遠端轉譯提供一些可隨時使用的內建環境對應。 所有內建的環境對應都是 cubemaps。

|識別碼                         | 描述                                              | 圖例                                                      |
|-----------------------------------|:---------------------------------------------------------|:-----------------------------------------------------------------:|
|builtin://Autoshop                 | 各種 stripe 燈，明亮的室內基本光源    | ![Autoshop](media/autoshop.png)
|builtin://BoilerRoom               | 明亮的室內光源設定，多個視窗燈      | ![BoilerRoom](media/boiler-room.png)
|builtin://ColorfulStudio           | Varyingly 中等光線室內設定的彩色燈  | ![ColorfulStudio](media/colorful-studio.png)
|builtin://Hangar                   | 適度明亮的環境大廳光線                     | ![SmallHangar](media/hangar.png)
|builtin://IndustrialPipeAndValve   | 具有淺暗對比度的立體室內設定              | ![IndustrialPipeAndValve](media/industrial-pipe-and-valve.png)
|builtin://Lebombo                  | 日間環境室光線，明亮的視窗區域光線     | ![Lebombo](media/lebombo.png)
|builtin://SataraNight              | 使用多個周圍光源來進行深夜間的天空和地面   | ![SataraNight](media/satara-night.png)
|builtin://SunnyVondelpark          | 明亮的陽光與陰影對比                      | ![SunnyVondelpark](media/sunny-vondelpark.png)
|builtin://Syferfontein             | 以中等地面光源清除天空            | ![Syferfontein](media/syferfontein.png)
|builtin://TearsOfSteelBridge       | 適度改變太陽和網底                         | ![TearsOfSteelBridge](media/tears-of-steel-bridge.png)
|builtin://VeniceSunset             | 夜間的日落燈接近時分                    | ![VeniceSunset](media/venice-sunset.png)
|builtin://WhippleCreekRegionalPark | 明亮、lush-綠色和白色燈，暗色調 | ![WhippleCreekRegionalPark](media/whipple-creek-regional-park.png)
|builtin://WinterRiver              | 具有明亮的環境地面的日間                 | ![WinterRiver](media/winter-river.png)
|builtin://DefaultSky               | 與 TearsOfSteelBridge 相同                               | ![DefaultSky](media/tears-of-steel-bridge.png)

## <a name="next-steps"></a>後續步驟

* [光線](../../overview/features/lights.md)
* [材質](../../concepts/materials.md)
* [紋理](../../concepts/textures.md)
* [TexConv 命令列工具](../../resources/tools/tex-conv.md)
