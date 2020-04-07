---
title: 格
description: Azure 遠端呈現範圍內的模因的定義
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: b800272a538243d39a89e4eed64bc5cbc2d53ad8
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681762"
---
# <a name="meshes"></a>格

## <a name="mesh-resource"></a>格線資源

meshes是一個不可變[的共用資源](../concepts/lifetime.md),只能通過[模型轉換](../how-tos/conversion/model-conversion.md)來創建。 meshes 包含一個或多個*子等。* 每個子網格引用預設情況下應呈現材質[的材料](materials.md)。 要將格線放置在 3D 空間中[Entity](entities.md),加入[網格元件](#meshcomponent)。

### <a name="mesh-resource-properties"></a>網格資源屬性

類別`Mesh`屬性包括:

* **材料:** 一系列材料。 每種材料都由不同的子網格使用。 陣列中的多個項目可能參考相同的[材質](materials.md)。 此數據在運行時無法修改。

* **邊界:** 網格頂點的局部空間軸對齊邊界框 (AABB)。

## <a name="meshcomponent"></a>格線元件

類`MeshComponent`用於放置網格資源的實例。 每個網格元件引用單個網格。 它可以覆蓋用於渲染每個子網格的材料。

### <a name="meshcomponent-properties"></a>格線元件屬性

* **格線:** 此元件使用的網格資源。

* **材料:** 網格元件本身上指定的材質陣列。 陣列的長度始終與網格資源上的 *「材質」* 陣組相同。 不應從網格預設值中重寫的材料將在此陣列中設置為*null。*

* **已用材料:** 每個子網格的實際使用材質的陣列。 對於非空值,將和 *「材質」* 陣列中的數據相同。 否則,它包含網格實例中的 *「材質」* 陣列中的值。

## <a name="next-steps"></a>後續步驟

* [物料](materials.md)
