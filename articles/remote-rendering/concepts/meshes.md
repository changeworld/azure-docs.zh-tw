---
title: 網狀
description: Azure 遠端呈現範圍中的網格定義
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: b800272a538243d39a89e4eed64bc5cbc2d53ad8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80681762"
---
# <a name="meshes"></a>網狀

## <a name="mesh-resource"></a>網格資源

網格是不可變的[共用資源](../concepts/lifetime.md)，只能透過[模型轉換](../how-tos/conversion/model-conversion.md)來建立。 網格包含一個或多個*submeshes*。 根據預設，每個 submesh 都會參考應該呈現的[材質](materials.md)。 若要在3D 空間中放置網格，請將[MeshComponent](#meshcomponent)新增至[實體](entities.md)。

### <a name="mesh-resource-properties"></a>網格資源屬性

`Mesh`類別屬性包括：

* **材質：** 材質的陣列。 不同的 submesh 會使用每個材質。 陣列中的多個專案可能會參考[相同的](materials.md)資料。 此資料無法在執行時間修改。

* **界限：** 網格頂點的本機空間軸對齊周框方塊（AABB）。

## <a name="meshcomponent"></a>MeshComponent

`MeshComponent`類別是用來放置網格資源的實例。 每個 MeshComponent 都會參考單一網格。 它可能會覆寫用來呈現每個 submesh 的材質。

### <a name="meshcomponent-properties"></a>MeshComponent 屬性

* **網格：** 此元件所使用的網格資源。

* **材質：** 網格元件本身所指定的材質陣列。 陣列的長度一律會與網格資源上的*材質*陣列相同。 不能從網格預設值覆寫的材質，會在此陣列中設為*null* 。

* **UsedMaterials：** 每個 submesh 實際使用的材質陣列。 對於非 null 值，將會與*材質*陣列中的資料完全相同。 否則，它會包含網格實例中*材質*陣列的值。

## <a name="next-steps"></a>後續步驟

* [材質](materials.md)
