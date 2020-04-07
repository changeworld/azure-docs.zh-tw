---
title: 空間查詢
description: 如何在場景中執行空間查詢
author: jakrams
ms.author: jakras
ms.date: 02/07/2020
ms.topic: article
ms.openlocfilehash: 9a981aeb08ec46900994fd599b592b9f16034f34
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680527"
---
# <a name="spatial-queries"></a>空間查詢

空間查詢是可以詢問遠端呈現服務位於區域中的物件的操作。 空間查詢通常用於實現交互,例如確定使用者指向哪個物件。

所有空間查詢都在伺服器上計算。 因此,它們是異步操作,結果將延遲到達,具體取決於您的網路延遲。 由於每個空間查詢都會生成網路流量,因此請注意不要一次執行太多操作。

## <a name="collision-meshes"></a>碰撞模因

空間查詢由[Havok 物理](https://www.havok.com/products/havok-physics)引擎提供動力,需要存在專用的碰撞網格。 默認情況下,[模型轉換](../../how-tos/conversion/model-conversion.md)生成衝突模樣。 如果不需要對複雜模型進行空間查詢,請考慮在[轉換選項](../../how-tos/conversion/configure-model-conversion.md)中禁用衝突網格生成,因為它具有多種方式的影響:

* [模型轉換](../../how-tos/conversion/model-conversion.md)將需要更長的時間。
* 轉換后的模型檔大小明顯較大,影響下載速度。
* 運行時載入時間較長。
* 運行時 CPU 記憶體消耗較高。
* 每個模型實例都有輕微的運行時性能開銷。

## <a name="ray-casts"></a>雷鑄

*光線強制轉換*是一種空間查詢,運行時檢查哪些物件由光線相交,從給定位置開始並指向特定方向。 作為優化,還給出了最大光線距離,以便不搜索太遠的物件。

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

有三種命中收集模式:

* **最近:** 在此模式下,僅報告最接近的命中。
* **任何:** 首選這種模式,當你想知道的是,*光線是否會*擊中任何東西,但不在乎什麼擊中究竟。 此查詢可以大大便宜評估,但也只有很少的應用程式。
* **所有:** 在此模式下,沿光線的所有命中都報告,按距離排序。 不要使用此模式,除非您確實需要超過第一次命中。 使用`MaxHits`選項限制報告命中數。

為了有選擇地排除物件考慮光線強制轉換,可以使用[分層狀態覆蓋元件元件](override-hierarchical-state.md)。

<!--
The CollisionMask allows the quey to consider or ignore some objects based on their collision layer. If an object has layer L, it will be hit only if the mask has  bit L set.
It is useful in case you want to ignore objects, for instance when setting an object transparent, and trying to select another object behind it.
TODO : Add an API to make that possible.
-->

### <a name="hit-result"></a>命中結果

光線轉換查詢的結果是命中陣組。 如果未命中任何物件,則陣列為空。

命中具有以下屬性:

* **命中實體:** 哪個[實體](../../concepts/entities.md)被擊中。
* **子部份Id:** 哪個*子網格*在[網格元件](../../concepts/meshes.md)中被擊中。 可用於索引`MeshComponent.UsedMaterials`與尋找[材料](../../concepts/materials.md)。
* **命中位置:** 光線與物體相交的世界空間位置。
* **命中正常值:** 網格在交點位置的世界空間表面正常。
* **距離命中:** 從光線起始位置到命中的距離。

## <a name="next-steps"></a>後續步驟

* [物件邊界](../../concepts/object-bounds.md)
* [重寫分層狀態](override-hierarchical-state.md)
