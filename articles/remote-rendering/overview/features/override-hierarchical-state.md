---
title: 階層狀態覆寫
description: 解釋分層狀態覆蓋元件的概念。
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: f3be073857cc8583669ab26f306760478479e2ae
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680787"
---
# <a name="hierarchical-state-override"></a>階層狀態覆寫

在許多情況下,有必要動態更改[模型](../../concepts/models.md)各部分的外觀,例如隱藏子圖或將部分切換到透明渲染。 更改每個所涉及的部件的材料並不實際,因為它需要遍過整個場景圖,並管理每個節點上的材料克隆和分配。

要以盡可能少的花費完成此用例,請使用`HierarchicalStateOverrideComponent`。 此元件在場景圖的任意分支上實現分層狀態更新。 這意味著,可以在場景圖中的任何級別上定義狀態,並逐漸滴入層次結構,直到它被新狀態覆蓋或應用於葉物件。

例如,考慮汽車的型號,並且您想要將整輛車切換為透明,但發動機內部部件除外。 此用例僅涉及元件的兩個實體:

* 第一個元件被分配到模型的根節點,並打開整車的透明渲染。
* 第二個元件分配給引擎的根節點,並通過顯式關閉通路模式再次覆蓋狀態。

## <a name="features"></a>特性

可以重寫的固定狀態集是:

* **隱藏**: 場景圖中的相應模分被隱藏或顯示。
* **色調**:渲染的物件可以著色,其單獨的色調和色調權重。 下圖顯示了對車輪邊緣著色的顏色。
  
  ![色彩色調](./media/color-tint.png)

* **透明**:幾何體以半透明方式呈現,例如顯示對象的內部部分。 下圖顯示了整個車輛在看通模式下呈現,紅色制動卡鉗除外:

  ![檢視](./media/see-through.png)

  > [!IMPORTANT]
  > 僅當使用*基於"基於切片的合成"*[呈現模式](../../concepts/rendering-modes.md)時,透出效果才起作用。

* **選定**: 幾何體使用[選擇輪廓](outlines.md)呈現。

  ![選擇大綱](./media/selection-outline.png)

* **關閉碰撞**: 幾何體不受[空間查詢。](spatial-queries.md) **隱藏**標誌不會關閉衝突,因此這兩個標誌通常一起設置。

## <a name="hierarchical-overrides"></a>階層覆寫

`HierarchicalStateOverrideComponent`可以附加到物件層次結構的多個級別上。 由於實體上每種類型的元件只能有一個,因此每個`HierarchicalStateOverrideComponent`元件都管理隱藏、瀏覽、選擇、顏色色調和衝突狀態。

因此,可以將每個狀態設置為:

* `ForceOn`- 此節點上與下方的所有格線都啟用狀態
* `ForceOff`- 此節點上與下方的所有網路線都關閉狀態
* `InheritFromParent`- 狀態不受此覆寫元件的影響

您可以直接或透過`SetState`函數變更狀態:

```cs
HierarchicalStateOverrideComponent component = ...;

// set one state directly
component.HiddenState = HierarchicalEnableState.ForceOn;

// set a state with the SetState function
component.SetState(HierarchicalStates.SeeThrough, HierarchicalEnableState.InheritFromParent);

// set multiple states at once with the SetState function
component.SetState(HierarchicalStates.Hidden | HierarchicalStates.DisableCollision, HierarchicalEnableState.ForceOff);
```

### <a name="tint-color"></a>色調

色調覆蓋略為特殊,因為同時存在開/關/繼承狀態和色調屬性。 色調的 Alpha 部分定義著色效果的權重:如果設置為 0.0,則沒有可見色調,如果設置為 1.0,則物件將以純色調呈現。 對於中間值,最終顏色將與色調混合。 色調可以按幀更改,以實現顏色動畫。

## <a name="performance-considerations"></a>效能考量

本身的`HierarchicalStateOverrideComponent`實例不會增加太多的運行時開銷。 但是,保持活動元件數量少始終是一種好的做法。 例如,在實現突出顯示拾取物件的選擇系統時,建議在刪除高光時刪除元件。 使用中性功能保持元件可以快速累積。

與標準渲染相比,透明渲染在伺服器 GPU 上增加工作負載。 如果場景圖的大部分切換到 *「透光」,* 許多幾何層可見,則可能成為性能瓶頸。 對於具有[選擇輪廓](../../overview/features/outlines.md#performance)的物件,也是如此。

## <a name="next-steps"></a>後續步驟

* [概述](../../overview/features/outlines.md)
* [成像模式](../../concepts/rendering-modes.md)
* [空間查詢](../../overview/features/spatial-queries.md)
