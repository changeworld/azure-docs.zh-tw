---
title: 天空反射
description: 說明如何設定天空反射的環境對應
author: florianborn71
ms.author: flborn
ms.date: 02/07/2020
ms.topic: article
ms.openlocfilehash: be3dc2b113cb21c2dfb54a29e7f426e0d925c6d9
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83759110"
---
# <a name="sky-reflections"></a>天空反射

在 Azure 遠端轉譯中，會使用天空紋理來實際照亮物件。 針對擴增實境應用程式，此紋理應類似於您的真實世界周圍，讓物件看起來更有說服力。 本文說明如何變更天空紋理。

> [!NOTE]
> 天空紋理也稱為「環境對應」。 這些詞彙可交換使用。

## <a name="object-lighting"></a>物件光源

Azure 遠端轉譯會採用「以物理為基礎的轉譯」(PBR) 來進行實際的光源計算。 雖然您可以將[光源](lights.md)新增至場景，但是使用良好的天空紋理會有最佳的影響。

下列影像顯示僅使用天空紋理時，不同表面的光源結果：

| 粗糙度  | 0                                        | 0.25                                          | 0.5                                          | 0.75                                          | 1                                          |
|:----------:|:----------------------------------------:|:---------------------------------------------:|:--------------------------------------------:|:---------------------------------------------:|:------------------------------------------:|
| 非金屬  | ![Dielectric0](media/dielectric-0.png)   | ![GreenPointPark](media/dielectric-0.25.png)  | ![GreenPointPark](media/dielectric-0.5.png)  | ![GreenPointPark](media/dielectric-0.75.png)  | ![GreenPointPark](media/dielectric-1.png)  |
| 金屬      | ![GreenPointPark](media/metallic-0.png)  | ![GreenPointPark](media/metallic-0.25.png)    | ![GreenPointPark](media/metallic-0.5.png)    | ![GreenPointPark](media/metallic-0.75.png)    | ![GreenPointPark](media/metallic-1.png)    |

如需光源模型的詳細資訊，請參閱[材質](../../concepts/materials.md)章節。

> [!IMPORTANT]
> Azure 遠端轉譯僅針對光源模型使用天空紋理。 不會將天空轉譯為背景，因為擴增實境應用程式已經有適當的背景，也就是真實世界。

## <a name="changing-the-sky-texture"></a>變更天空紋理

若要變更環境對應，您只需要[載入紋理](../../concepts/textures.md)並且變更工作階段的 `SkyReflectionSettings`：

```cs
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

```cpp
void ChangeEnvironmentMap(ApiHandle<AzureSession> session)
{
    LoadTextureFromSASParams params;
    params.TextureType = TextureType::CubeMap;
    params.TextureUrl = "builtin://VeniceSunset";
    ApiHandle<LoadTextureAsync> skyTextureLoad = *session->Actions()->LoadTextureFromSASAsync(params);

    skyTextureLoad->Completed([&](ApiHandle<LoadTextureAsync> res)
    {
        if (res->IsRanToCompletion())
        {
            ApiHandle<SkyReflectionSettings> settings = *session->Actions()->SkyReflectionSettings();
            settings->SkyReflectionTexture(*res->Result());
        }
        else
        {
            printf("Texture loading failed!");
        }
    });
}

```

請注意，上面使用 `LoadTextureFromSASAsync` 變體，因為已載入內建紋理。 如果是從[連結的 Blob 儲存體](../../how-tos/create-an-account.md#link-storage-accounts)載入，請使用 `LoadTextureAsync` 變體。

## <a name="sky-texture-types"></a>天空紋理類型

您可以同時使用[立方體貼圖](https://en.wikipedia.org/wiki/Cube_mapping)和「2D 紋理」作為環境對應。

所有紋理都必須是[支援的紋理格式](../../concepts/textures.md#supported-texture-formats)。 您不需要為天空紋理提供 mipmap。

### <a name="cube-environment-maps"></a>Cube 環境對應

如需參考，以下是未包裝的立方體貼圖：

![未包裝的立方體貼圖](media/Cubemap-example.png)

使用 `AzureSession.Actions.LoadTextureAsync`/ `LoadTextureFromSASAsync` 與 `TextureType.CubeMap` 來載入立方體貼圖紋理。

### <a name="sphere-environment-maps"></a>球體環境對應

使用 2D 紋理作為環境對應時，影像必須在[球面座標空間](https://en.wikipedia.org/wiki/Spherical_coordinate_system)中。

![球面座標中的天空影像](media/spheremap-example.png)

使用具有 `TextureType.Texture2D` 的 `AzureSession.Actions.LoadTextureAsync` 來載入球面環境對應。

## <a name="built-in-environment-maps"></a>內建環境對應

Azure 遠端轉譯提供一些永遠可供使用的內建環境對應。 所有內建的環境對應都是立方體貼圖。

|識別碼                         | 描述                                              | 圖例                                                      |
|-----------------------------------|:---------------------------------------------------------|:-----------------------------------------------------------------:|
|builtin://Autoshop                 | 各種帶狀光源，明亮的室內基本光源    | ![Autoshop](media/autoshop.png)
|builtin://BoilerRoom               | 明亮的室內光源設定，多個窗戶光線      | ![BoilerRoom](media/boiler-room.png)
|builtin://ColorfulStudio           | 中等光源室內設定的不同色彩光線  | ![ColorfulStudio](media/colorful-studio.png)
|builtin://Hangar                   | 適度明亮的環境大廳光線                     | ![SmallHangar](media/hangar.png)
|builtin://IndustrialPipeAndValve   | 具有深淺對比度的黯淡室內設定              | ![IndustrialPipeAndValve](media/industrial-pipe-and-valve.png)
|builtin://Lebombo                  | 日間環境房間光線，明亮窗戶區域光線     | ![Lebombo](media/lebombo.png)
|builtin://SataraNight              | 具有許多周圍光線的黑夜天空和地面   | ![SataraNight](media/satara-night.png)
|builtin://SunnyVondelpark          | 明亮陽光與陰影對比                      | ![SunnyVondelpark](media/sunny-vondelpark.png)
|builtin://Syferfontein             | 中度地面光源的清澈天空光線            | ![Syferfontein](media/syferfontein.png)
|builtin://TearsOfSteelBridge       | 適度的變化陽光和陰影                         | ![TearsOfSteelBridge](media/tears-of-steel-bridge.png)
|builtin://VeniceSunset             | 接近黃昏的夜間日落光線                    | ![VeniceSunset](media/venice-sunset.png)
|builtin://WhippleCreekRegionalPark | 明亮、鬱鬱蔥蔥綠色和白色色調，黯淡的地面 | ![WhippleCreekRegionalPark](media/whipple-creek-regional-park.png)
|builtin://WinterRiver              | 具有明亮環境地面光線的日間                 | ![WinterRiver](media/winter-river.png)
|builtin://DefaultSky               | 與 TearsOfSteelBridge 相同                               | ![DefaultSky](media/tears-of-steel-bridge.png)

## <a name="next-steps"></a>後續步驟

* [光線](../../overview/features/lights.md)
* [材質](../../concepts/materials.md)
* [紋理](../../concepts/textures.md)
* [TexConv 命令列工具](../../resources/tools/tex-conv.md)
