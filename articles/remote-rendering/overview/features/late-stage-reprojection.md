---
title: 延遲階段重新投影
description: 有關延遲階段 Reprojection 和如何使用的資訊。
author: sebastianpick
ms.author: sepick
ms.date: 02/04/2020
ms.topic: article
ms.openlocfilehash: 8d42087008f1812bc3713456025ed3be351d0917
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84022175"
---
# <a name="late-stage-reprojection"></a>延遲階段重新投影

*延遲階段 Reprojection* （LSR）是一種硬體功能，可在使用者移動時協助穩定的全息影像。

當您四處移動時，靜態模型應該會以視覺化方式維護其位置。 如果似乎不穩定，此行為可能會在 LSR 問題時提示。 請注意，其他動態轉換（例如動畫或激增視圖）可能會遮罩此行為。

您可以在兩種不同的 LSR 模式（也就是**平面 LSR**或**深度 LSR**）之間做選擇。 哪一個是作用中的取決於用戶端應用程式是否提交深度緩衝區。

這兩種 LSR 模式都會改善全息影像的穩定性，雖然它們有不同的限制。 一開始請先嘗試深度 LSR，因為這在大部分情況下都能提供更好的結果。

## <a name="choose-lsr-mode-in-unity"></a>選擇 Unity 中的 LSR 模式

在 Unity 編輯器中，移至 *:::no-loc text="File > Build Settings":::* 。 選取 *:::no-loc text="Player Settings":::* 左下方的，然後勾選 [ *:::no-loc text="Player > XR Settings > Virtual Reality SDKs > Windows Mixed Reality":::* 是否 **:::no-loc text="Enable Depth Buffer Sharing":::** 已核取]：

![深度緩衝區共用啟用旗標](./media/unity-depth-buffer-sharing-enabled.png)

如果是，您的應用程式將會使用深度 LSR，否則會使用平面 LSR。

## <a name="depth-lsr"></a>深度 LSR

若要讓深度 LSR 能夠正常執行，用戶端應用程式必須提供有效的深度緩衝區，其中包含 LSR 期間所要考慮的所有相關幾何。

深度 LSR 會根據提供的深度緩衝區內容，嘗試將影片框架穩定。 因此，LSR 無法調整尚未呈現給它的內容（例如透明物件），而且可能會顯示不穩定和 reprojection 構件。

## <a name="planar-lsr"></a>平面 LSR

平面 LSR 不會有每個圖元的深度資訊，如深度 LSR。 相反地，它會根據您必須提供每個畫面格的平面來 reprojects 所有內容。

平面 LSR 會 reprojects 那些位於所提供平面附近的物件。 物件越遠離，就越不穩定。 深度 LSR 在不同深度的 reprojecting 物件上較佳，但平面 LSR 可能適用于內容與平面的對齊效果。

### <a name="configure-planar-lsr-in-unity"></a>在 Unity 中設定平面 LSR

平面參數是從所謂的*焦點*衍生而來，您必須提供每個畫面格 `UnityEngine.XR.WSA.HolographicSettings.SetFocusPointForFrame` 。 如需詳細資訊，請參閱[Unity 焦點點 API](https://docs.microsoft.com/windows/mixed-reality/focus-point-in-unity) 。 如果您未設定焦點點，則會為您選擇一個回溯。 不過，自動回退通常會導致較佳的結果。

您可以自行計算焦點，但根據遠端轉譯主機所計算的工作點可能會有意義。 呼叫 `RemoteManagerUnity.CurrentSession.GraphicsBinding.GetRemoteFocusPoint` 以取得該。 系統會要求您提供用來表示焦點的座標框架。 在大部分的情況下，您只想要從這裡提供結果 `UnityEngine.XR.WSA.WorldManager.GetNativeISpatialCoordinateSystemPtr` 。

通常用戶端和主機都會呈現另一端不知道的內容，例如用戶端上的 UI 元素。 因此，將遠端焦點點結合到本機計算的區域可能是合理的。

在兩個連續的畫面格中計算的焦點點可能會有相當大的不同。 只要以自己的方式使用它們，就會導致顯示的全息影像出現。 為避免此行為，建議您在前一個和目前的焦點點之間進行插入。

## <a name="next-steps"></a>後續步驟

* [伺服器端效能查詢](performance-queries.md)
