---
title: 單面呈現
description: 描述單面呈現設定和使用案例
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: 97e0456e274adee7d678e373cfd92b5003f3d801
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83759093"
---
# <a name="single-sided-rendering"></a>單面呈現

大部分轉譯器會使用[背面剔除](https://en.wikipedia.org/wiki/Back-face_culling)來改善效能。 不過，當網格以[切割平面](cut-planes.md)的方式切開時，使用者通常會查看三角形的後端。 如果這些三角形剔除掉，結果就不會令人信服。

可靠地避免這個問題的方法就是「雙面」呈現三角形。 因為不使用背面剔除會影響效能，所以根據預設，Azure 遠端轉譯只會針對與切割平面有交集的網格切換為雙面呈現。

*單面呈現*設定可讓您自訂此行為。

> [!CAUTION]
> 單面呈現設定是實驗性的功能。 未來可能會再次移除。 除非真的能解決應用程式中的重大問題，否則請不要變更預設設定。

## <a name="prerequisites"></a>Prerequisites

單面呈現設定只會影響已[轉換](../../how-tos/conversion/configure-model-conversion.md)，且 `opaqueMaterialDefaultSidedness` 選項設定為 `SingleSided` 的網格。 根據預設，這個選項會設為 `DoubleSided`。

## <a name="single-sided-rendering-setting"></a>單面呈現設定

有三個不同的模式︰

**一般：** 在此模式中，網格一律會在轉換時轉譯。 這表示使用設定為 `SingleSided` 的 `opaqueMaterialDefaultSidedness` 所轉換的網格，一律會在啟用背面剔除的情況下轉譯，即使與切割平面有交集也一樣。

**DynamicDoubleSiding:** 在此模式中，當切割平面與網格有交集時，會自動切換為雙面呈現。 此模式為預設模式。

**AlwaysDoubleSided：** 強制將所有單面幾何隨時成為雙面呈現。 此模式大多是公開的，因此您可以輕鬆地比較單面和雙面呈現之間的效能影響。

變更單面呈現設定的方式如下所示：

```cs
void ChangeSingleSidedRendering(AzureSession session)
{
    SingleSidedSettings settings = session.Actions.SingleSidedSettings;

    // Single-sided geometry is rendered as is
    settings.Mode = SingleSidedMode.Normal;

    // Single-sided geometry is always rendered double-sided
    settings.Mode = SingleSidedMode.AlwaysDoubleSided;
}
```

```cpp
void ChangeSingleSidedRendering(ApiHandle<AzureSession> session)
{
    ApiHandle<SingleSidedSettings> settings = *session->Actions()->SingleSidedSettings();

    // Single-sided geometry is rendered as is
    settings->Mode(SingleSidedMode::Normal);

    // Single-sided geometry is always rendered double-sided
    settings->Mode(SingleSidedMode::AlwaysDoubleSided);
}
```

## <a name="next-steps"></a>後續步驟

* [切割平面](cut-planes.md)
* [設定模型轉換](../../how-tos/conversion/configure-model-conversion.md)
