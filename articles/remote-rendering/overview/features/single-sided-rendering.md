---
title: 單面呈現
description: 描述單面轉譯設定和使用案例
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: 34ee5d4978c6476da407cde33598a5713177078e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80682009"
---
# <a name="single-sided-rendering"></a>單面呈現

大部分轉譯器都會使用[背面的剔除](https://en.wikipedia.org/wiki/Back-face_culling)來改善效能。 不過，當網格以剪下[平面](cut-planes.md)開啟時，使用者通常會查看三角形的背面。 如果這些三角形挑選掉，結果就不會令人信服。

若要可靠地避免這個問題，就是*要雙向呈現三角形。* 因為不使用後端的剔除會影響效能，所以根據預設，Azure 遠端轉譯只會切換為與剪下平面相交之網格的雙側轉譯。

[*單面*轉譯] 設定可讓您自訂此行為。

> [!CAUTION]
> 「單面轉譯」設定是實驗性的功能。 未來可能會再次移除。 除非真的解決應用程式中的重大問題，否則請不要變更預設設定。

## <a name="prerequisites"></a>先決條件

單面轉譯設定只會影響已[轉換](../../how-tos/conversion/configure-model-conversion.md) `opaqueMaterialDefaultSidedness`選項設為`SingleSided`的網格。 根據預設，此選項設定為`DoubleSided`。

## <a name="single-sided-rendering-setting"></a>單面轉譯設定

有三種不同的模式：

**一般：** 在此模式中，網格一律會在轉換時轉譯。 這表示以`opaqueMaterialDefaultSidedness`設定為轉換的`SingleSided`網格，一律會以已啟用的背面剔除來轉譯，即使它們與剪下的平面相交也一樣。

**DynamicDoubleSiding：** 在此模式中，當切割平面與網格相交時，它會自動切換為雙面呈現。 此模式為預設模式。

**AlwaysDoubleSided：** 強制將所有的單側幾何隨時呈現。 此模式大多是公開的，因此您可以輕鬆地比較單面和雙面轉譯之間的效能影響。

變更單側轉譯設定的方式如下所示：

``` cs
void ChangeSingleSidedRendering(AzureSession session)
{
    SingleSidedSettings settings = session.Actions.SingleSidedSettings;

    // Single-sided geometry is rendered as is
    settings.Mode = SingleSidedMode.Normal;

    // Single-sided geometry is always rendered double-sided
    settings.Mode = SingleSidedMode.AlwaysDoubleSided;
}
```

## <a name="next-steps"></a>後續步驟

* [切割平面](cut-planes.md)
* [設定模型轉換](../../how-tos/conversion/configure-model-conversion.md)
