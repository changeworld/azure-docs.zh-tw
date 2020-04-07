---
title: 天空反射
description: 描述如何為天空反射設定環境地圖
author: florianborn71
ms.author: flborn
ms.date: 02/07/2020
ms.topic: article
ms.openlocfilehash: 7316df7bcf78e3a154510e69116c288b2b293d4c
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680605"
---
# <a name="sky-reflections"></a>天空反射

在 Azure 遠程渲染中,天空紋理用於真實地照亮物件。 對於增強現實應用,此紋理應類似於實際環境,以使對象看起來令人信服。 本文介紹如何更改天空紋理。

> [!NOTE]
> 天空紋理也稱為*環境地圖*。 這些術語可互換使用。

## <a name="object-lighting"></a>物件照明

Azure 遠端渲染採用*基於物理的渲染*(PBR) 進行逼真的照明計算。 儘管您可以將[光源](lights.md)添加到場景中,但使用良好的天空紋理影響最大。

下圖僅顯示僅使用天空紋理照亮不同曲面的結果:

| 粗糙度  | 0                                        | 0.25                                          | 0.5                                          | 0.75                                          | 1                                          |
|:----------:|:----------------------------------------:|:---------------------------------------------:|:--------------------------------------------:|:---------------------------------------------:|:------------------------------------------:|
| 非金屬  | ![介電0](media/dielectric-0.png)   | ![綠點公園](media/dielectric-0.25.png)  | ![綠點公園](media/dielectric-0.5.png)  | ![綠點公園](media/dielectric-0.75.png)  | ![綠點公園](media/dielectric-1.png)  |
| Metal      | ![綠點公園](media/metallic-0.png)  | ![綠點公園](media/metallic-0.25.png)    | ![綠點公園](media/metallic-0.5.png)    | ![綠點公園](media/metallic-0.75.png)    | ![綠點公園](media/metallic-1.png)    |

有關照明模型的詳細資訊,請參閱[材料](../../concepts/materials.md)章節。

> [!IMPORTANT]
> Azure 遠端渲染僅對照明模型使用天空紋理。 它不渲染天空作為背景,因為增強現實應用程式已經有一個適當的背景 - 現實世界。

## <a name="changing-the-sky-texture"></a>變更天空紋理

要更改環境映射,只需[載入紋理](../../concepts/textures.md)並變更工作階段`SkyReflectionSettings`的 :

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

請注意,`LoadTextureFromSASAsync`該變體用於上述,因為已載入內置紋理。 如果從[連結的 Blob 儲存](../../how-tos/create-an-account.md#link-storage-accounts)載入,請`LoadTextureAsync`使用變數 。

## <a name="sky-texture-types"></a>天空紋理類型

您可以將*[立方體貼圖](https://en.wikipedia.org/wiki/Cube_mapping)* 和*2D 紋理*用作環境貼圖。

所有紋理必須使用[支援的紋理格式](../../concepts/textures.md#supported-texture-formats)。 您無需為天空紋理提供 mipmap。

### <a name="cube-environment-maps"></a>立方體環境地圖

供參考,下面是一個未包裝的立方體圖:

![未包裝的立方體圖](media/Cubemap-example.png)

`AzureSession.Actions.LoadTextureAsync``LoadTextureFromSASAsync`用於載入`TextureType.CubeMap`立方體貼/ 圖紋理。

### <a name="sphere-environment-maps"></a>球體環境圖

當使用 2D 紋理作為環境貼圖時,圖像必須位於[球形座標空間](https://en.wikipedia.org/wiki/Spherical_coordinate_system)中。

![球形座標中的天空影像](media/spheremap-example.png)

用於`AzureSession.Actions.LoadTextureAsync``TextureType.Texture2D`載入球形環境貼圖。

## <a name="built-in-environment-maps"></a>內建環境圖

Azure 遠端呈現提供了一些始終可用的內置環境映射。 所有內置環境地圖都是立方體貼圖。

|識別碼                         | 描述                                              | 圖例                                                      |
|-----------------------------------|:---------------------------------------------------------|:-----------------------------------------------------------------:|
|builtin://Autoshop                 | 各種條紋燈,明亮的室內基礎照明    | ![自動商店](media/autoshop.png)
|builtin://BoilerRoom               | 明亮的室內燈光設置,多個窗燈      | ![鍋爐房](media/boiler-room.png)
|builtin://ColorfulStudio           | 中等燈光室內設定中顏色不同的燈光  | ![彩色工作室](media/colorful-studio.png)
|builtin://Hangar                   | 中等明亮的環境大廳燈                     | ![小漢加爾](media/hangar.png)
|builtin://IndustrialPipeAndValve   | 暗暗室內設置,形成淺暗對比度              | ![工業管道和閥門](media/industrial-pipe-and-valve.png)
|builtin://Lebombo                  | 白天環境室燈,明亮的窗戶區域光線     | ![萊邦博](media/lebombo.png)
|builtin://SataraNight              | 黑暗的夜空和地面,有許多周圍的燈光   | ![薩塔拉之夜](media/satara-night.png)
|builtin://SunnyVondelpark          | 明亮的陽光和陰影對比度                      | ![桑尼馮德爾帕克](media/sunny-vondelpark.png)
|builtin://Syferfontein             | 清晰的天光,具有適度的地面照明            | ![西費爾方丹](media/syferfontein.png)
|builtin://TearsOfSteelBridge       | 適度變化的太陽和陰影                         | ![鋼橋的眼淚](media/tears-of-steel-bridge.png)
|builtin://VeniceSunset             | 傍晚日落燈接近黃昏                    | ![威尼斯日落](media/venice-sunset.png)
|builtin://WhippleCreekRegionalPark | 明亮、鬱鬱蔥蔥的綠色和白色光色調,昏暗的地面 | ![惠普勒克里克區域公園](media/whipple-creek-regional-park.png)
|builtin://WinterRiver              | 白天,具有明亮的環境地面光                 | ![溫特河](media/winter-river.png)
|builtin://DefaultSky               | 與《鋼鐵橋的眼淚》一樣                               | ![預設天空](media/tears-of-steel-bridge.png)

## <a name="next-steps"></a>後續步驟

* [燈](../../overview/features/lights.md)
* [物料](../../concepts/materials.md)
* [紋理](../../concepts/textures.md)
* [TexConv 指令列工具](../../resources/tools/tex-conv.md)
