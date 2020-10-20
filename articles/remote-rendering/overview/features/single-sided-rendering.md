---
title: 單面呈現
description: 描述單面呈現設定和使用案例
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 37a665c776a64558a13910875f221462fb7d0ef8
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2020
ms.locfileid: "92205059"
---
# <a name="no-loc-textsingle-sided-rendering"></a>:::no-loc text="Single-sided"::: 渲染

大部分轉譯器會使用[背面剔除](https://en.wikipedia.org/wiki/Back-face_culling)來改善效能。 不過，當網格以[切割平面](cut-planes.md)的方式切開時，使用者通常會查看三角形的後端。 如果這些三角形剔除掉，結果就不會令人信服。

可靠地避免這個問題的方法就是「雙面」呈現三角形。 因為不使用背面剔除會影響效能，所以根據預設，Azure 遠端轉譯只會針對與切割平面有交集的網格切換為雙面呈現。

轉譯* :::no-loc text="single-sided"::: 設定可*讓您自訂這個行為。

> [!CAUTION]
> 轉譯 :::no-loc text="single-sided"::: 設定是實驗性功能。 未來可能會再次移除。 除非真的能解決應用程式中的重大問題，否則請不要變更預設設定。

## <a name="prerequisites"></a>Prerequisites

轉譯 :::no-loc text="single-sided"::: 設定只會影響已 [轉換](../../how-tos/conversion/configure-model-conversion.md) `opaqueMaterialDefaultSidedness` 選項設定為的網格 `SingleSided` 。 根據預設，這個選項會設為 `DoubleSided`。

## <a name="no-loc-textsingle-sided-rendering-setting"></a>:::no-loc text="Single-sided"::: 轉譯設定

有三個不同的模式︰

**一般：** 在此模式中，網格一律會在轉換時轉譯。 這表示使用設定為 `SingleSided` 的 `opaqueMaterialDefaultSidedness` 所轉換的網格，一律會在啟用背面剔除的情況下轉譯，即使與切割平面有交集也一樣。

**DynamicDoubleSiding:** 在此模式中，當切割平面與網格有交集時，會自動切換為雙面呈現。 此模式為預設模式。

**AlwaysDoubleSided：** 強制將所有單面幾何隨時成為雙面呈現。 這種模式大多是公開的，因此您可以輕鬆地比較和轉譯之間的效能影響 :::no-loc text="single-sided"::: :::no-loc text="double-sided"::: 。

您可以依照下列方式來變更轉譯 :::no-loc text="single-sided"::: 設定：

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
    ApiHandle<SingleSidedSettings> settings = session->Actions()->GetSingleSidedSettings();

    // Single-sided geometry is rendered as is
    settings->SetMode(SingleSidedMode::Normal);

    // Single-sided geometry is always rendered double-sided
    settings->SetMode(SingleSidedMode::AlwaysDoubleSided);
}
```

## <a name="api-documentation"></a>API 文件

* [C # RemoteManager. SingleSidedSettings 屬性](/dotnet/api/microsoft.azure.remoterendering.remotemanager.singlesidedsettings)
* [C + + RemoteManager：： SingleSidedSettings ( # B1 ](/cpp/api/remote-rendering/remotemanager#singlesidedsettings)

## <a name="next-steps"></a>後續步驟

* [切割平面](cut-planes.md)
* [設定模型轉換](../../how-tos/conversion/configure-model-conversion.md)