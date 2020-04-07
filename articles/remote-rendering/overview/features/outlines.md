---
title: 輪廓渲染
description: 說明如何選擇輪廓
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 8b52dbe8cd12e51c42677ce37acbd57ad551ec50
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680826"
---
# <a name="outline-rendering"></a>輪廓渲染

通過[分層狀態覆蓋元件](../../overview/features/override-hierarchical-state.md)添加輪廓渲染,可以直觀地突出顯示選定的物件。 本章介紹如何通過用戶端 API 更改大綱呈現的全域參數。

大綱屬性是全域設置。 使用輪廓渲染的所有物件都將使用相同的設置 - 無法使用每個物件的輪廓顏色。

## <a name="parameters-for-outlinesettings"></a>參數`OutlineSettings`

類`OutlineSettings`包含與全域大綱屬性相關的設置。 它公開以下成員:

| 參數      | 類型    | 描述                                             |
|----------------|---------|---------------------------------------------------------|
| `Color`          | 顏色4Ub | 用於繪製輪廓的顏色。 將忽略 Alpha 部分。         |
| `PulseRateHz`    | FLOAT   | 輪廓每秒振蕩的速度|
| `PulseIntensity` | FLOAT   | 輪廓脈衝效果的強度。 無脈動必須介於 0.0 之間,完全脈動必須介於 1.0 之間。 強度隱式將輪廓的最小不一度設定`MinOpacity = 1.0 - PulseIntensity`為 。 |

![大綱](./media/outlines.png)`color`將 參數從黃色(左)更改為品紅色(中心`pulseIntensity`)和從 0 到 0.8(右)的效果。

## <a name="example"></a>範例

以下代碼顯示了透過 API 設定大綱參數的範例:

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

大綱渲染可能對渲染性能產生重大影響。 此影響因給定幀的選定物件和非選定對象之間的螢幕空間空間關係而異。

## <a name="next-steps"></a>後續步驟

* [階層狀態覆蓋元件](../../overview/features/override-hierarchical-state.md)
