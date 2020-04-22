---
title: 疑難排解
description: Azure 遠端呈現的故障排除資訊
author: florianborn71
ms.author: flborn
ms.date: 02/25/2020
ms.topic: troubleshooting
ms.openlocfilehash: b86af2ff8fad3793fc47cec9399fd499c1cabba7
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681863"
---
# <a name="troubleshoot"></a>疑難排解

此頁列出了干擾 Azure 遠程呈現的常見問題,以及解決這些問題的方法。

## <a name="client-cant-connect-to-server"></a>用戶端無法連線到伺服器

確保您的防火牆(在裝置上、路由器內部等)不會阻止以下埠:

* **50051 (TCP)** - 初始連線(HTTP 握手)需要
* **8266 (TCP+UDP)** - 資料傳輸需要
* **5000 (TCP)**, **5433 (TCP)**, **8443 (TCP)** - [ArrInspector](tools/arr-inspector.md)需要

## <a name="error-disconnected-videoformatnotavailable"></a>錯誤"已斷開連接:視頻格式不可用"

檢查您的 GPU 是否支援硬體視訊解碼。 請參考[式開發 PC](../overview/system-requirements.md#development-pc)。

如果您在具有兩個 GPU 的便攜式電腦上工作,則預設情況下正在運行的 GPU 可能無法提供硬體視訊解碼功能。 如果是這樣,請嘗試強制你的應用使用其他 GPU。 這在 GPU 驅動程式設置中通常是可能的。

## <a name="h265-codec-not-available"></a>H265 編解碼器不可用

伺服器可能拒絕連接**編解碼器不可用**錯誤的原因有兩個。

**未安裝 H265 編解碼器:**

首先,請確保安裝系統要求[的軟體](../overview/system-requirements.md#software)部分中提到的**HEVC 視訊擴展**。

如果仍然遇到問題,請確保您的顯卡支援 H265 並安裝了最新的圖形驅動程式。 有關特定於供應商的資訊,請參閱系統要求的[「開發 PC」](../overview/system-requirements.md#development-pc)部分。

**已安裝編解碼器,但無法使用:**

此問題的原因是 DLL 上的安全設置不正確。 在嘗試觀看使用 H265 編碼的視頻時,此問題不會顯現出來。 重新安裝編解碼器也不能解決問題。 而是執行以下步驟:

1. 開啟**具有管理權限的 PowerShell**並執行

    ```PowerShell
    Get-AppxPackage -Name Microsoft.HEVCVideoExtension
    ```
  
    該命令應輸出編`InstallLocation`解碼器,如下所示:
  
    ```cmd
    InstallLocation   : C:\Program Files\WindowsApps\Microsoft.HEVCVideoExtension_1.0.23254.0_x64__5wasdgertewe
    ```

1. 在 Windows 資源管理員開啟該資料夾
1. 應該有一個**x86**和一個**x64**子資料夾。 右鍵按一個資料夾並選擇 **"屬性"**
    1. 選擇 **「安全**」選項卡,然後按**下 「進階**設定」按鈕
    1. 按下 **「** 所有者變更 **」**
    1. 在文字欄位中鍵入**管理員**
    1. 按下 **"檢查名稱**"和 **"確定"**
1. 對其他資料夾重複上述步驟
1. 同時,對兩個資料夾中的每個 DLL 檔重複上述步驟。 應該總共有四個 DLL。

要驗證設定現在是否正確,請對四個 DLL 中的每一個執行以下操作:

1. 選擇 **'>安全>編輯屬性**
1. 瀏覽所有**群組/使用者**的清單,並確保每個群組都具有 **「讀取&執行**」右側集(必須勾選**允許**列中的複選標記)

## <a name="low-video-quality"></a>視訊品質低

網路品質或缺少的 H265 視頻編解碼器可能會損害視頻品質。

* 請參閱[識別網路問題](#unstable-holograms)的步驟。
* 請參考安裝最新圖形驅動程式[的系統要求](../overview/system-requirements.md#development-pc)。

## <a name="video-recorded-with-mrc-does-not-reflect-the-quality-of-the-live-experience"></a>使用 MRC 錄製的影片不反映即時體驗的品質

視頻可以通過[混合現實捕獲 (MRC)](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers)在霍洛倫斯上錄製。 但是,生成的視頻的品質比現場體驗差,原因有二:
* 視頻幀速率上限為 30 Hz,而不是 60 Hz。
* 視頻圖像不會經過[後期重新投影](../overview/features/late-stage-reprojection.md)處理步驟,因此視頻看起來是斬波的。

兩者都是錄音技術的內在局限性。

## <a name="black-screen-after-successful-model-loading"></a>成功載入模型後黑屏

如果連接到渲染運行時並成功載入了模型,但之後只能看到黑屏,則這可能有幾個不同的原因。

我們建議在進行更深入的分析之前測試以下操作:

* 是否安裝了 H265 編解碼器? 儘管應該對 H264 編解碼器進行回退,但我們已經看到此回退無法正常工作的情況。 請參考安裝最新圖形驅動程式[的系統要求](../overview/system-requirements.md#development-pc)。
* 使用 Unity 專案時,關閉 Unity,刪除專案目錄中的暫存*庫*和*obj*資料夾,然後再次載入/生成專案。 在某些情況下,緩存的數據會導致示例無法正常工作,原因顯而易見。

如果這兩個步驟沒有説明,則需要瞭解用戶端是否接收視頻幀。 這可以通過伺服器[端性能查詢](../overview/features/performance-queries.md)章節中解釋的方式以程式設計方式查詢。 具有`FrameStatistics struct`一個成員,指示已收到的視頻幀數。 如果此數位大於 0 並且隨時間而增加,則用戶端將從伺服器接收實際視頻幀。 因此,它必須是用戶端上的問題。

### <a name="common-client-side-issues"></a>常見的用戶端問題

**模型不在檢視角度內:**

在許多情況下,模型顯示正確,但位於攝像機外部。 一個常見原因是模型已匯出到離中心遠的樞軸,因此它被攝像機的遠剪平面剪切。 它有助於以程式設計方式查詢模型的邊界框,並將 Unity 作為行框可視化框,或將其值列印到調試日誌。

此外,轉換過程將產生與轉換模型一起生成的[輸出 json 檔](../how-tos/conversion/get-information.md)。 要調試模型定位問題,值得查看[輸出統計部分](../how-tos/conversion/get-information.md#the-outputstatistics-section)中的`boundingBox`條目:

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

邊界框被描述為 3D`min``max`空間中的和 位置,以米為單位。 因此,座標 1000.0 表示它距離原點 1 公里。

此邊界框可能有兩個問題,導致不可見的幾何體:
* **該框可以遠離中心**,因此由於遠平面剪切,物件被完全剪切。 在這種情況下`boundingBox`,值如下所示`min = [-2000, -5,-5], max = [-1990, 5,5]`: ,使用 x 軸上的大偏移作為示例。 要解決此問題,在[模型轉換配置](../how-tos/conversion/configure-model-conversion.md)中`recenterToOrigin`啟用 選項。
* **盒子可以居中,但數量過大**。 這意味著,儘管攝像機從模型的中心開始,但其幾何形狀被剪切到所有方向。 在這種情況下`boundingBox`,典型值如下所示`min = [-1000,-1000,-1000], max = [1000,1000,1000]`: 。 此類問題的原因通常是單位比例不匹配。 要進行補償,請在[轉換期間指定縮放值](../how-tos/conversion/configure-model-conversion.md#geometry-parameters),或使用正確的單位標記源模型。 在運行時載入模型時,也可以將縮放應用於根節點。

**Unity 渲染管管不包括渲染掛鉤:**

Azure 遠端渲染將掛到 Unity 渲染管道中,以便使用視頻執行幀合成,並執行重新投影。 要驗證這些掛鉤是否存在,請打開選單*視窗>分析>幀調試器*。 開啟它並確保管道`HolographicRemotingCallbackPass`中有兩個條目:

![統一幀除錯器](./media/troubleshoot-unity-pipeline.png)

## <a name="unity-code-using-the-remote-rendering-api-doesnt-compile"></a>使用遠端呈現 API 的統一程式不編譯

將 Unity 解決方案的*產生類型*切換到**除錯**。 在 Unity 編輯器中測試`UNITY_EDITOR`ARR 時,該定義僅在「除錯」生成中可用。 請注意,這與用於[部署應用程式的](../quickstarts/deploy-to-hololens.md)生成類型無關,在其中您應該更喜歡"發佈"生成。

## <a name="unstable-holograms"></a>不穩定的全息圖

如果渲染的物件似乎隨著頭部移動而移動,則可能會遇到*後期投影*(LSR) 的問題。 請參閱[「後期重新投影](../overview/features/late-stage-reprojection.md)」一節,瞭解如何處理此類情況。

全息圖不穩定的另一個原因(擺動、扭曲、抖動或跳躍全息圖)可能是網路連接不良,特別是網路頻寬不足或延遲過高。 [性能統計](../overview/features/performance-queries.md)`ARRServiceStats.VideoFramesReused`值 是網路連接品質的一個很好的指標。 重複使用的幀指示舊視頻幀需要在用戶端重複使用的情況,因為沒有新的視頻幀可用 - 例如,由於數據包丟失或網路延遲的變化。 如果`ARRServiceStats.VideoFramesReused`通常大於零,則表示存在網路問題。

另一個要檢視的值`ARRServiceStats.LatencyPoseToReceiveAvg`是 。 它應該始終低於 100 毫秒。 如果看到較高的值,則表示您已連接到太遠的數據中心。

有關潛在緩解措施的清單,請參閱[網路連接指南](../reference/network-requirements.md#guidelines-for-network-connectivity)。

## <a name="next-steps"></a>後續步驟

* [系統需求](../overview/system-requirements.md)
* [網路需求](../reference/network-requirements.md)
