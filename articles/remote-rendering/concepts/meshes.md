---
title: 網狀
description: Azure 遠端轉譯範圍中的網格定義
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: 08d80a5ec2099147c12bcecd3b52d64429837285
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2020
ms.locfileid: "90563959"
---
# <a name="meshes"></a>網狀

## <a name="mesh-resource"></a>網格資源

網格是不可變的 [共用資源](../concepts/lifetime.md)，只能透過 [模型轉換](../how-tos/conversion/model-conversion.md)來建立。 網格包含一或多個 *submeshes* ，以及 [raycast 查詢](../overview/features/spatial-queries.md)的物理標記法。 每個 submesh 都會參考預設應呈現的 [材質](materials.md) 。 若要在3D 空間中放置網格，請將 [MeshComponent](#meshcomponent) 新增至 [實體](entities.md)。

### <a name="mesh-resource-properties"></a>網格資源屬性

`Mesh`類別屬性包括：

* **教材：** 材質的陣列。 每個材質都是由不同的 submesh 使用。 陣列中的多個專案可能會參考相同的 [材質](materials.md)。 此資料無法在執行時間修改。

* **界限：** 區域間距軸對齊的周框方塊 (AABB 網格頂點的) 。

## <a name="meshcomponent"></a>MeshComponent

`MeshComponent`類別是用來放置網格資源的實例。 每個 MeshComponent 都會參考單一網格。 它可能會覆寫用來呈現每個 submesh 的材質。

### <a name="meshcomponent-properties"></a>MeshComponent 屬性

* **網格：** 此元件使用的網格資源。

* **教材：** 網格元件本身所指定的材質陣列。 陣列的長度一律會與網格資源上的 *材質* 陣列相同。 不應從網格預設覆寫的材質會在此陣列中設定為 *null* 。

* **UsedMaterials：** 每個 submesh 的實際使用材質的陣列。 將與 *資料陣列中的資料* 相同，用於非 null 的值。 否則，它會包含網格實例中 *材質* 陣列的值。

### <a name="sharing-of-meshes"></a>網格的共用

您 `Mesh` 可以跨多個網格元件實例共用資源。 此外， `Mesh` 指派給網格元件的資源可以在任何時間以程式設計方式變更。 下列程式碼示範如何複製網格：

```cs
Entity CloneEntityWithModel(RemoteManager manager, Entity sourceEntity)
{
    MeshComponent meshComp = sourceEntity.FindComponentOfType<MeshComponent>();
    if (meshComp != null)
    {
        Entity newEntity = manager.CreateEntity();
        MeshComponent newMeshComp = manager.CreateComponent(ObjectType.MeshComponent, newEntity) as MeshComponent;
        newMeshComp.Mesh = meshComp.Mesh; // share the mesh
        return newEntity;
    }
    return null;
}
```

```cpp
ApiHandle<Entity> CloneEntityWithModel(ApiHandle<RemoteManager> manager, ApiHandle<Entity> sourceEntity)
{
    if (ApiHandle<MeshComponent> meshComp = sourceEntity->FindComponentOfType<MeshComponent>())
    {
        ApiHandle<Entity> newEntity = *manager->CreateEntity();
        ApiHandle<MeshComponent> newMeshComp = manager->CreateComponent(ObjectType::MeshComponent, newEntity)->as<RemoteRendering::MeshComponent>();
        newMeshComp->SetMesh(meshComp->GetMesh()); // share the mesh
        return newEntity;
    }
    return nullptr;
}
```

## <a name="api-documentation"></a>API 文件

* [C # 網狀類別](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.mesh)
* [C # MeshComponent 類別](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.meshcomponent)
* [C + + 網格類別](https://docs.microsoft.com/cpp/api/remote-rendering/mesh)
* [C + + MeshComponent 類別](https://docs.microsoft.com/cpp/api/remote-rendering/meshcomponent)


## <a name="next-steps"></a>後續步驟

* [材質](materials.md)
