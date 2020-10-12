---
title: 階層式狀態覆寫
description: 說明階層式狀態覆寫元件的概念。
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: c098dc6b1d3b41a41246857f8a353dd4f5dfcef1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90884176"
---
# <a name="hierarchical-state-override"></a>階層式狀態覆寫

在許多情況下，您必須以動態方式變更 [模型](../../concepts/models.md)部分的外觀，例如隱藏子圖形或將零件切換成透明轉譯。 變更每個相關組件的材質並不實用，因為這種方式需要逐一查看整個場景圖形，並管理每個節點上的材質複製和指派。

若要儘可能以最少的額外負荷來完成此使用案例，請使用 `HierarchicalStateOverrideComponent`。 此元件會在場景圖形的任意分支上，執行階層式狀態更新。 這表示，您可以在場景圖形的任何層級上定義狀態，並在階層中向下分流，直到新狀態加以覆寫或套用到分葉物件為止。

我們以汽車模型作為例子，您想要將整輛汽車切換為透明模式，但內部引擎部分除外。 此使用案例只牽涉到元件的兩個執行個體：

* 第一個元件會指派給模型的根節點，並讓整輛汽車以透明方式轉譯。
* 第二個元件會指派給引擎的根節點，並藉由明確關閉透視模式來覆寫狀態。

## <a name="features"></a>特性

可以覆寫的一組固定狀態為：

* **`Hidden`**：場景圖形中的個別網格會隱藏或顯示。
* **`Tint color`**：轉譯的物件可以色彩色彩，以及其個別的色調色彩和色調粗細。 下圖顯示滾輪邊緣的濃淡色彩。
  
  ![用來將物件變成綠色的色調色彩](./media/color-tint.png)

* **`See-through`**：幾何會以半透明方式轉譯，例如，用來顯示物件的內部部分。 下圖顯示除了紅色的剎車制動器之外，以透視模式轉譯的整輛汽車：

  ![查看用來讓選取的物件變成透明的模式](./media/see-through.png)

  > [!IMPORTANT]
  > 只有在使用 *TileBasedComposition* [轉譯模式](../../concepts/rendering-modes.md) 時，才可使用透視效果。

* **`Selected`**：幾何會以 [選取範圍大綱](outlines.md)呈現。

  ![用來反白顯示所選元件的大綱選項](./media/selection-outline.png)

* **`DisableCollision`**：幾何豁免于 [空間查詢](spatial-queries.md)。 **`Hidden`** 旗標不會影響衝突狀態旗標，因此這兩個旗標通常會一起設定。

* **`UseCutPlaneFilterMask`**：使用個別的篩選位遮罩來控制剪下的平面選取範圍。 此旗標會決定是否應該使用個別篩選遮罩，或從其父系繼承。 篩選位遮罩本身是透過 `CutPlaneFilterMask` 屬性設定。 如需篩選運作方式的詳細資訊，請參閱「 [選擇性剪下平面」段落](cut-planes.md#selective-cut-planes)。
![選擇性剪下平面](./media/selective-cut-planes.png)


> [!TIP]
> 除了關閉完整子圖表的可見度和空間查詢以外， `enabled` 還可以切換遊戲物件的狀態。 如果已停用階層，這就會優先使用任何 `HierarchicalStateOverrideComponent` 。

## <a name="hierarchical-overrides"></a>階層式覆寫

`HierarchicalStateOverrideComponent` 可以附加在物件階層的多個層級上。 因為實體上的每個類型都只能有一個元件，所以每個 `HierarchicalStateOverrideComponent` 都會管理隱藏、透視、已選取、濃淡色彩和衝突等狀態。

因此，每個狀態都可以設定為下列其中一種：

* `ForceOn` - 此節點之上和之下的所有網格皆為啟用狀態
* `ForceOff` - 此節點之上和之下的所有網格皆為停用狀態
* `InheritFromParent` - 狀態不受此覆寫元件影響

您可以直接變更狀態，或透過 `SetState` 函式變更：

```cs
HierarchicalStateOverrideComponent component = ...;

// set one state directly
component.HiddenState = HierarchicalEnableState.ForceOn;

// set a state with the SetState function
component.SetState(HierarchicalStates.SeeThrough, HierarchicalEnableState.InheritFromParent);

// set multiple states at once with the SetState function
component.SetState(HierarchicalStates.Hidden | HierarchicalStates.DisableCollision, HierarchicalEnableState.ForceOff);
```

```cpp
ApiHandle<HierarchicalStateOverrideComponent> component = ...;

// set one state directly
component->SetHiddenState(HierarchicalEnableState::ForceOn);

// or: set a state with the SetState function
component->SetState(HierarchicalStates::SeeThrough, HierarchicalEnableState::InheritFromParent);

// set multiple states at once with the SetState function
component->SetState(
    (HierarchicalStates)((int32_t)HierarchicalStates::Hidden | (int32_t)HierarchicalStates::DisableCollision), HierarchicalEnableState::ForceOff);

```

### <a name="tint-color"></a>濃淡色彩

覆 `tint color` 寫有點特殊，因為兩者都有開啟/關閉/繼承狀態和淡色色彩屬性。 濃淡色彩的 Alpha 部分會定義色調效果的權重：如果設為 0.0，則不會顯示濃淡色彩；如果設定為 1.0，則會以純濃淡色彩轉譯物件。 若為介於中間的值，最終色彩會與濃淡色彩混合。 您可以變更每個畫面的濃淡色彩，以完成色彩動畫。

## <a name="performance-considerations"></a>效能考量

`HierarchicalStateOverrideComponent` 本身的執行個體並不會增加太多執行階段的額外負荷。 不過，將作用中元件的數目維持在較低的狀態，一般來說是很好的作法。 例如，當實作反白顯示所挑選物件的選取範圍系統時，建議您在移除反白顯示時刪除該元件。 在元件周圍使用中性功能，可快速增加。

相較於標準轉譯，透明轉譯現會加重伺服器 CPU 的工作負載。 如果場景圖形的大型組件切換成*透視*，而且可以看見多層的幾何，則可能會變成效能瓶頸。 對於具有[選取範圍外框](../../overview/features/outlines.md#performance)的物件而言，這也是有效的作法。

## <a name="api-documentation"></a>API 文件

* [C # HierarchicalStateOverrideComponent 類別](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.hierarchicalstateoverridecomponent)
* [C + + HierarchicalStateOverrideComponent 類別](https://docs.microsoft.com/cpp/api/remote-rendering/hierarchicalstateoverridecomponent)

## <a name="next-steps"></a>後續步驟

* [外框](../../overview/features/outlines.md)
* [轉譯模式](../../concepts/rendering-modes.md)
* [空間查詢](../../overview/features/spatial-queries.md)
