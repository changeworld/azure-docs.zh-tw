---
title: 切割平面
description: 說明什麼是剪下的平面以及如何使用它們
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: 8075d9cd4530bafb12a338830baf0fe22eb03bce
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681021"
---
# <a name="cut-planes"></a>切割平面

「*剪下平面*」是一種視覺化功能，可在虛擬平面的一端裁剪圖元，並在[網格](../../concepts/meshes.md)內部展現。
下圖示范效果。 左邊顯示的是原始的網格，在右邊可以在網格內查看：

![剪下平面](./media/cutplane-1.png)

## <a name="limitations"></a>限制

* 在這段時間內，Azure 遠端轉譯**最多支援八個作用中的剪下平面**。 您可以建立更多剪下平面元件，但如果您嘗試同時啟用更多，則會忽略啟用。 如果您想要切換哪個元件應該影響場景，請先停用其他平面。
* 每個剪下平面都會影響所有遠端呈現的物件。 目前沒有任何方法可以排除特定物件或網格元件。
* 剪下平面純粹是一項視覺功能，不會影響[空間查詢](spatial-queries.md)的結果。 如果您想要將光線轉換成剪下的網格，可以將光線的起點調整為切割平面上的頂點。 如此一來，光線就只能叫用可見的部分。

## <a name="performance-considerations"></a>效能考量

每個作用中的剪下平面在轉譯期間都會產生較小的成本。 不需要時停用或刪除切割飛機。

## <a name="cutplanecomponent"></a>CutPlaneComponent

藉由建立*CutPlaneComponent*，您可以將剪下平面加入場景中。 平面的位置和方向取決於元件的擁有者[實體](../../concepts/entities.md)。

```cs
void CreateCutPlane(AzureSession session, Entity ownerEntity)
{
    CutPlaneComponent cutPlane = (CutPlaneComponent)session.Actions.CreateComponent(ObjectType.CutPlaneComponent, ownerEntity);
    cutPlane.Normal = Axis.X; // normal points along the positive x-axis of the owner object's orientation
    cutPlane.FadeColor = new Color4Ub(255, 0, 0, 128); // fade to 50% red
    cutPlane.FadeLength = 0.05f; // gradient width: 5cm
}
```

### <a name="cutplanecomponent-properties"></a>CutPlaneComponent 屬性

下列屬性會在剪下的平面元件上公開：

* **已啟用：** 您可以藉由停用元件，暫時關閉剪下飛機。 停用的剪下平面不會產生轉譯額外負荷，也不會計算全域剪下的限制。

* **一般：** 指定要使用哪一個方向（+ X，-X，+ Y，-Y，+ Z，-Z）作為平面的法線。 這個方向相對於擁有者實體的方向。 移動和旋轉擁有者實體以取得確切的位置。

* **FadeColor**和**FadeLength：**

  如果*FadeColor*的 Alpha 值為非零，接近切割平面的圖元會淡入 FADECOLOR 的 RGB 部分。 Alpha 色板的強度會決定是否要完全淡入淡出色彩或僅部分。 *FadeLength*會定義此淡入進行的距離。

## <a name="next-steps"></a>後續步驟

* [單面呈現](single-sided-rendering.md)
* [空間查詢](spatial-queries.md)
