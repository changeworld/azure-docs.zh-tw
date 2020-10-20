---
title: 空間查詢
description: 如何在場景中執行空間查詢
author: jakrams
ms.author: jakras
ms.date: 02/07/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: d1a7baa25497cf1ba697725ac8530bc04c458aa5
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2020
ms.locfileid: "92207426"
---
# <a name="spatial-queries"></a>空間查詢

空間查詢是一種作業，可讓您向遠端轉譯服務詢問哪些物件位於區域中。 空間查詢經常用來實作互動，例如找出使用者所指向的物件。

所有空間查詢都會在伺服器上進行評估。 因此，空間查詢是非同步作業，而且結果會隨著您的網路延遲而延遲。 由於每個空間查詢都會產生網路流量，請小心不要一次執行太多動作。

## <a name="collision-meshes"></a>衝突網格

空間查詢由 [Havok Physics](https://www.havok.com/products/havok-physics) 引擎提供技術支援，而且需要有專用的衝突網格。 根據預設，[模型轉換](../../how-tos/conversion/model-conversion.md)會產生衝突網格。 如果您不需要複雜模型的空間查詢，請考量在[轉換選項](../../how-tos/conversion/configure-model-conversion.md)中停用衝突網格產生，因為會有多種方面的影響：

* [模型轉換](../../how-tos/conversion/model-conversion.md)需要更長的時間。
* 轉換的模型檔案大小明顯較大，因而影響下載速度。
* 執行階段載入時間較長。
* 執行階段 CPU 記憶體取用量較高。
* 每個模型執行個體的執行階段效能會有些許負擔。

## <a name="ray-casts"></a>光線轉換

「光線轉換」是一個空間查詢，執行階段會檢查哪些物件與光線有交集，從指定的位置開始，指向特定的方向。 為了最佳化，也會指定最大光線距離，不要搜尋太遠的物件。

```cs
async void CastRay(AzureSession session)
{
    // trace a line from the origin into the +z direction, over 10 units of distance.
    RayCast rayCast = new RayCast(new Double3(0, 0, 0), new Double3(0, 0, 1), 10);

    // only return the closest hit
    rayCast.HitCollection = HitCollectionPolicy.ClosestHit;

    RayCastHit[] hits = await session.Actions.RayCastQueryAsync(rayCast).AsTask();

    if (hits.Length > 0)
    {
        var hitObject = hits[0].HitObject;
        var hitPosition = hits[0].HitPosition;
        var hitNormal = hits[0].HitNormal;

        // do something with the hit information
    }
}
```

```cpp
void CastRay(ApiHandle<AzureSession> session)
{
    // trace a line from the origin into the +z direction, over 10 units of distance.
    RayCast rayCast;
    rayCast.StartPos = { 0, 0, 0 };
    rayCast.EndPos = { 0, 0, 1 };
    rayCast.MaxHits = 10;

    // only return the closest hit
    rayCast.HitCollection = HitCollectionPolicy::ClosestHit;

    ApiHandle<RaycastQueryAsync> castQuery = *session->Actions()->RayCastQueryAsync(rayCast);

    castQuery->Completed([](const ApiHandle<RaycastQueryAsync>& async)
        {
            std::vector<RayCastHit> hits;
            async->GetResult(hits);

            if (hits.size() > 0)
            {
                auto hitObject = hits[0].HitObject;
                auto hitPosition = hits[0].HitPosition;
                auto hitNormal = hits[0].HitNormal;

                // do something with the hit information
            }
        });
}
```


有三種接觸收集模式：

* **`Closest`：** 在此模式中，只會報告最接近的接觸。
* **`Any`：** 當您只想知道光線「是否」會接觸到任何東西時，請偏好使用此模式，但是請不要在意真正的情況。 此查詢的評估成本會大幅降低，但是也只有少數應用程式。
* **`All`：** 在此模式中，會報告光線的所有接觸，並以距離進行排序。 除非您真的需要比第一次接觸更多的資訊，否則請勿使用此模式。 使用 `MaxHits` 選項來限制回報的接觸次數。

若要選擇性地排除物件，使其不考慮進行光線轉換，可以使用 [HierarchicalStateOverrideComponent](override-hierarchical-state.md) 元件。

<!--
The CollisionMask allows the query to consider or ignore some objects based on their collision layer. If an object has layer L, it will be hit only if the mask has bit L set.
It is useful in case you want to ignore objects, for instance when setting an object transparent, and trying to select another object behind it.
TODO : Add an API to make that possible.
-->

### <a name="hit-result"></a>接觸結果

光線轉換查詢的結果是接觸陣列。 如果未接觸到任何物件，陣列就是空的。

接觸具有下列屬性：

* **`HitEntity`：** 接觸到哪一個[實體](../../concepts/entities.md)。
* **`SubPartId`：** 在 [MeshComponent](../../concepts/meshes.md) 中接觸到哪一個「子網格」。 可以用來為 `MeshComponent.UsedMaterials` 編製索引，並在該時間點查詢[材質](../../concepts/materials.md)。
* **`HitPosition`：** 光線與物件有交集的世界空間位置。
* **`HitNormal`：** 位於交集位置的網格一般世界空間表面。
* **`DistanceToHit`：** 從光線開始位置到接觸位置的距離。

## <a name="api-documentation"></a>API 文件

* [C # RemoteManager. RayCastQueryAsync ( # B1 ](/dotnet/api/microsoft.azure.remoterendering.remotemanager.raycastqueryasync)
* [C + + RemoteManager：： RayCastQueryAsync ( # B1 ](/cpp/api/remote-rendering/remotemanager#raycastqueryasync)

## <a name="next-steps"></a>後續步驟

* [物件界限](../../concepts/object-bounds.md)
* [覆寫階層式狀態](override-hierarchical-state.md)