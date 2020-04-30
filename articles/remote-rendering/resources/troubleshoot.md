---
title: 疑難排解
description: Azure 遠端呈現的疑難排解資訊
author: florianborn71
ms.author: flborn
ms.date: 02/25/2020
ms.topic: troubleshooting
ms.openlocfilehash: b86af2ff8fad3793fc47cec9399fd499c1cabba7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81681863"
---
# <a name="troubleshoot"></a>疑難排解

此頁面會列出干擾 Azure 遠端呈現的常見問題，以及解決這些問題的方法。

## <a name="client-cant-connect-to-server"></a>用戶端無法連接到伺服器

請確定您的防火牆（在裝置上、路由器內部等等）不會封鎖下列埠：

* **50051 （TCP）** -初始連接（HTTP 交握）所需
* **8266 （TCP + UDP）** -資料傳輸的必要項
* **5000 （tcp）**、 **5433 （tcp）**、 **8443 （tcp）** - [ArrInspector](tools/arr-inspector.md)所需

## <a name="error-disconnected-videoformatnotavailable"></a>錯誤「已中斷連線： VideoFormatNotAvailable」

檢查您的 GPU 是否支援硬體視頻解碼。 請參閱[開發電腦](../overview/system-requirements.md#development-pc)。

如果您使用具有兩個 Gpu 的膝上型電腦，則預設執行的 GPU 可能不會提供硬體視頻解碼功能。 若是如此，請嘗試強制您的應用程式使用其他 GPU。 這通常可以在 GPU 驅動程式設定中進行。

## <a name="h265-codec-not-available"></a>H265 編解碼器無法使用

伺服器可能會拒絕使用「**無法使用編解碼器**」錯誤來連接的原因有兩個。

**未安裝 H265 編解碼器：**

首先，請務必安裝**HEVC 影片延伸**模組，如系統需求的[軟體](../overview/system-requirements.md#software)一節中所述。

如果您仍然遇到問題，請確定您的圖形配接器支援 H265，而且您已安裝最新的圖形驅動程式。 如需廠商專屬資訊的系統需求，請參閱[開發電腦](../overview/system-requirements.md#development-pc)一節。

**已安裝編解碼器，但無法使用：**

此問題的原因是 Dll 上的安全性設定不正確。 嘗試觀看以 H265 編碼的影片時，不會出現此問題。 重新安裝編解碼器並不會修正問題。 請改為執行下列步驟：

1. 以系統**管理員許可權開啟 PowerShell**並執行

    ```PowerShell
    Get-AppxPackage -Name Microsoft.HEVCVideoExtension
    ```
  
    該命令應該會輸出`InstallLocation`編解碼器的，如下所示：
  
    ```cmd
    InstallLocation   : C:\Program Files\WindowsApps\Microsoft.HEVCVideoExtension_1.0.23254.0_x64__5wasdgertewe
    ```

1. 在 Windows Explorer 中開啟該資料夾
1. 應該有**x86**和**x64**子資料夾。 以滑鼠右鍵按一下其中一個資料夾，然後選擇 [**屬性**]
    1. 選取 [**安全性**] 索引標籤，然後按一下 [**高級**設定] 按鈕
    1. 按一下 [**擁有**者] 的 [**變更**]
    1. 在文字欄位中輸入系統**管理員**
    1. 按一下 [**檢查名稱** **] 和 [確定]**
1. 針對另一個資料夾重複上述步驟
1. 同時對兩個資料夾中的每個 DLL 檔案重複上述步驟。 應該全部有四個 Dll。

若要確認設定是否正確，請針對這四個 Dll 執行此動作：

1. 選取 [**屬性] > 安全性 > 編輯**
1. 請流覽所有**群組/使用者**的清單，並確定每一個都有**讀取 & 執行**許可權集合（**允許**資料行中的核取記號必須是核取）

## <a name="low-video-quality"></a>低度視頻品質

視頻品質可能會因為網路品質或遺失的 H265 視頻編碼器而受到危害。

* 請參閱[識別網路問題](#unstable-holograms)的步驟。
* 請參閱安裝最新圖形驅動程式的[系統需求](../overview/system-requirements.md#development-pc)。

## <a name="video-recorded-with-mrc-does-not-reflect-the-quality-of-the-live-experience"></a>以 MRC 錄製的影片不會反映即時體驗的品質

您可以透過[混合現實 Capture （MRC）](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers)在 Hololens 上錄製影片。 不過，產生的影片的品質比即時體驗差，原因有兩個：
* 影片的畫面播放速率上限為 30 Hz，而不是 60 Hz。
* 影片影像不會經過[延遲階段的 reprojection](../overview/features/late-stage-reprojection.md)處理步驟，因此影片看起來會是 choppier。

這兩者都是錄製技巧的固有限制。

## <a name="black-screen-after-successful-model-loading"></a>成功載入模型後的黑色畫面

如果您已連接到轉譯執行時間並成功載入模型，但之後只會看到黑色畫面，則這可能有幾個不同的原因。

我們建議您先測試下列事項，再進行更深入的分析：

* H265 編解碼器是否已安裝？ 雖然應該會回復 H264 編解碼器，但我們已看到此回復無法正常運作的情況。 請參閱安裝最新圖形驅動程式的[系統需求](../overview/system-requirements.md#development-pc)。
* 使用 Unity 專案時，請關閉 Unity，並刪除專案目錄中的臨時連結*庫*和*obj*資料夾，然後再次載入/建立專案。 在某些情況下，快取的資料會導致範例無法正常運作，而不會有明顯的原因。

如果這兩個步驟沒有説明，就必須找出用戶端是否收到影片畫面。 如[伺服器端效能查詢](../overview/features/performance-queries.md)一章所述，您可以用程式設計方式查詢這項功能。 `FrameStatistics struct`具有一個成員，指出已收到多少個影片畫面格。 如果此數位大於0並隨著時間而增加，用戶端就會從伺服器接收實際的影片畫面。 因此，它在用戶端上必須是問題。

### <a name="common-client-side-issues"></a>常見的用戶端問題

**此模型不在視圖的「截錐圖：**

在許多情況下，此模型會正確顯示，但位於相機的截錐外部。 其中一個常見的原因是已使用最大的離維軸來匯出模型，因此它會由相機的最遠裁剪平面來裁剪。 它有助於以程式設計方式查詢模型的周框方塊，並使用 Unity 作為線條方塊來視覺化方塊，或將其值列印到偵錯工具記錄檔。

此外，轉換程式會連同已轉換的模型一起產生[輸出 json](../how-tos/conversion/get-information.md)檔案。 若要偵測模型定位問題，請務必查看`boundingBox` [outputStatistics 區段](../how-tos/conversion/get-information.md#the-outputstatistics-section)中的專案：

```JSON
{
    ...
    "outputStatistics": {
        ...
        "boundingBox": {
            "min": [
                -43.52,
                -61.775,
                -79.6416
            ],
            "max": [
                43.52,
                61.775,
                79.6416
            ]
        }
    }
}
```

周框方塊會描述為 3D `min`空間`max`中的和位置（以量為單位）。 因此，1000.0 的座標表示它是從原點離開的1公里。

此周框方塊可能會有兩個問題導致不可見的幾何：
* **此方塊可以是離中央**，因此物件會因為最平面裁剪而完全裁剪。 在`boundingBox`此情況下，這些值看起來像`min = [-2000, -5,-5], max = [-1990, 5,5]`這樣：，在 X 軸上使用大位移，例如這裡的範例。 若要解決這種類型的問題， `recenterToOrigin`請啟用 [[模型轉換](../how-tos/conversion/configure-model-conversion.md)設定] 中的選項。
* **此方塊可以置中，但也可能是大小過大的順序**。 這表示雖然相機是從模型的中央開始，但其幾何會以所有方向裁剪。 在`boundingBox`此情況下，一般值看起來會`min = [-1000,-1000,-1000], max = [1000,1000,1000]`像這樣：。 這類問題的原因通常是單位規模不相符。 若要補償，請[在轉換期間指定調整值](../how-tos/conversion/configure-model-conversion.md#geometry-parameters)，或以正確的單位標記來源模型。 在執行時間載入模型時，調整也可以套用至根節點。

**Unity render 管線不包含轉譯勾點：**

在 Unity 轉譯管線中進行 Azure 遠端轉譯，以執行影片的畫面格組合，以及執行 reprojection。 若要確認這些勾點是否存在，請開啟功能表*視窗 > 分析 > 框架偵錯工具]*。 請啟用它，並確定管線`HolographicRemotingCallbackPass`中有兩個專案：

![Unity 框架偵錯工具](./media/troubleshoot-unity-pipeline.png)

## <a name="unity-code-using-the-remote-rendering-api-doesnt-compile"></a>使用遠端呈現 API 的 Unity 程式碼不會編譯

將 Unity 方案的*組建類型*切換為**Debug**。 在 Unity 編輯器中測試 ARR 時，定義`UNITY_EDITOR`只能在 ' Debug ' 組建中使用。 請注意，這與用於已[部署應用程式](../quickstarts/deploy-to-hololens.md)的組建類型無關，您應該在其中偏好「發行」組建。

## <a name="unstable-holograms"></a>不穩定的全息影像

如果呈現的物件似乎與 head 移動一起移動，您可能會遇到*延遲階段 Reprojection* （LSR）的問題。 如需有關如何處理這類情況的指引，請參閱[晚期階段 Reprojection](../overview/features/late-stage-reprojection.md)一節。

不穩定的全息影像（wobbling、彎曲、jittering 或跳躍的全息影像）的另一個原因可能是網路連線不佳，特別是網路頻寬不足，或太高的延遲。 您的網路連線品質的良好指標就是[效能統計資料](../overview/features/performance-queries.md)值`ARRServiceStats.VideoFramesReused`。 重複使用的畫面表示在用戶端上需要重複使用舊的影片畫面格的情況，因為沒有新的影片畫面格可用–例如因為封包遺失或網路延遲的變化。 如果`ARRServiceStats.VideoFramesReused`經常大於零，這就表示網路有問題。

另一個要查看的值`ARRServiceStats.LatencyPoseToReceiveAvg`是。 應持續低於100毫秒。 如果您看到較高的值，這表示您連線的資料中心太遠。

如需潛在緩和措施的清單，請參閱網路連線的[指導方針](../reference/network-requirements.md#guidelines-for-network-connectivity)。

## <a name="next-steps"></a>後續步驟

* [系統需求](../overview/system-requirements.md)
* [網路需求](../reference/network-requirements.md)
