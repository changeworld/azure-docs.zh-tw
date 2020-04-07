---
title: Z 戰鬥緩解
description: 描述緩解 z 戰鬥偽影的技術
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: bc06deafe3f589fce9a9178fefdb22388254929d
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680449"
---
# <a name="z-fighting-mitigation"></a>Z 戰鬥緩解

當兩個曲面重疊時,不清楚哪一個應呈現在另一個曲面的頂部。 結果甚至因圖元而異,從而導致與視圖相關的偽影。 因此,當攝像機或網格移動時,這些模式會明顯閃爍。 此偽影稱為*z 戰鬥*。 對於 AR 和 VR 應用,問題更加強化,因為頭戴式設備自然會移動。 為了防止觀看者不適,Azure 遠程渲染中提供了 z 防功能緩解功能。

## <a name="z-fighting-mitigation-modes"></a>Z 防震緩解模式

|情況                        | 結果                               |
|---------------------------------|:-------------------------------------|
|定期 z 戰鬥               |![Z 戰鬥](./media/zfighting-0.png)|
|Z 戰鬥緩解啟用    |![Z 戰鬥](./media/zfighting-1.png)|
|開啟棋盤突顯|![Z 戰鬥](./media/zfighting-2.png)|

以下代碼支援 z 防爭緩解:

``` cs
void EnableZFightingMitigation(AzureSession session, bool highlight)
{
    ZFightingMitigationSettings settings = session.Actions.ZFightingMitigationSettings;

    // enabling z-fighting mitigation
    settings.Enabled = true;

    // enabling checkerboard highlighting of z-fighting potential
    settings.Highlighting = highlight;
}
```

> [!NOTE]
> Z 防爭緩解是影響所有呈現的網件的全域設置。

## <a name="reasons-for-z-fighting"></a>z 戰鬥的原因

Z 戰鬥發生主要有兩個原因:

1. 當曲面遠離相機時,其深度值的精度會降低,並且這些值變得無法區分
1. 當格線中的曲面物理重疊時

第一個問題總是會發生,而且很難消除。 如果應用程式中發生這種情況,請確保*近平面*距離與*遠平面*距離的比率盡可能低。 例如,距離 0.01 處的近平面和距離 1000 處的遠平面將比在 0.1 處的近平面和距離 20 處的遠平面更早產生此問題。

第二個問題是編寫不良內容的指示器。 在現實世界中,兩個物件不能同時位於同一位置。 根據應用程式的不同,使用者可能想知道是否存在重疊曲面及其位置。 例如,作為真實世界結構基礎的建築物的 CAD 場景不應包含物理上不可能的表面交點。 為了便於目視檢查,突出顯示模式可用,該模式將潛在的 z 戰鬥顯示為動畫棋盤圖案。

## <a name="limitations"></a>限制

提供的 z 戰鬥緩解是最大的努力。 不能保證它消除所有 z 戰鬥。 此外,它會自動首選一個曲面而不是另一個曲面。 因此,當您的曲面彼此太近時,可能會出現"錯誤"曲面最終位於頂部。 一個常見的問題是文本和其他貼圖應用於曲面時。 啟用 z 戰鬥緩解功能后,這些細節很容易消失。

## <a name="performance-considerations"></a>效能考量

* 啟用 z 防功能緩解只需很少或根本沒有性能開銷。
* 此外,啟用 z 戰鬥疊加確實會產生非同尋常的性能開銷,儘管它可能因場景而異。

## <a name="next-steps"></a>後續步驟

* [成像模式](../../concepts/rendering-modes.md)
* [後期重新投影](late-stage-reprojection.md)
