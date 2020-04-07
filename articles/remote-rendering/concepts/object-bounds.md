---
title: 物件邊界
description: 解釋如何查詢空間物件邊界
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: 1d2dfdb203b05f2f6b7de740718d7407bd88066c
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681710"
---
# <a name="object-bounds"></a>物件邊界

物件邊界表示[實體](entities.md)及其子級佔用的卷。 在 Azure 遠端渲染中,物件邊界始終作為*軸對齊的邊界框*(AABB) 給出。 物件邊界可以是*本地空間*,也可以位於*世界空間*。 無論哪種方式,它們始終與軸對齊,這意味著局部世界空間表示的範圍和體積可能不同。

## <a name="querying-object-bounds"></a>查詢物件邊界

可以直接從網格資源查詢[網格](meshes.md)的局部 AABB。 這些邊界可以使用實體的轉換轉換為實體的本地空間或世界空間。

可以以這種方式計算整個物件層次結構的邊界,但這需要遍歷層次結構、查詢每個網格的邊界並手動組合它們。 此操作既繁瑣又低效。

更好的方法是調用`QueryLocalBoundsAsync`實體`QueryWorldBoundsAsync`或調用實體。 然後,計算將卸載到伺服器,以最小的延遲返回。

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
