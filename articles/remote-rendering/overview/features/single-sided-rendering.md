---
title: 單面渲染
description: 描述單面繪圖設定與用例
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: 34ee5d4978c6476da407cde33598a5713177078e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80682009"
---
# <a name="single-sided-rendering"></a>單面渲染

大多數渲染器使用[背面剔除](https://en.wikipedia.org/wiki/Back-face_culling)來提高性能。 然而,當用[切割平面](cut-planes.md)切開的網外時,使用者通常會看到三角形的背面。 如果這些三角形被剔除,結果看起來並不令人信服。

可靠地防止此問題的方法是呈現雙*面*三角形。 由於不使用背面剔除會影響性能,因此默認情況下,Azure 遠端渲染僅切換到與切割平面相交的網間的雙面渲染。

*單面呈現*設置允許您自定義此行為。

> [!CAUTION]
> 單面渲染設置是一個實驗功能。 將來可能會再次刪除它。 請不要更改預設設置,除非它確實解決了應用程式中的關鍵問題。

## <a name="prerequisites"></a>Prerequisites

單面呈現設定僅對已[converted](../../how-tos/conversion/configure-model-conversion.md)轉換`opaqueMaterialDefaultSidedness`選項`SingleSided`設置為的網件具有效果。 預設情況下,這個選項設定為`DoubleSided`。

## <a name="single-sided-rendering-setting"></a>單面渲染設定

有三種不同的模式:

**正常:** 在此模式下,在轉換這些模樣時,始終呈現這些等點。 這意味著轉換到`opaqueMaterialDefaultSidedness``SingleSided`的 模樣將始終在啟用背面剔除的情況下呈現,即使它們與切割平面相交也是如此。

**動態雙襯:** 在此模式下,當剪切平面與網格相交時,它會自動切換到雙面渲染。 此模式為預設模式。

**始終雙面:** 強制所有單面幾何體在任何時候呈現雙面幾何。 此模式大多公開,因此您可以輕鬆比較單面渲染和雙面渲染之間的性能影響。

變更單面渲染設定可以如下所示:

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
