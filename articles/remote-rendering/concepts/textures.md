---
title: 紋理
description: 紋理資源工作流程
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 27395fe377972f51c849f8a61f51a628612ed54d
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2020
ms.locfileid: "92202662"
---
# <a name="textures"></a>紋理

紋理是不可變的[共用資源](../concepts/lifetime.md)。 紋理可以從 [Blob 儲存體](../how-tos/conversion/blob-storage.md)載入並直接套用至模型，如[教學課程：變更環境與材質](../tutorials/unity/materials-lighting-effects/materials-lighting-effects.md)中所示。 但最常見的是，紋理會成為[轉換後模型](../how-tos/conversion/model-conversion.md)的一部分，並會由其[材質](materials.md)加以參考。

## <a name="texture-types"></a>紋理類型

不同的紋理類型具有不同的使用案例：

* **2D 紋理**主要用於 [材質](materials.md)。
* **立方體地圖**可用於[天空](../overview/features/sky.md)。

## <a name="supported-texture-formats"></a>支援的紋理格式

提供給 ARR 的所有紋理都必須是 [DDS 格式](https://en.wikipedia.org/wiki/DirectDraw_Surface)。 最好使用 Mipmap 和紋理壓縮。 如果您要將轉換流程自動化，請參閱 [TexConv 命令列工具](../resources/tools/tex-conv.md)。

## <a name="loading-textures"></a>載入紋理

載入紋理時，您必須指定其預期的類型。 如果類型不符，紋理載入會失敗。
載入兩次具有相同 URI 的紋理，會傳回相同的材質物件，因為是[共用資源](../concepts/lifetime.md)。

與載入模型類似，在來源 Blob 儲存體中處理紋理資產的兩種變體如下：

* 紋理資產可以透過其 SAS URI 處理。 相關的載入函式為 `LoadTextureFromSASAsync` 與 `LoadTextureFromSASParams` 參數。 載入[內建紋理](../overview/features/sky.md#built-in-environment-maps)時，也請使用此變體。
* 此紋理可直接由 Blob 儲存體參數定址，以防 [Blob 儲存體連結至帳戶](../how-tos/create-an-account.md#link-storage-accounts)。 在此情況下，相關的載入函式為 `LoadTextureAsync` 與 `LoadTextureParams` 參數。

下列範例程式碼示範如何透過其 SAS URI (或內建紋理) 載入紋理 - 請注意，在其他情況下，只有載入函式/參數會有所不同：

```cs
LoadTextureAsync _textureLoad = null;
void LoadMyTexture(AzureSession session, string textureUri)
{
    _textureLoad = session.Actions.LoadTextureFromSASAsync(new LoadTextureFromSASParams(textureUri, TextureType.Texture2D));
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

```cpp
void LoadMyTexture(ApiHandle<AzureSession> session, std::string textureUri)
{
    LoadTextureFromSASParams params;
    params.TextureType = TextureType::Texture2D;
    params.TextureUrl = std::move(textureUri);
    ApiHandle<LoadTextureAsync> textureLoad = *session->Actions()->LoadTextureFromSASAsync(params);
    textureLoad->Completed([](ApiHandle<LoadTextureAsync> res)
    {
        if (res->GetIsRanToCompletion())
        {
            //use res->Result()
        }
        else
        {
            printf("Texture loading failed!");
        }
    });
}
```

根據應使用的紋理，可能會有紋理類型和內容的限制。 例如，[PBR 材質](../overview/features/pbr-materials.md)的粗糙度對應必須是灰階。

> [!CAUTION]
> ARR 中的所有「非同步」函式都會傳回非同步作業物件。 您必須先儲存這些物件的參考，直到作業完成為止。 否則，C# 記憶體回收行程可能會提早刪除作業，而且其永遠無法完成。 在上述的範例程式碼中，會使用成員變數 '_textureLoad' 來保存參考，直到「已完成」事件抵達為止。

## <a name="api-documentation"></a>API 文件

* [C # 材質類別](/dotnet/api/microsoft.azure.remoterendering.texture)
* [C # RemoteManager. LoadTextureAsync ( # B1 ](/dotnet/api/microsoft.azure.remoterendering.remotemanager.loadtextureasync)
* [C # RemoteManager. LoadTextureFromSASAsync ( # B1 ](/dotnet/api/microsoft.azure.remoterendering.remotemanager.loadtexturefromsasasync)
* [C + + 材質類別](/cpp/api/remote-rendering/texture)
* [C + + RemoteManager：： LoadTextureAsync ( # B1 ](/cpp/api/remote-rendering/remotemanager#loadtextureasync)
* [C + + RemoteManager：： LoadTextureFromSASAsync ( # B1 ](/cpp/api/remote-rendering/remotemanager#loadtexturefromsasasync)

## <a name="next-steps"></a>後續步驟

* [材質](materials.md)
* [天空](../overview/features/sky.md)