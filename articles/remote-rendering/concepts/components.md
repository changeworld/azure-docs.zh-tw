---
title: 元件
description: Azure 遠端呈現範圍中元件的定義
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: conceptual
ms.openlocfilehash: cb8b38addef736914a8627971e57ea2b173293d6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681892"
---
# <a name="components"></a>元件

Azure 遠端呈現使用[實體元件系統](https://en.wikipedia.org/wiki/Entity_component_system)模式。 雖然[實體](entities.md)代表物件的位置和階層式組合，但元件會負責執行行為。

最常用的元件類型是「[網格元件](meshes.md)」，可將網格新增至轉譯管線。 同樣地，使用[light 元件](../overview/features/lights.md)來新增光源和[剪下平面元件](../overview/features/cut-planes.md)，是用來切割開啟的網格。

所有這些元件都使用其所連接之實體的轉換（位置、旋轉、縮放）做為參考點。

## <a name="working-with-components"></a>使用元件

您可以用程式設計方式輕鬆地新增、移除和操作元件：

```cs
// create a point light component
AzureSession session = GetCurrentlyConnectedSession();
PointLightComponent lightComponent = session.Actions.CreateComponent(ObjectType.PointLightComponent, ownerEntity) as PointLightComponent;

lightComponent.Color = new Color4Ub(255, 150, 20, 255);
lightComponent.Intensity = 11;

// ...

// destroy the component
lightComponent.Destroy();
lightComponent = null;
```

元件會在建立時附加至實體。 之後就無法將它移到另一個實體。 當元件的擁有者`Component.Destroy()`實體終結時，會明確地使用或自動刪除元件。

一次只能將每個元件類型的一個實例加入至實體。

## <a name="unity-specific"></a>Unity 特定

Unity 整合有額外的擴充功能，可與元件互動。 請參閱[Unity 遊戲物件和元件](../how-tos/unity/objects-components.md)。

## <a name="next-steps"></a>後續步驟

* [物件界限](object-bounds.md)
* [網狀](meshes.md)
