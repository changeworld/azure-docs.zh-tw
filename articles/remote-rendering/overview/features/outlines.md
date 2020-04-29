---
title: 外框呈現
description: 說明如何執行選取範圍大綱呈現
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 8b52dbe8cd12e51c42677ce37acbd57ad551ec50
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80680826"
---
# <a name="outline-rendering"></a>外框呈現

選取的物件可以透過「階層式狀態覆寫」[元件](../../overview/features/override-hierarchical-state.md)加入大綱轉譯，以視覺化方式反白顯示。 本章說明如何透過用戶端 API 變更大綱呈現的全域參數。

大綱屬性是全域設定。 所有使用大綱呈現的物件都將使用相同的設定-不可能使用每個物件的外框色彩。

## <a name="parameters-for-outlinesettings"></a>的參數`OutlineSettings`

類別`OutlineSettings`會保存與全域大綱屬性相關的設定。 它會公開下列成員：

| 參數      | 類型    | 描述                                             |
|----------------|---------|---------------------------------------------------------|
| `Color`          | Color4Ub | 用於繪製外框的色彩。 忽略 Alpha 部分。         |
| `PulseRateHz`    | FLOAT   | 每秒大綱 oscillates 的速率|
| `PulseIntensity` | FLOAT   | 外框脈衝效果的濃度。 必須介於0.0 之間，才不會有任何閃爍，而是1.0 以進行完整的閃爍。 濃度會隱含地將外框的最小`MinOpacity = 1.0 - PulseIntensity`不透明度設定為。 |

![概述](./media/outlines.png)將`color`參數從黃色（左）變更為洋紅（中間）和`pulseIntensity`從0到0.8 （右）的效果。

## <a name="example"></a>範例

下列程式碼示範透過 API 設定外框參數的範例：

``` cs
void SetOutlineParameters(AzureSession session)
{
    OutlineSettings outlineSettings = session.Actions.OutlineSettings;
    outlineSettings.Color = new Color4Ub(255, 255, 0, 255);
    outlineSettings.PulseRateHz = 2.0f;
    outlineSettings.PulseIntensity = 0.5f;
}
```

## <a name="performance"></a>效能

外框轉譯可能會對轉譯效能造成重大影響。 這種影響會因特定框架的選取和非選取物件之間的螢幕空間空間關聯而異。

## <a name="next-steps"></a>後續步驟

* [階層式狀態覆寫元件](../../overview/features/override-hierarchical-state.md)
