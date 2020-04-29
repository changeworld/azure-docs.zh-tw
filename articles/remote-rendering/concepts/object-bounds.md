---
title: 物件界限
description: 說明如何查詢空間物件界限
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: 1d2dfdb203b05f2f6b7de740718d7407bd88066c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681710"
---
# <a name="object-bounds"></a>物件界限

物件界限代表[實體](entities.md)及其子系所佔用的磁片區。 在 Azure 遠端呈現中，物件界限一律會指定為*軸對齊的周框方塊*（AABB）。 物件界限可以是在*本機空間*或*世界空間*中。 不論是哪種方式，它們一律會對齊軸，這表示區域和磁片區可能會在本機和世界空間標記法之間有所不同。

## <a name="querying-object-bounds"></a>查詢物件界限

您可以直接從網格資源查詢[網格](meshes.md)的本機 AABB。 您可以使用實體的轉換，將這些界限轉換成實體的本機空間或世界空間。

有可能以這種方式計算整個物件階層的界限，但這需要遍歷階層、查詢每個網格的界限，並以手動方式加以結合。 這項作業既繁瑣又沒有效率。

更好的方式是在`QueryLocalBoundsAsync`實體`QueryWorldBoundsAsync`上呼叫或。 然後，計算會卸載至伺服器，並以最少的延遲傳回。

``` cs
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

## <a name="next-steps"></a>後續步驟

* [空間查詢](../overview/features/spatial-queries.md)
