---
title: 紋理
description: 材質資源工作流程
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: 09fa22d33377dfcbafd84f0caeb5f33a575b1bce
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681658"
---
# <a name="textures"></a>紋理

材質是不可變的[共用資源](../concepts/lifetime.md)。 材質可以從[blob 儲存體](../how-tos/conversion/blob-storage.md)載入並直接套用至模型，如[教學課程：變更環境和資料所](../tutorials/unity/changing-environment-and-materials.md)示。 不過，最常見的材質是[轉換模型](../how-tos/conversion/model-conversion.md)的一部分，其[材質](materials.md)會參考它們。

## <a name="texture-types"></a>紋理類型

不同的材質類型具有不同的使用案例：

* **2D 材質**主要用於[材質](materials.md)。
* **Cubemaps**可用於[天空](../overview/features/sky.md)。

## <a name="supported-texture-formats"></a>支援的材質格式

所有提供給 ARR 的紋理都必須是[DDS 格式](https://en.wikipedia.org/wiki/DirectDraw_Surface)。 最好使用 mipmap 和材質壓縮。 如果您想要將轉換程式自動化，請參閱[TexConv 命令列工具](../resources/tools/tex-conv.md)。

## <a name="loading-textures"></a>載入紋理

載入材質時，您必須指定其預期的類型。 如果類型不符，材質載入會失敗。
載入具有相同 URI 的紋理兩次，將會傳回相同的材質物件，因為它是[共用資源](../concepts/lifetime.md)。

類似于載入模型，在來源 blob 儲存體中定址材質資產的兩種變體如下：

* 材質資產可以透過其 SAS URI 來定址。 相關的載入函數`LoadTextureFromSASAsync`是使用`LoadTextureFromSASParams`參數。 載入[內建材質](../overview/features/sky.md#built-in-environment-maps)時，也請使用此變體。
* 如果[blob 儲存體連結至帳戶](../how-tos/create-an-account.md#link-storage-accounts)，則可直接由 blob 儲存體參數定址材質。 在此情況下，相關的`LoadTextureAsync`載入函數`LoadTextureParams`是使用參數。

下列範例程式碼示範如何透過其 SAS URI （或內建材質）載入材質-請注意，在其他情況下，只有載入函數/參數會有所不同：

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

根據應使用的材質，可能會有材質類型和內容的限制。 例如， [.pbr 材質](../overview/features/pbr-materials.md)的粗糙度對應必須是灰階。

> [!CAUTION]
> ARR 中的所有*非同步*函數都會傳回非同步作業物件。 您必須先儲存這些物件的參考，直到作業完成為止。 否則，c # 垃圾收集行程可能會提早刪除作業，而且永遠無法完成。 在上述的範例程式碼中，會使用成員變數 ' _textureLoad ' 來保存參考，直到*完成*的事件抵達為止。

## <a name="next-steps"></a>後續步驟

* [材質](materials.md)
* [Sky](../overview/features/sky.md)
