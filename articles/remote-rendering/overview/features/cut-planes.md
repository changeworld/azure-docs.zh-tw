---
title: 切割平面
description: 說明什麼是切割平面以及如何使用
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: b92bfad99c854f75c945121d352a7122d8c6db89
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "89011594"
---
# <a name="cut-planes"></a>切割平面

「切割平面」是一種視覺化功能，可以在虛擬平面的一端裁剪像素，並在[網格](../../concepts/meshes.md)內顯示。
下圖示範效果。 左邊顯示的是原始網格，在右邊可以查看網格內部：

![切割平面](./media/cutplane-1.png)

## <a name="limitations"></a>限制

* 從現在起，Azure 遠端轉譯支援**最多八個使用中的切割平面**。 您可以建立更多切割平面元件，但是如果您嘗試同時啟用更多，則會忽略啟用。 如果您想要切換哪個元件應該影響場景，請先停用其他平面。
* 每個切割平面都會影響所有遠端轉譯物件。 目前沒有任何方法可以排除特定物件或網格部分。
* 切割平面純粹是一項視覺效果功能，不會影響[空間查詢](spatial-queries.md)的結果。 如果您想要將光線轉換成切開的網格，可以將光線的起點調整為在切割平面上。 如此一來，光線就只能接觸可見的部分。

## <a name="performance-considerations"></a>效能考量

每個使用中的切割平面在轉譯期間都會產生小額成本。 在不需要時停用或刪除切割平面。

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

## <a name="next-steps"></a>後續步驟

* [單面呈現](single-sided-rendering.md)
* [空間查詢](spatial-queries.md)
