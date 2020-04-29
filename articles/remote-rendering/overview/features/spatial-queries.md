---
title: 空間查詢
description: 如何在場景中執行空間查詢
author: jakrams
ms.author: jakras
ms.date: 02/07/2020
ms.topic: article
ms.openlocfilehash: 9a981aeb08ec46900994fd599b592b9f16034f34
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80680527"
---
# <a name="spatial-queries"></a>空間查詢

空間查詢是一種作業，可讓您向遠端轉譯服務詢問哪些物件位於區域中。 空間查詢經常用來執行互動，例如找出使用者所指向的物件。

所有空間查詢都會在伺服器上進行評估。 因此，它們是非同步作業，而且結果會隨著您的網路延遲而延遲。 由於每個空間查詢都會產生網路流量，請小心不要一次執行太多動作。

## <a name="collision-meshes"></a>衝突網格

空間查詢是由[Havok 物理](https://www.havok.com/products/havok-physics)引擎提供技術支援，而且需要有專用的衝突網格。 根據預設，[模型轉換](../../how-tos/conversion/model-conversion.md)會產生衝突網格。 如果您不需要在複雜模型上進行空間查詢，請考慮在[轉換選項](../../how-tos/conversion/configure-model-conversion.md)中停用衝突網格產生，因為它有多種方式的影響：

* [模型轉換](../../how-tos/conversion/model-conversion.md)需要更長的時間。
* 轉換的模型檔案大小明顯較大，因而影響下載速度。
* 執行時間載入時間較長。
* 執行時間 CPU 記憶體耗用量較高。
* 每個模型實例的執行時間效能會有些許負擔。

## <a name="ray-casts"></a>光線轉換

「*光線轉換*」是一種空間查詢，執行時間會檢查哪些物件與光線相交，從指定的位置開始，指向特定的方向。 做為優化，也會指定最大光線距離，而不會搜尋太遠的物件。

````c#
async void CastRay(AzureSession session)
{
    // trace a line from the origin into the +z direction, over 10 units of distance.
    RayCast rayCast = new RayCast(new Double3(0, 0, 0), new Double3(0, 0, 1), 10);

    // only return the closest hit
    rayCast.HitCollection = HitCollectionPolicy.ClosestHit;

    RayCastHit[] hits = await session.Actions.RayCastQueryAsync(rayCast).AsTask();

    if (hits.Length > 0)
    {
        var hitObject = hits[0].HitEntity;
        var hitPosition = hits[0].HitPosition;
        var hitNormal = hits[0].HitNormal;

        // do something with the hit information
    }
}
````

有三種點擊收集模式：

* **最接近：** 在此模式中，只會報告最接近的叫用。
* **任何：** 當您只想知道光線是否會碰到任何東西，但不在意到底*達到的情況*時，偏好使用此模式。 此查詢的評估成本會大幅降低，但也只有少數應用程式。
* **全部：** 在此模式中，會報告光線中的所有點擊次數，並以距離進行排序。 除非您真的需要比第一次叫用更多的資訊，否則請勿使用此模式。 使用`MaxHits`選項限制回報的點擊次數。

若要選擇性地排除物件而不考慮光線轉換，可以使用[HierarchicalStateOverrideComponent](override-hierarchical-state.md)元件。

<!--
The CollisionMask allows the quey to consider or ignore some objects based on their collision layer. If an object has layer L, it will be hit only if the mask has  bit L set.
It is useful in case you want to ignore objects, for instance when setting an object transparent, and trying to select another object behind it.
TODO : Add an API to make that possible.
-->

### <a name="hit-result"></a>點擊結果

「光線轉換」查詢的結果是一種叫用陣列。 如果未叫用任何物件，陣列就是空的。

點擊具有下列屬性：

* **HitEntity：** 已叫用的[實體](../../concepts/entities.md)。
* **SubPartId：**[MeshComponent](../../concepts/meshes.md)中遇到了哪個*submesh* 。 可以用來編制索引， `MeshComponent.UsedMaterials`並在該點查詢[材質](../../concepts/materials.md)。
* **HitPosition：** 與物件相交之光線的世界空間位置。
* **HitNormal：** 網格在交集位置的世界空間表面法線。
* **DistanceToHit：** 從光線開始位置到點擊的距離。

## <a name="next-steps"></a>後續步驟

* [物件界限](../../concepts/object-bounds.md)
* [覆寫階層式狀態](override-hierarchical-state.md)
