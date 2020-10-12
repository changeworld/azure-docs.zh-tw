---
title: 切割平面
description: 說明什麼是切割平面以及如何使用
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 468d21abc861e905472d1d15405b1c8ba9e5be74
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90904879"
---
# <a name="cut-planes"></a>切割平面

「切割平面」是一種視覺化功能，可以在虛擬平面的一端裁剪像素，並在[網格](../../concepts/meshes.md)內顯示。
下圖示範效果。 左邊顯示的是原始網格，在右邊可以查看網格內部：

![切割平面](./media/cutplane-1.png)

## <a name="cutplanecomponent"></a>CutPlaneComponent

藉由建立 *CutPlaneComponent*，將切割平面新增至場景中。 平面的位置和方向是由元件的擁有者[實體](../../concepts/entities.md)決定。

```cs
void CreateCutPlane(AzureSession session, Entity ownerEntity)
{
    CutPlaneComponent cutPlane = (CutPlaneComponent)session.Actions.CreateComponent(ObjectType.CutPlaneComponent, ownerEntity);
    cutPlane.Normal = Axis.X; // normal points along the positive x-axis of the owner object's orientation
    cutPlane.FadeColor = new Color4Ub(255, 0, 0, 128); // fade to 50% red
    cutPlane.FadeLength = 0.05f; // gradient width: 5cm
}
```

```cpp
void CreateCutPlane(ApiHandle<AzureSession> session, ApiHandle<Entity> ownerEntity)
{
    ApiHandle<CutPlaneComponent> cutPlane = session->Actions()->CreateComponent(ObjectType::CutPlaneComponent, ownerEntity)->as<CutPlaneComponent>();;
    cutPlane->SetNormal(Axis::X); // normal points along the positive x-axis of the owner object's orientation
    Color4Ub fadeColor;
    fadeColor.channels = { 255, 0, 0, 128 }; // fade to 50% red
    cutPlane->SetFadeColor(fadeColor);
    cutPlane->SetFadeLength(0.05f); // gradient width: 5cm
}
```

### <a name="cutplanecomponent-properties"></a>CutPlaneComponent 屬性

下列屬性會在切割平面元件上公開：

* `Enabled`:您可以藉由停用元件，暫時關閉切割平面。 停用的切割平面不會產生轉譯額外負荷，也不會計入全域切割平面限制。

* `Normal`:指定要使用哪個方向 (+X、-X、+Y、-Y、+Z、-Z) 作為一般平面。 這個方向相對於擁有者實體的方向。 移動和旋轉擁有者實體以取得確切的位置。

* `FadeColor` 和 `FadeLength`：

  如果 FadeColor 的 Alpha 值為非零，接近切割平面的像素會淡入 FadeColor 的 RGB 部分。 Alpha 色板的強度會決定是否要完全淡入淡出色彩或僅部分。 *FadeLength* 會定義此淡入發生的距離。

* `ObjectFilterMask`：篩選位遮罩，用來決定剪下的平面會影響哪些幾何。 如需詳細資訊，請參閱下一段。

### <a name="selective-cut-planes"></a>選擇性剪下平面

您可以設定個別的剪下平面，使其只影響特定幾何。 下圖說明此設定如何實務：

![選擇性剪下平面](./media/selective-cut-planes.png)

篩選是在剪下平面端的位元遮罩與幾何上設定的第二個位遮罩之間的 **邏輯位遮罩比較** 運作。 如果 `AND` 遮罩之間邏輯運算的結果不是零，剪下的平面將會影響幾何。

* 剪下平面元件上的位元遮罩是透過其屬性來設定。 `ObjectFilterMask`
* 幾何子階層上的位元遮罩是透過[HierarchicalStateOverrideComponent](override-hierarchical-state.md#features)設定。

範例：

| 剪下平面篩選遮罩 | 幾何篩選遮罩  | 邏輯的結果 `AND` | 剪下平面會影響幾何嗎？  |
|--------------------|-------------------|-------------------|:----------------------------:|
|  (0000 0001) = = 1   |  (0000 0001) = = 1  |  (0000 0001) = = 1  | 是 |
|  (1111 0000) = = 240 |  (0001 0001) = = 17 |  (0001 0000) = = 16 | 是 |
|  (0000 0001) = = 1   |  (0000 0010) = = 2  |  (0000 0000) = = 0  | 否 |
|  (0000 0011) = = 3   |  (0000 1000) = = 8  |  (0000 0000) = = 0  | 否 |

>[!TIP]
> 將剪下的平面設定 `ObjectFilterMask` 為0，表示它不會影響任何幾何，因為邏輯的結果 `AND` 絕對不能是 null。 轉譯系統不會在一開始就考慮這些平面，因此這是停用個別剪下平面的輕量方法。 這些剪下的平面也不會計入8個使用中平面的限制。

## <a name="limitations"></a>限制

* Azure 遠端轉譯 **最多可支援八個作用中的剪下平面**。 您可以建立更多切割平面元件，但是如果您嘗試同時啟用更多，則會忽略啟用。 如果您想要切換哪些元件應該影響場景，請先停用其他平面。
* 剪下的平面是純粹的視覺特徵，它們不會影響 [空間查詢](spatial-queries.md)的結果。 如果您想要將光線轉換成切開的網格，可以將光線的起點調整為在切割平面上。 如此一來，光線就只能接觸可見的部分。

## <a name="performance-considerations"></a>效能考量

每個使用中的切割平面在轉譯期間都會產生小額成本。 在不需要時停用或刪除切割平面。

## <a name="api-documentation"></a>API 文件

* [C # CutPlaneComponent 類別](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.cutplanecomponent)
* [C + + CutPlaneComponent 類別](https://docs.microsoft.com/cpp/api/remote-rendering/cutplanecomponent)

## <a name="next-steps"></a>後續步驟

* [單面呈現](single-sided-rendering.md)
* [空間查詢](spatial-queries.md)
