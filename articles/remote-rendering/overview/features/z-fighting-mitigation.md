---
title: Z-fighting 緩和措施
description: 說明用來緩和 Z-fighting 成品的技術
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: e7550d0f997182b3938285f1d0a360a31bf05177
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2020
ms.locfileid: "92207456"
---
# <a name="z-fighting-mitigation"></a>Z-fighting 緩和措施

當兩個表面重疊時，會不確定應將哪個表面轉譯在另一個表面上。 結果甚至會因圖元而異，因此會產生與相機視圖相依的構件。 因此當相機或網格移動時，圖樣會明顯閃爍。 這樣的成品稱為「Z-fighting」。 AR 和 VR 應用程式會加重這種問題，這是因為頭戴式裝置會原本就會持續移動。 為了預防檢視人員身體不適，Azure 遠端轉譯提供 Z-fighting 緩和功能。

## <a name="z-fighting-mitigation-modes"></a>Z-fighting 緩和措施模式

|情況                        | 結果                               |
|---------------------------------|:-------------------------------------|
|一般 Z-fighting               |![Red 和綠四邊形之間沒有決定性的優先順序](./media/zfighting-0.png)|
|啟用 Z-fighting 緩和措施    |![紅色四個優先](./media/zfighting-1.png)|
|啟用棋盤式反白顯示|![棋盤模式的紅色和綠色四切換喜好設定](./media/zfighting-2.png)|

下列程式碼可啟用 Z-fighting 緩和措施：

```cs
void EnableZFightingMitigation(AzureSession session, bool highlight)
{
    ZFightingMitigationSettings settings = session.Actions.ZFightingMitigationSettings;

    // enabling z-fighting mitigation
    settings.Enabled = true;

    // enabling checkerboard highlighting of z-fighting potential
    settings.Highlighting = highlight;
}
```

```cpp
void EnableZFightingMitigation(ApiHandle<AzureSession> session, bool highlight)
{
    ApiHandle<ZFightingMitigationSettings> settings = session->Actions()->GetZFightingMitigationSettings();

    // enabling z-fighting mitigation
    settings->SetEnabled(true);

    // enabling checkerboard highlighting of z-fighting potential
    settings->SetHighlighting(highlight);
}
```

> [!NOTE]
> Z-fighting 緩和措施是一種全域設定，會影響所有轉譯的網格。

## <a name="reasons-for-z-fighting"></a>Z-fighting 的原因

Z-fighting 主要有兩種發生原因：

1. 當表面距離相機非常遠時，其深度值的精確度會降低，且數值會變得無法區分
1. 當網格中的表面實際重疊時

第一個問題經常發生，而且難以完全解決。 如果您的應用程式發生這種情況，請盡可能減少「接近平面」距離到「遠離平面」距離的比例。 舉例來說，相較於接近平面距離為 0.1、遠離平面距離為 20，接近平面距離為 0.01、遠離平面距離為 1000 時，此問題會更快發生。

第二個問題則代表內容製作不良。 在真實世界中，兩個物件不能同時位於相同位置。 視應用程式而定，使用者可能會想知道是否有重疊表面存在，以及重疊表面的所在位置。 例如，建築物的 CAD 場景以真實世界的建物為基礎，所以不應含有實際不可能ˊ在的表面交集。 若要允許進行視覺效果檢查，有醒目提示模式可供使用，此模式會以動畫棋盤圖樣顯示可能的 Z-fighting。

## <a name="limitations"></a>限制

所提供的 Z-fighting 緩和措施是盡量達到目標的做法， 不保證能解決所有 Z-fighting 的情況。 此外，這種措施會自動偏好其中一個表面。 因此當表面彼此過於接近時，可能會發生「錯誤」的表面疊在上方的情況。 常見的問題案例之一，是發生在文字和其他裝置材質套用到表面時。 啟用 Z-fighting 緩和措施之後，這類細節可能很容易消失不見。

## <a name="performance-considerations"></a>效能考量

* 啟用 Z-fighting 幾乎不會產生任何額外的效能負擔。
* 額外啟用 Z-fighting 重疊確實會對效能產生不小的額外負擔，不過會因場景而異。

## <a name="api-documentation"></a>API 文件

* [C # RemoteManager. ZFightingMitigationSettings 屬性](/dotnet/api/microsoft.azure.remoterendering.remotemanager.zfightingmitigationsettings)
* [C + + RemoteManager：： ZFightingMitigationSettings ( # B1 ](/cpp/api/remote-rendering/remotemanager#zfightingmitigationsettings)

## <a name="next-steps"></a>後續步驟

* [轉譯模式](../../concepts/rendering-modes.md)
* [延遲階段重新投影](late-stage-reprojection.md)