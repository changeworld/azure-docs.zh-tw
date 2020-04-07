---
title: 紋理
description: 紋理資源工作流
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: 09fa22d33377dfcbafd84f0caeb5f33a575b1bce
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681658"
---
# <a name="textures"></a>紋理

紋理是一種不可變[的分享資源](../concepts/lifetime.md)。 紋理可以從[blob 儲存](../how-tos/conversion/blob-storage.md)載入並直接應用於模型,如[教程:更改環境和材質](../tutorials/unity/changing-environment-and-materials.md)所示。 但最常見的情況是,紋理將是[轉換模型](../how-tos/conversion/model-conversion.md)的一部分,其中它們被其[材質](materials.md)引用。

## <a name="texture-types"></a>紋理類型

不同的紋理類型具有不同的用例:

* **2D紋理**主要用於[材料](materials.md)。
* **立方體地圖**可用於[天空](../overview/features/sky.md)。

## <a name="supported-texture-formats"></a>支援的紋理格式

給 ARR 的所有紋理都必須採用[DDS 格式](https://en.wikipedia.org/wiki/DirectDraw_Surface)。 最好使用 mipmaps 和紋理壓縮。 如果要自動執行轉換過程,請參閱[TexConv 命令列工具](../resources/tools/tex-conv.md)。

## <a name="loading-textures"></a>載入紋理

載入紋理時,必須指定其預期類型。 如果類型不匹配,紋理載入將失敗。
使用同一 URI 載入紋理兩次將傳回相同的紋理物件,因為它是[分享資源](../concepts/lifetime.md)。

與載入模型類似,在源 Blob 儲存中定址紋理資產有兩種變體:

* 紋理資產可以通過其 SAS URI 進行定址。 相關的載入函數是`LoadTextureFromSASAsync`帶參數`LoadTextureFromSASParams`的。 載入[內建紋理](../overview/features/sky.md#built-in-environment-maps)時,也可以使用此變體。
* 如果[blob 儲存連結到帳戶](../how-tos/create-an-account.md#link-storage-accounts),則可以通過 blob 儲存參數直接定址紋理。 在這種情況下,相關的載入功能是`LoadTextureAsync`參數`LoadTextureParams`。

以下範例代碼展示如何透過其 SAS URI(或內建紋理)載入紋理 - 請注意,對於其他情況,只有載入函數/參數不同:

``` cs
LoadTextureAsync _textureLoad = null;
void LoadMyTexture(AzureSession session, string textureUri)
{
    _textureLoad = session.Actions.LoadTextureAsync(new LoadTextureParams(textureUri, TextureType.Texture2D));
    _textureLoad.Completed +=
        (LoadTextureAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                System.Console.WriteLine("Texture loading failed!");
            }
            _textureLoad = null;
        };
}
```

根據紋理應該用於什麼,紋理類型和內容可能會有限制。 例如[,PBR 材質](../overview/features/pbr-materials.md)的粗糙度貼圖必須為灰度。

> [!CAUTION]
> ARR 中的所有*非同步*函數傳回非同步操作物件。 您必須存儲對這些物件的引用,直到操作完成。 否則,C# 垃圾回收器可能會提前刪除該操作,並且它永遠無法完成。 在上述範例代碼中,成員變數"_textureLoad"用於保存引用,直到 *「已完成」* 事件到達。

## <a name="next-steps"></a>後續步驟

* [物料](materials.md)
* [天空](../overview/features/sky.md)
