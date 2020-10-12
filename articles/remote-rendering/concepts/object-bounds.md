---
title: 物件界限
description: 說明如何查詢空間物件界限
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: bd2b1026f69b5ec5814124a92d6a78d7f0225143
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89613829"
---
# <a name="object-bounds"></a>物件界限

物件界限代表[實體](entities.md)和其子系佔用的磁碟區。 在 Azure 遠端轉譯中，物件界限一律會指定為「對齊軸的週框方塊」(AABB)。 物件界限可以是「區域空間」或「世界空間」。 不論是哪種方式，物件界限一律會對齊軸，這表示範圍和磁碟區可能在區域空間和世界空間標記法之間有所不同。

## <a name="querying-object-bounds"></a>查詢物件界限

[網格](meshes.md)的區域 AABB 可以直接從網格資源中查詢。 您可以使用實體轉換，將這些界限轉換成實體的區域空間或世界空間。

您有可能以這種方式計算整個物件階層的界限，但需要周遊階層、查詢每個網格的界限，並手動結合兩者。 這項作業既繁瑣又缺乏效率。

較好的方式是在實體上呼叫 `QueryLocalBoundsAsync` 或 `QueryWorldBoundsAsync`。 接下來計算會卸載至伺服器，並以最低限度的延遲傳回。

```cs
private BoundsQueryAsync _boundsQuery = null;

public void GetBounds(Entity entity)
{
    _boundsQuery = entity.QueryWorldBoundsAsync();
    _boundsQuery.Completed += (BoundsQueryAsync bounds) =>
    {
        if (bounds.IsRanToCompletion)
        {
            Double3 aabbMin = bounds.Result.min;
            Double3 aabbMax = bounds.Result.max;
            // ...
        }
    };
}
```

```cpp
void GetBounds(ApiHandle<Entity> entity)
{
    ApiHandle<BoundsQueryAsync> boundsQuery = *entity->QueryWorldBoundsAsync();
    boundsQuery->Completed([](ApiHandle<BoundsQueryAsync> bounds)
    {
        if (bounds->GetIsRanToCompletion())
        {
            Double3 aabbMin = bounds->GetResult().min;
            Double3 aabbMax = bounds->GetResult().max;
            // ...
        }
    });
}
```

## <a name="api-documentation"></a>API 文件

* [C # 實體. QueryLocalBoundsAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.entity.querylocalboundsasync)
* [C + + Entity：： QueryLocalBoundsAsync](https://docs.microsoft.com/cpp/api/remote-rendering/entity#querylocalboundsasync)

## <a name="next-steps"></a>後續步驟

* [空間查詢](../overview/features/spatial-queries.md)
