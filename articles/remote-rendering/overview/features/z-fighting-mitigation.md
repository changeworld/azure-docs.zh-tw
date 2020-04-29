---
title: Z-fighting 緩和措施
description: 說明用來減輕 z 對抗成品的技術
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: bc06deafe3f589fce9a9178fefdb22388254929d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80680449"
---
# <a name="z-fighting-mitigation"></a>Z-fighting 緩和措施

當兩個表面重迭時，不清楚哪一個呈現在另一個介面上。 結果甚至會因為每個圖元而有所不同，因而產生了視圖相依的成品。 因此，當相機或網格移動時，這些模式會明顯閃爍。 此成品稱為「 *z 對抗*」。 對於 AR 和 VR 應用程式，問題是更的，因為 head 掛接的裝置自然地會移動。 若要防止檢視器 discomfort 的 z 對抗緩和功能，可在 Azure 遠端轉譯中取得。

## <a name="z-fighting-mitigation-modes"></a>Z 對抗風險降低模式

|實際                        | 結果                               |
|---------------------------------|:-------------------------------------|
|一般的 z 對抗               |![Z-對抗](./media/zfighting-0.png)|
|已啟用 Z 對抗緩和措施    |![Z-對抗](./media/zfighting-1.png)|
|已啟用棋盤反白顯示|![Z-對抗](./media/zfighting-2.png)|

下列程式碼可啟用 z 對抗緩和措施：

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
> Z 對抗緩和是一種全域設定，會影響所有呈現的網格。

## <a name="reasons-for-z-fighting"></a>Z 對抗的原因

Z 進行操作主要有兩個原因：

1. 當表面距離相機非常遠時，其深度值的精確度會降低，且值會變得無法區分
1. 當網格中的表面實際重迭時

第一個問題總是可能發生，而且很容易消除。 如果在您的應用程式中發生這種情況，請確定*近平面*距離到最接近平面距離的比率低於實際*程度*。 例如，距離0.01 的近平面和距離1000的遠平面，會更早地建立此問題，而不會有接近的平面位於0.1，而最遠的平面在距離20。

第二個問題是錯誤撰寫內容的指標。 在真實世界中，兩個物件不能同時位於相同的位置。 視應用程式而定，使用者可能想要知道重迭的表面是否存在，以及它們的所在位置。 例如，建築物的 CAD 場景是真實世界結構的基礎，不應包含實體不可能的表面交集。 若要允許視覺效果檢查，可使用醒目提示模式，這會以動畫棋盤模式顯示可能的 z 對抗。

## <a name="limitations"></a>限制

所提供的 z 對抗風險降低是最佳的工作。 並不保證會移除所有的 z 對抗。 此外，它也會自動偏好一個表面。 因此，當您的表面太接近彼此時，可能會發生「錯誤」的表面結束。 常見的問題是當文字和其他 decals 套用至介面時。 啟用「z 對抗」緩和功能之後，就可以輕鬆地消失這些詳細資料。

## <a name="performance-considerations"></a>效能考量

* 啟用 z 對抗緩和措施，幾乎不會產生任何效能負擔。
* 此外，啟用 z 操作重迭也會產生非一般的效能額外負荷，不過這可能會因場景而異。

## <a name="next-steps"></a>後續步驟

* [轉譯模式](../../concepts/rendering-modes.md)
* [延遲階段重新投影](late-stage-reprojection.md)
