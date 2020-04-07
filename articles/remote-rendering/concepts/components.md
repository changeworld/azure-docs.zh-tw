---
title: 元件
description: Azure 遠端呈現範圍內元件定義
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: conceptual
ms.openlocfilehash: cb8b38addef736914a8627971e57ea2b173293d6
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681892"
---
# <a name="components"></a>元件

Azure 遠端呈現使用[實體元件系統](https://en.wikipedia.org/wiki/Entity_component_system)模式。 雖然[實體](entities.md)表示物件的位置和層次結構,但元件負責實現行為。

最常用的元件類型是[網格元件](meshes.md),它們向呈現管道中添加網格。 同樣,[光元件](../overview/features/lights.md)用於添加照明,[切割平面元件](../overview/features/cut-planes.md)用於剪切打開的構件。

所有這些元件都使用它們所附加到的實體的變換(位置、旋轉、比例)作為參考點。

## <a name="working-with-components"></a>使用元件

您可以輕鬆地以程式設計方式新增、刪除與操作元件:

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

元件在創建時附加到實體。 之後無法將其移動到其他實體。 當元件的擁有者實體被銷毀時`Component.Destroy()`,將顯式刪除或自動刪除元件。

一次只能將每個元件類型的一個實例添加到實體中。

## <a name="unity-specific"></a>特定於統一

Unity 整合有用於與元件互動的其他擴充功能。 請參考[Unity 遊戲物件與元件](../how-tos/unity/objects-components.md)。

## <a name="next-steps"></a>後續步驟

* [物件邊界](object-bounds.md)
* [格](meshes.md)
