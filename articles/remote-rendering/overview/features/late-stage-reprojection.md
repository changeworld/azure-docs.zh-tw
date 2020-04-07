---
title: 後期重新投影
description: 有關後期重新投影以及如何使用它的資訊。
author: sebastianpick
ms.author: sepick
ms.date: 02/04/2020
ms.topic: article
ms.openlocfilehash: 4aa1148e544ff3451aa1cb956bc4a5fb932b9611
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680982"
---
# <a name="late-stage-reprojection"></a>後期重新投影

*後期重投影*(LSR) 是一種硬體功能,可説明在使用者移動時穩定全息圖。

靜態模型在移動時應直觀地保持其位置。 如果它們看起來不穩定,則此行為可能會提示 LSR 問題。 請注意,其他動態轉換(如動畫或分解視圖)可能會掩蓋此行為。

您可以在兩種不同的 LSR 模式之間進行選擇,即**平面 LSR**或**深度 LSR**。 哪個處於活動狀態取決於用戶端應用程式是否提交深度緩衝區。

這兩種LSR模式都提高了全息圖的穩定性,儘管它們有其明顯的局限性。 首先嘗試深度LSR,因為它可以說在大多數情況下會提供更好的結果。

## <a name="choose-lsr-mode-in-unity"></a>在統一中選擇 LSR 模式

在 Unity 編輯器中,轉到 *">生成设置的文件*"。 選擇左下角的*播放器設定*,然後在 *「播放機> XR 設定>虛擬實境 SDK > Windows 混合實境*中檢查是否選擇**深度緩衝區共用**:

![深度緩衝區分享啟用標誌](./media/unity-depth-buffer-sharing-enabled.png)

如果是,你的應用將使用深度 LSR,否則將使用平面 LSR。

## <a name="depth-lsr"></a>深度 LSR

要使深度 LSR 正常工作,用戶端應用程式必須提供有效的深度緩衝區,其中包含在 LSR 期間要考慮的所有相關幾何體。

深度LSR嘗試根據提供的深度緩衝區的內容穩定視頻幀。 因此,尚未呈現給它的內容(如透明物件)無法由 LSR 進行調整,並且可能會顯示不穩定和重新投影偽影。

## <a name="planar-lsr"></a>平面 LSR

平面LSR沒有每圖元深度資訊,深度LSR也是如此。 相反,它根據必須為每個幀提供的平面重新專案所有內容。

平面LSR會重新投射那些最靠近提供的平面的物件。 物體越遠,它看起來就越不穩定。 雖然深度LSR更善於在不同深度處重新投影物件,但平面LSR可能更適合與平面對齊的內容。

### <a name="configure-planar-lsr-in-unity"></a>整合配置平面 LSR

平面參數派生自所謂的*焦點*,您`UnityEngine.XR.WSA.HolographicSettings.SetFocusPointForFrame`必須通過 提供每個幀。 有關詳細資訊,請參閱[統一焦點 API。](https://docs.microsoft.com/windows/mixed-reality/focus-point-in-unity) 如果不設置焦點,將為您選擇回退。 但是,自動回退通常會導致結果不理想。

您可以自己計算焦點,儘管將其基於遠端渲染主機計算的焦點可能有意義。 調用`RemoteManagerUnity.CurrentSession.GraphicsBinding.GetRemoteFocusPoint`以獲取該。 系統要求您提供一個座標框架,用於表達焦點。 在大多數情況下,您只想在此處`UnityEngine.XR.WSA.WorldManager.GetNativeISpatialCoordinateSystemPtr`提供結果。

通常,用戶端和主機都呈現對方不知道的內容,例如用戶端上的 UI 元素。 因此,將遠端焦點與本地計算的焦點組合起來可能有意義。

在連續兩個幀中計算的焦點可能大不相同。 簡單地使用它們作為可以導致全息圖似乎跳躍。 為了防止此行為,建議在前一個焦點和當前焦點之間插值。

## <a name="next-steps"></a>後續步驟

* [伺服器端效能查詢](performance-queries.md)
