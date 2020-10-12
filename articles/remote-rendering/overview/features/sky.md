---
title: 天空反射
description: 說明如何設定天空反射的環境對應
author: florianborn71
ms.author: flborn
ms.date: 02/07/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 39e3b41d49ad06e5dbe5164809a6743da8dedae5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89613745"
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
| 非金屬  | ![Dielectric，粗糙度 = 0](media/dielectric-0.png)   | ![Dielectric，粗糙度 = 0.25](media/dielectric-0.25.png)  | ![Dielectric，粗糙度 = 0。5](media/dielectric-0.5.png)  | ![Dielectric，粗糙度 = 0.75](media/dielectric-0.75.png)  | ![Dielectric，粗糙度 = 1](media/dielectric-1.png)  |
| 金屬      | ![金屬，粗糙度 = 0](media/metallic-0.png)  | ![金屬，粗糙度 = 0.25](media/metallic-0.25.png)    | ![金屬，粗糙度 = 0。5](media/metallic-0.5.png)    | ![金屬，粗糙度 = 0.75](media/metallic-0.75.png)    | ![金屬，粗糙度 = 1](media/metallic-1.png)    |

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
            if (res->GetIsRanToCompletion())
            {
                ApiHandle<SkyReflectionSettings> settings = session->Actions()->GetSkyReflectionSettings();
                settings->SetSkyReflectionTexture(res->GetResult());
            }
            else
            {
                printf("Texture loading failed!\n");
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
|builtin://Autoshop                 | 各種帶狀光源，明亮的室內基本光源    | ![用來照亮物件的 Autoshop skybox](media/autoshop.png)
|builtin://BoilerRoom               | 明亮的室內光源設定，多個窗戶光線      | ![用來照亮物件的 BoilerRoom skybox](media/boiler-room.png)
|builtin://ColorfulStudio           | 中等光源室內設定的不同色彩光線  | ![用來照亮物件的 ColorfulStudio skybox](media/colorful-studio.png)
|builtin://Hangar                   | 適度明亮的環境大廳光線                     | ![用來照亮物件的 SmallHangar skybox](media/hangar.png)
|builtin://IndustrialPipeAndValve   | 具有深淺對比度的黯淡室內設定              | ![用來照亮物件的 IndustrialPipeAndValve skybox](media/industrial-pipe-and-valve.png)
|builtin://Lebombo                  | 日間環境房間光線，明亮窗戶區域光線     | ![用來照亮物件的 Lebombo skybox](media/lebombo.png)
|builtin://SataraNight              | 具有許多周圍光線的黑夜天空和地面   | ![用來照亮物件的 SataraNight skybox](media/satara-night.png)
|builtin://SunnyVondelpark          | 明亮陽光與陰影對比                      | ![用來照亮物件的 SunnyVondelpark skybox](media/sunny-vondelpark.png)
|builtin://Syferfontein             | 中度地面光源的清澈天空光線            | ![用來照亮物件的 Syferfontein skybox](media/syferfontein.png)
|builtin://TearsOfSteelBridge       | 適度的變化陽光和陰影                         | ![用來照亮物件的 TearsOfSteelBridge skybox](media/tears-of-steel-bridge.png)
|builtin://VeniceSunset             | 接近黃昏的夜間日落光線                    | ![用來照亮物件的 VeniceSunset skybox](media/venice-sunset.png)
|builtin://WhippleCreekRegionalPark | 明亮、鬱鬱蔥蔥綠色和白色色調，黯淡的地面 | ![用來照亮物件的 WhippleCreekRegionalPark skybox](media/whipple-creek-regional-park.png)
|builtin://WinterRiver              | 具有明亮環境地面光線的日間                 | ![用來照亮物件的 WinterRiver skybox](media/winter-river.png)
|builtin://DefaultSky               | 與 TearsOfSteelBridge 相同                               | ![用來照亮物件的 DefaultSky skybox](media/tears-of-steel-bridge.png)

## <a name="api-documentation"></a>API 文件

* [C # RemoteManager. SkyReflectionSettings 屬性](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.remotemanager.skyreflectionsettings)
* [C + + RemoteManager：： SkyReflectionSettings ( # B1 ](https://docs.microsoft.com/cpp/api/remote-rendering/remotemanager#skyreflectionsettings)

## <a name="next-steps"></a>後續步驟

* [光線](../../overview/features/lights.md)
* [材質](../../concepts/materials.md)
* [紋理](../../concepts/textures.md)
* [TexConv 命令列工具](../../resources/tools/tex-conv.md)
