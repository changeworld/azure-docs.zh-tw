---
title: 階層式狀態覆寫
description: 說明階層式狀態覆寫元件的概念。
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: f3be073857cc8583669ab26f306760478479e2ae
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80680787"
---
# <a name="hierarchical-state-override"></a>階層式狀態覆寫

在許多情況下，必須動態變更[模型](../../concepts/models.md)部分的外觀，例如隱藏 subgraphs 或將元件切換成透明呈現。 變更每個相關元件的材質並不實用，因為它需要逐一查看整個場景圖形，並管理每個節點上的材質複製和指派。

若要以最少可能的額外負荷來完成此使用`HierarchicalStateOverrideComponent`案例，請使用。 此元件會在場景圖形的任意分支上，執行階層式狀態更新。 這表示，您可以在場景圖形的任何層級上定義狀態，並在階層中向下分流，直到它被新狀態覆寫或套用到分葉物件為止。

例如，假設汽車的型號，而且您想要將整個汽車切換為透明，但內部引擎部分除外。 此使用案例只牽涉到元件的兩個實例：

* 第一個元件會指派給模型的根節點，並開啟整個車輛的透明呈現。
* 第二個元件會指派給引擎的根節點，並藉由明確關閉 [查看] 模式來覆寫狀態。

## <a name="features"></a>功能

可以覆寫的一組固定狀態為：

* **Hidden**：場景圖形中的個別網格會隱藏或顯示。
* **色調色彩**：轉譯的物件可以使用其個別色調色彩和色調粗細，以色彩顏色呈現。 下圖顯示滾輪邊緣的色彩著色。
  
  ![色彩色調](./media/color-tint.png)

* **流覽**：幾何會以半透明的方式呈現，例如，用來顯示物件的內部部分。 下圖顯示以 [查看] 模式轉譯的整個汽車，但 red 剎車 caliper 除外：

  ![查看-流覽](./media/see-through.png)

  > [!IMPORTANT]
  > 只有在使用*TileBasedComposition*轉譯模式時，才會有 [查看[]](../../concepts/rendering-modes.md)效果。

* 已**選取**： geometry 會以[選取範圍外框](outlines.md)來呈現。

  ![選取範圍外框](./media/selection-outline.png)

* **DisableCollision**： geometry 不會受到[空間查詢](spatial-queries.md)的豁免。 **隱藏**旗標不會關閉衝突，因此通常會將這兩個旗標設定在一起。

## <a name="hierarchical-overrides"></a>階層式覆寫

`HierarchicalStateOverrideComponent`可以附加在物件階層的多個層級上。 因為實體上的每個類型都只能有一個元件，所以每`HierarchicalStateOverrideComponent`個都可管理隱藏的狀態、已選取的流覽、已選取、色彩色調和衝突。

因此，每個狀態都可以設定為下列其中一個：

* `ForceOn`-此節點上和下方的所有網格皆啟用狀態
* `ForceOff`-此節點上和下方的所有網格都會停用狀態
* `InheritFromParent`-狀態不受此覆寫元件影響

您可以直接或透過`SetState`函式變更狀態：

```cs
HierarchicalStateOverrideComponent component = ...;

// set one state directly
component.HiddenState = HierarchicalEnableState.ForceOn;

// set a state with the SetState function
component.SetState(HierarchicalStates.SeeThrough, HierarchicalEnableState.InheritFromParent);

// set multiple states at once with the SetState function
component.SetState(HierarchicalStates.Hidden | HierarchicalStates.DisableCollision, HierarchicalEnableState.ForceOff);
```

### <a name="tint-color"></a>色調色彩

色調色彩覆寫有點特殊，因為有開啟/關閉/繼承狀態和色調色彩屬性。 色調色彩的 Alpha 部分會定義色彩效果的粗細：如果設定為0.0，則不會顯示色調色彩，如果設定為1.0，則會以純色調色彩呈現物件。 若為介於中的值，最終色彩會與色調色彩混合。 您可以變更每個畫面格的色調色彩，以達到色彩動畫。

## <a name="performance-considerations"></a>效能考量

實例`HierarchicalStateOverrideComponent`本身並不會增加太多執行時間額外負荷。 不過，將作用中元件的數目維持在較低的狀態，是很好的作法。 例如，當您執行反白顯示所挑選物件的選取系統時，建議您在移除醒目提示時刪除該元件。 使用中性功能來保持元件周圍，可以快速增加。

透明轉譯會在伺服器的 Gpu 上放置比標準轉譯更多的工作負載。 如果場景圖形的大型部分已切換為可*看到*，而且有多層的幾何可見，則可能會變成效能瓶頸。 對具有[選取範圍外框](../../overview/features/outlines.md#performance)的物件而言，相同的也是有效的。

## <a name="next-steps"></a>後續步驟

* [描述](../../overview/features/outlines.md)
* [轉譯模式](../../concepts/rendering-modes.md)
* [空間查詢](../../overview/features/spatial-queries.md)
