---
title: 疑難排解
description: Azure 遠端轉譯的疑難排解資訊
author: florianborn71
ms.author: flborn
ms.date: 02/25/2020
ms.topic: troubleshooting
ms.openlocfilehash: b6cb9c70de27e40c62d6a7adeece5cb39554c090
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/26/2020
ms.locfileid: "83844554"
---
# <a name="troubleshoot"></a>疑難排解

本頁面會列出干擾 Azure 遠端轉譯的常見問題，以及解決這些問題的方法。

## <a name="cant-link-storage-account-to-arr-account"></a>無法將儲存體帳戶連結至 ARR 帳戶

有時候在[連結儲存體帳戶期間](../how-tos/create-an-account.md#link-storage-accounts)，不會列出遠端轉譯帳戶。 若要修正此問題，請移至 Azure 入口網站中的 ARR 帳戶，然後在左側的 [設定] 群組下選取 [身分識別]。 請確定**狀態**設定為 [開啟]。
![Unity 畫面偵錯工具](./media/troubleshoot-portal-identity.png)

## <a name="client-cant-connect-to-server"></a>無法連線到伺服器

請確定您的防火牆 (在裝置上、路由器內部等等) 不會封鎖下列連接埠：

* **50051 (TCP)** -初始連線的必要項目 (HTTP 交握)
* **8266 (TCP+UDP)** - 資料傳輸的必要項目
* **5000 (TCP)** 、**5433 (TCP)** 、**8443 (TCP)** - [ArrInspector](tools/arr-inspector.md) 的必要項目

## <a name="error-disconnected-videoformatnotavailable"></a>錯誤「已中斷連線：VideoFormatNotAvailable」

檢查您的 GPU 是否支援硬體視訊解碼。 請參閱[開發電腦](../overview/system-requirements.md#development-pc)。

如果您使用具有兩個 GPU 的膝上型電腦，則預設執行的 GPU 可能不會提供硬體視訊解碼功能。 若是如此，請嘗試強制您的應用程式使用其他 GPU。 這通常可以在 GPU 驅動程式設定中進行。

## <a name="h265-codec-not-available"></a>H265 轉碼器無法使用

伺服器可能因為**無法使用轉碼器**錯誤而拒絕連線，有兩個原因會造成此問題。

**未安裝 H265 轉碼器：**

首先，請務必安裝 **HEVC 視訊延伸模組**，如系統需求的[軟體](../overview/system-requirements.md#software)一節中所述。

如果仍然遇到問題，請確定您的圖形卡支援 H265，而且已安裝最新的圖形驅動程式。 如需特定廠商的系統需求資訊，請參閱[開發電腦](../overview/system-requirements.md#development-pc)一節。

**已安裝轉碼器，但無法使用：**

此問題的原因是 DLL 上的安全性設定不正確。 嘗試觀看以 H265 編碼的影片時，不會出現此問題。 重新安裝轉碼器也無法解決問題。 而是必須執行下列步驟：

1. **以管理員權限開啟 PowerShell** 並執行

    ```PowerShell
    Get-AppxPackage -Name Microsoft.HEVCVideoExtension
    ```
  
    該命令應會輸出轉碼器的 `InstallLocation`，如下所示：
  
    ```cmd
    InstallLocation   : C:\Program Files\WindowsApps\Microsoft.HEVCVideoExtension_1.0.23254.0_x64__5wasdgertewe
    ```

1. 在 Windows 檔案總管中開啟資料夾
1. 您應該會看到 **x86** 和 **x64** 子資料夾。 以滑鼠右鍵按一下其中一個資料夾，然後選擇 [屬性]
    1. 選取 [安全性] 索引標籤，並按一下 [進階] 設定按鈕
    1. 針對 [擁有者] 按一下 [變更]
    1. 在文字欄位中輸入**管理員**
    1. 按一下 [檢查名稱] 及 [確定]
1. 對其他資料夾重複上述步驟
1. 同時對兩個資料夾中的每個 DLL 檔案重複上述步驟。 總共應該有四個 DLL。

若要確認目前設定是否正確，請針對這四個 DLL 執行此動作：

1. 選取 **屬性 > 安全性 > 編輯**
1. 瀏覽所有**群組/使用者**清單，並確定每個群組/使用者都有正確的**讀取和執行**集合 (必須已勾選**允許**資料行中的核取記號)

## <a name="low-video-quality"></a>影片畫質不佳

影片品質可能會因為網路品質或缺少 H265 視訊轉碼器而受到損害。

* 請參閱[找出網路問題](#unstable-holograms)的步驟。
* 請參閱[系統需求](../overview/system-requirements.md#development-pc)，以安裝最新的圖形驅動程式。

## <a name="video-recorded-with-mrc-does-not-reflect-the-quality-of-the-live-experience"></a>以 MRC 錄製的影片不會反映即時體驗的品質

您可以透過[混合實境擷取 (MRC)](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers)，在 Hololens 上錄製影片。 不過，產生的影片品質會比即時體驗差，原因有兩個：
* 影片的畫面播放速率上限為 30 Hz，而不是 60 Hz。
* 影片影像不會經過[延遲階段重新投影](../overview/features/late-stage-reprojection.md)處理步驟，因此影片畫質會較為抖動。

這兩者都是錄製技巧的既定限制。

## <a name="black-screen-after-successful-model-loading"></a>成功載入模型後出現黑色畫面

如果您已連線到轉譯執行階段並成功載入模型，但只會看到黑色畫面，這可能有幾個不同的原因。

建議您先測試下列項目，再進行更深入的分析：

* 是否已安裝 H265 轉碼器？ 雖然應該會回復使用 H264 轉碼器，但我們已看到此回復無法正常運作。 請參閱[系統需求](../overview/system-requirements.md#development-pc)，以安裝最新的圖形驅動程式。
* 使用 Unity 專案時，請關閉 Unity，刪除暫存的「程式庫」和專案目錄中的 obj 資料夾，然後再次載入/建置專案。 在某些情況下，快取的資料會導致範例無法正常運作，而且並沒有明顯的原因。

如果這兩個步驟沒有幫助，就必須了解用戶端是否有收到影片畫面。 如[伺服器端效能查詢](../overview/features/performance-queries.md)一章所述，您可以使用程式設計方式查詢。 `FrameStatistics struct` 有一個成員，指出已收到多少個影片畫面。 如果此數字大於 0，並隨著時間而增加，用戶端就會從伺服器接收到實際的影片畫面。 因此，這在用戶端上是個問題。

### <a name="common-client-side-issues"></a>用戶端常見問題

**此模型超過所選 VM 的限制，尤其是多邊形的最大數目：**

請參閱特定的 [VM 大小限制](../reference/limits.md#overall-number-of-polygons)。

**此模型不在視圖的視體範圍內：**

在許多情況下，此模型會正確顯示，但卻位於相機的視體之外。 其中一個常見的原因是已使用十分偏離中央位置的樞紐來匯出模型，因此會由相機的遠裁剪平面來裁剪。 這種方式有助於以程式設計方式查詢模型的周框方塊，並使用 Unity 作為線條方塊將方塊視覺化，或將其值列印到偵錯工具記錄。

此外，轉換流程會產生[輸出 JSON 檔案](../how-tos/conversion/get-information.md)， 與已轉換的模型搭配使用。 若要偵錯模型定位問題，請務必參閱 [outputStatistics 一節](../how-tos/conversion/get-information.md#the-outputstatistics-section)中的 `boundingBox` 項目：

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

周框方塊在 3D 空間中的位置會描述為 `min` 和 `max`，以公尺為單位。 因此，1000.0 的座標表示其位於原點的 1 公里之外。

此周框方塊可能會有兩個問題，因此看不見幾何：
* **方塊可能十分偏離中央位置**，因此物件會因為目前的遠平面裁剪而遭到系統裁剪。 在這種情況下，`boundingBox` 值看起來會像這樣：`min = [-2000, -5,-5], max = [-1990, 5,5]`，使用 X 軸上的大量位移作為範例。 若要解決這種類型的問題，請啟用[模型轉換設定](../how-tos/conversion/configure-model-conversion.md)中的 `recenterToOrigin` 選項。
* **方塊可以置中，但範圍可能太大**。 這表示雖然相機是從模型的中央啟動，但其幾何會以所有方向裁剪。 在這種情況下，一般 `boundingBox` 值看起來會像這樣：`min = [-1000,-1000,-1000], max = [1000,1000,1000]`。 這類問題的原因通常是單位調整不相符。 若要補償，請[在轉換期間調整值](../how-tos/conversion/configure-model-conversion.md#geometry-parameters)，或以正確的單位標記來源模型。 在執行階段載入模型時，調整也可以套用至根節點。

**Unity 管線不包含轉譯勾點：**

Azure 遠端轉譯會在 Unity 轉譯管線中執行勾點，組合影片的畫面以進行重新投影。 若要確認這些勾點是否存在，請開啟功能表「視窗 > 分析 > 畫面偵錯工具」。 請加以啟用，並確定管線中的 `HolographicRemotingCallbackPass` 有兩個項目：

![Unity 畫面偵錯工具](./media/troubleshoot-unity-pipeline.png)

## <a name="unity-code-using-the-remote-rendering-api-doesnt-compile"></a>使用遠端轉譯 API 的 Unity 程式碼不會進行編譯

### <a name="use-debug-when-compiling-for-unity-editor"></a>針對 Unity 編輯器進行編譯時使用偵錯

將 Unity 解決方案的「組建類型」 切換為**偵錯**。 在 Unity 編輯器中測試 ARR 時，定義 `UNITY_EDITOR` 僅適用於「偵錯」組建。 請注意，這與用於 [部署應用程式](../quickstarts/deploy-to-hololens.md)的組建類型無關，您應該使用「發行」組建。

### <a name="compile-failures-when-compiling-unity-samples-for-hololens-2"></a>編譯 HoloLens 2 的 Unity 範例時失敗

我們在嘗試編譯適用於 HoloLens 2 的 Unity 範例 (快速入門、ShowCaseApp...) 時，看到了假性失敗。 Visual Studio 抱怨有關無法複製某些檔案 (雖然檔案存在)。 如果您遇到這個問題：
* 從專案中移除所有暫存 Unity 檔案，然後再試一次。 也就是說，請關閉 Unity，刪除暫存的「程式庫」和專案目錄中的 obj 資料夾，然後再次載入/建置專案。
* 請確定專案位於磁碟上具有合理短路徑的目錄中，因為複製步驟有時似乎會遇到長檔名的問題。
* 如果沒有幫助，可能是因為 MS Sense 受到複製步驟的干擾。 若要設定例外狀況，請從命令列執行此登錄命令 (需要管理員權限)：
    ```cmd
    reg.exe ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows Advanced Threat Protection" /v groupIds /t REG_SZ /d "Unity”
    ```
    
## <a name="unstable-holograms"></a>不穩定的全像投影

如果轉譯的物件看起來與前端同時移動，您可能遇到了*延遲階段重新投影* (LSR) 的問題。 如需有關如何處理這類情況的指引，請參閱[延遲階段重新投影](../overview/features/late-stage-reprojection.md)一節。

全像投影不穩定 (晃動、變形、抖動 或全像投影跳動) 的另一個原因可能是網路連線不佳，特別是網路頻寬不足，或延遲過高。 網路連線品質良好的指標是 [效能統計資料](../overview/features/performance-queries.md) 值 `ARRServiceStats.VideoFramesReused`。 重複使用的畫面表示需要在用戶端重複使用舊影片畫面的情況 (因為沒有新的影片畫面可用)，例如因為封包遺失或網路延遲等的差異。 如果 `ARRServiceStats.VideoFramesReused` 經常大於零，這就表示網路有問題。

另一個要查看的值是 `ARRServiceStats.LatencyPoseToReceiveAvg`。 此值應始終低於 100 毫秒。 如果您看到較高的值，這表示您連線的資料中心距離太遠。

如需各種可能降低風險方式的清單，請參閱[網路連線指導方針](../reference/network-requirements.md#guidelines-for-network-connectivity)。

## <a name="next-steps"></a>後續步驟

* [系統需求](../overview/system-requirements.md)
* [網路需求](../reference/network-requirements.md)
