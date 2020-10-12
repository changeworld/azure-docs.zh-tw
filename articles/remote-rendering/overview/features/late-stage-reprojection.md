---
title: 延遲階段重新投影
description: 有關最晚階段 Reprojection 和使用方式的資訊。
author: sebastianpick
ms.author: sepick
ms.date: 02/04/2020
ms.topic: article
ms.openlocfilehash: 8d42087008f1812bc3713456025ed3be351d0917
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84022175"
---
# <a name="late-stage-reprojection"></a>延遲階段重新投影

*延遲階段 Reprojection* (LSR) 是一種硬體功能，可在使用者移動時協助穩定的全像影像。

當您四處移動靜態模型時，應該會以視覺化的方式維持它們的位置。 如果看起來不穩定，此行為可能會提示 LSR 問題。 請注意，其他動態轉換（例如動畫或爆炸視圖）可能會遮罩此行為。

您可以選擇兩種不同的 LSR 模式，也就是 **平面 LSR** 或 **深度 LSR**。 哪一個是作用中的，取決於用戶端應用程式是否提交深度緩衝區。

這兩種 LSR 模式都能改善全像全像的不同，不過它們有獨特的限制。 先嘗試深度 LSR，因為在大部分情況下都能提供更好的結果。

## <a name="choose-lsr-mode-in-unity"></a>選擇 Unity 中的 LSR 模式

在 Unity 編輯器中，移至 *:::no-loc text="File > Build Settings":::* 。 選取 *:::no-loc text="Player Settings":::* 左下方的，然後檢查是否已 *:::no-loc text="Player > XR Settings > Virtual Reality SDKs > Windows Mixed Reality":::* **:::no-loc text="Enable Depth Buffer Sharing":::** 核取：

![深度緩衝區共用啟用旗標](./media/unity-depth-buffer-sharing-enabled.png)

如果是，您的應用程式將會使用深度 LSR，否則會使用平面 LSR。

## <a name="depth-lsr"></a>深度 LSR

若要讓深度 LSR 正常運作，用戶端應用程式必須提供有效的深度緩衝區，其中包含 LSR 期間要考慮的所有相關幾何。

深度 LSR 會根據提供的深度緩衝區內容，嘗試將影片框架穩定。 因此，LSR 無法調整尚未轉譯的內容（例如透明物件），而且可能會顯示不穩定和 reprojection 的構件。

## <a name="planar-lsr"></a>平面 LSR

平面 LSR 不會有每個圖元的深度資訊，如深度 LSR 所做的。 相反地，它會根據您必須提供每個畫面格的平面 reprojects 所有內容。

平面 LSR 會 reprojects 最適合所提供平面的物件。 物件愈遠，其外觀愈不穩定。 深度 LSR 最好是在不同深度的重新投射物件上，而平面 LSR 可能更適合與平面的內容對齊。

### <a name="configure-planar-lsr-in-unity"></a>在 Unity 中設定平面 LSR

平面參數衍生自所謂的焦點點，您必須透過此 *焦點點*提供每個畫面格 `UnityEngine.XR.WSA.HolographicSettings.SetFocusPointForFrame` 。 如需詳細資訊，請參閱 [Unity 焦點點 API](https://docs.microsoft.com/windows/mixed-reality/focus-point-in-unity) 。 如果您未設定焦點點，則會為您選擇一個回復。 不過，自動回復通常會導致不佳的結果。

您可以自行計算焦點，但以遠端轉譯主機所計算的端點為基礎，可能有意義。 呼叫 `RemoteManagerUnity.CurrentSession.GraphicsBinding.GetRemoteFocusPoint` 以取得該。 系統會要求您提供座標框架以表示焦點點。 在大部分的情況下，您只想要從這裡提供結果 `UnityEngine.XR.WSA.WorldManager.GetNativeISpatialCoordinateSystemPtr` 。

通常用戶端和主機都會轉譯另一端不知道的內容，例如用戶端上的 UI 元素。 因此，將遠端焦點點與本機計算的點合併可能有意義。

在兩個連續的框架中計算的焦點點可能相當不同。 單純地使用它們，可能會導致出現可四處移動的全息影像。 若要避免此行為，建議您在前一個和目前的焦點點之間進行插值。

## <a name="next-steps"></a>接下來的步驟

* [伺服器端效能查詢](performance-queries.md)
