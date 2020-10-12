---
title: 元件
description: Azure 遠端轉譯範圍中的元件定義
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: a488e2499b92b290ad2b55120c3c70a18d45d426
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89613942"
---
# <a name="components"></a>元件

Azure 遠端轉譯使用[實體元件系統](https://en.wikipedia.org/wiki/Entity_component_system) \(英文\) 模式。 雖然[實體](entities.md)代表物件的位置和階層式組合，但元件會負責實作行為。

最常使用的元件類型是 [:::no-loc text="mesh components":::](meshes.md) ，會將網格新增至轉譯管線。 同樣地，[光線元件](../overview/features/lights.md)會用來新增光源，而[切割平面元件](../overview/features/cut-planes.md)會用來剪開網格。

所有這些元件都使用其所連結實體的轉換 (位置、旋轉、縮放) 作為其參考點。

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

```cpp
// create a point light component
ApiHandle<AzureSession> session = GetCurrentlyConnectedSession();

ApiHandle<PointLightComponent> lightComponent = session->Actions()->CreateComponent(ObjectType::PointLightComponent, ownerEntity)->as<PointLightComponent>();

// ...

// destroy the component
lightComponent->Destroy();
lightComponent = nullptr;
```

元件會在建立時連結至實體。 之後就無法將其移動到另一個實體。 當元件的擁有者實體終結時，元件會以 `Component.Destroy()` 明確地刪除或自動刪除。

每個元件類型的執行個體每次只能有一個新增至實體。

## <a name="unity-specific"></a>Unity 特定

Unity 整合有額外的延伸模組功能，可與元件互動。 請參閱 [Unity 遊戲物件和元件](../how-tos/unity/objects-components.md)。

## <a name="api-documentation"></a>API 文件

* [C # ComponentBase](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.componentbase)
* [C # RemoteManager. CreateComponent ( # B1 ](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.remotemanager.createcomponent)
* [C # Entity. FindComponentOfType ( # B1 ](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.entity.findcomponentoftype)
* [C + + ComponentBase](https://docs.microsoft.com/cpp/api/remote-rendering/componentbase)
* [C + + RemoteManager：： CreateComponent ( # B1 ](https://docs.microsoft.com/cpp/api/remote-rendering/remotemanager#createcomponent)
* [C + + Entity：： FindComponentOfType ( # B1 ](https://docs.microsoft.com/cpp/api/remote-rendering/entity#findcomponentoftype)

## <a name="next-steps"></a>後續步驟

* [物件界限](object-bounds.md)
* [網格](meshes.md)
