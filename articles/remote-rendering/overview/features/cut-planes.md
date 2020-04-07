---
title: 切割平面
description: 解釋什麼是切割平面以及如何使用它們
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: 8075d9cd4530bafb12a338830baf0fe22eb03bce
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681021"
---
# <a name="cut-planes"></a>切割平面

*剪切平面*是一種視覺特徵,用於剪輯虛擬平面一側的圖元,揭示[網](../../concepts/meshes.md)內。
下圖演示了效果。 左方顯示原始格線,右側可以檢視格線內部:

![切割平面](./media/cutplane-1.png)

## <a name="limitations"></a>限制

* 目前,Azure 遠端呈現最多支援八個**活動切割平面**。 您可以創建更多切割平面元件,但如果嘗試同時啟用更多,它將忽略啟動。 如果要切換要影響場景的元件,請先禁用其他平面。
* 每個切割平面都會影響所有遠程渲染的物件。 目前無法排除特定物件或網格部分。
* 剪切平面純粹是視覺特徵,它們不會影響[空間查詢](spatial-queries.md)的結果。 如果確實希望將鑄入剪切開放網格的光線,則可以調整光線的起始點以位於切割平面上。 這樣,光線只能擊中可見部分。

## <a name="performance-considerations"></a>效能考量

每個活動切割平面在渲染過程中會產生少量成本。 在不需要切割平面時禁用或刪除它們。

## <a name="cutplanecomponent"></a>切割平面元件

通過創建*CutPlane 元件*,向場景添加剪切平面。 平面的位置和方向由元件的所有者[實體](../../concepts/entities.md)決定。

```cs
void CreateCutPlane(AzureSession session, Entity ownerEntity)
{
    CutPlaneComponent cutPlane = (CutPlaneComponent)session.Actions.CreateComponent(ObjectType.CutPlaneComponent, ownerEntity);
    cutPlane.Normal = Axis.X; // normal points along the positive x-axis of the owner object's orientation
    cutPlane.FadeColor = new Color4Ub(255, 0, 0, 128); // fade to 50% red
    cutPlane.FadeLength = 0.05f; // gradient width: 5cm
}
```

### <a name="cutplanecomponent-properties"></a>切割平面元件屬性

以下屬性在切割平面元件上公開:

* **已開啟:** 您可以通過禁用元件暫時關閉切割平面。 禁用的切割平面不會產生渲染開銷,也不會計入全域切割平面限制。

* **正常:** 指定使用哪個方向(+X、-X、+Y、Y、+Z、-Z)作為平面法線。 此方向與所有者實體的方向相關。 移動和旋轉擁有者實體以進行精確放置。

* **淡入淡出顏色**與**淡入淡出長度:**

  如果*淡入淡出顏色*的 alpha 值是非零,則靠近剪切平面的圖元將淡入淡入 FadeColor 的 RGB 部分。 Alpha 通道的強度決定了它是完全褪色還是僅部分淡入淡出。 *淡入淡出符定義*此淡入淡出將發生的距離。

## <a name="next-steps"></a>後續步驟

* [單面渲染](single-sided-rendering.md)
* [空間查詢](spatial-queries.md)
