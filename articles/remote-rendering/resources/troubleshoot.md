---
title: 疑難排解
description: Azure 遠端轉譯的疑難排解資訊
author: florianborn71
ms.author: flborn
ms.date: 02/25/2020
ms.topic: troubleshooting
ms.openlocfilehash: f82ea8361cef76b2030e5b257b3d3351968d8050
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91322184"
---
# <a name="troubleshoot"></a>疑難排解

本頁面會列出干擾 Azure 遠端轉譯的常見問題，以及解決這些問題的方法。

## <a name="cant-link-storage-account-to-arr-account"></a>無法將儲存體帳戶連結至 ARR 帳戶

有時候在[連結儲存體帳戶期間](../how-tos/create-an-account.md#link-storage-accounts)，不會列出遠端轉譯帳戶。 若要修正此問題，請移至 Azure 入口網站中的 ARR 帳戶，然後在左側的 [設定] 群組下選取 [身分識別]。 請確定**狀態**設定為 [開啟]。
![Unity 畫面偵錯工具](./media/troubleshoot-portal-identity.png)

## <a name="client-cant-connect-to-server"></a>無法連線到伺服器

請確定您的防火牆 (在裝置上、在路由器內等等，) 不會封鎖 [系統需求](../overview/system-requirements.md#network-ports)中所述的埠。

## <a name="error-disconnected-videoformatnotavailable"></a>錯誤 ' `Disconnected: VideoFormatNotAvailable` '

檢查您的 GPU 是否支援硬體視訊解碼。 請參閱[開發電腦](../overview/system-requirements.md#development-pc)。

如果您使用具有兩個 GPU 的膝上型電腦，則預設執行的 GPU 可能不會提供硬體視訊解碼功能。 若是如此，請嘗試強制您的應用程式使用其他 GPU。 這通常可以在 GPU 驅動程式設定中進行。

## <a name="retrieve-sessionconversion-status-fails"></a>取得會話/轉換狀態失敗

傳送 REST API 命令太頻繁，最後會導致伺服器節流並傳回失敗。 節流案例中的 HTTP 狀態碼為 429 ( 「太多要求」 ) 。 根據經驗法則，**後續的呼叫之間應該會有 5-10 秒**的延遲。

請注意，這項限制不只會影響直接呼叫的 REST API 呼叫，同時也會影響其 c #/C + + 對應專案，例如 `Session.GetPropertiesAsync` 、 `Session.RenewAsync` 或 `Frontend.GetAssetConversionStatusAsync` 。

如果您遇到伺服器端節流，請變更程式碼，以減少呼叫的頻率。 伺服器每分鐘會重設節流狀態，因此可以安全地在一分鐘後重新執行程式碼。

## <a name="h265-codec-not-available"></a>H265 轉碼器無法使用

伺服器可能會拒絕與錯誤連接的原因有兩個 `codec not available` 。

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

請參閱特定 [伺服器大小限制](../reference/limits.md#overall-number-of-polygons)。

**此模型不在相機的截錐中：**

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

Azure 遠端轉譯會在 Unity 轉譯管線中執行勾點，組合影片的畫面以進行重新投影。 若要確認這些攔截是否存在，請開啟功能表 *:::no-loc text="Window > Analysis > Frame debugger":::* 。 請加以啟用，並確定管線中的 `HolographicRemotingCallbackPass` 有兩個項目：

![Unity 轉譯管線](./media/troubleshoot-unity-pipeline.png)

## <a name="checkerboard-pattern-is-rendered-after-model-loading"></a>在模型載入之後轉譯棋盤模式

如果呈現的影像看起來像這樣： ![ 螢幕擷取畫面顯示具有 [工具] 功能表的黑色和白色方塊方格。](../reference/media/checkerboard.png)
然後，轉譯器會達到 [標準設定大小的多邊形限制](../reference/vm-sizes.md)。 若要減輕問題，請切換至 **premium** 設定大小或減少可見的多邊形數目。

## <a name="the-rendered-image-in-unity-is-upside-down"></a>Unity 中轉譯的影像是倒置的

請務必遵循 [Unity 教學課程：完全查看遠端模型](../tutorials/unity/view-remote-models/view-remote-models.md) 。 [倒置] 影像表示必須有 Unity 才能建立非螢幕呈現目標。 此行為目前不受支援，而且會對 HoloLens 2 產生相當大的效能影響。

此問題的原因可能是 MSAA、HDR 或啟用 post 處理。 確定已選取低品質的設定檔，並將其設為 Unity 中的預設值。 若要這樣做，請移至 *編輯 > 專案設定 ... > 品質*。

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
    
### <a name="arm64-builds-for-unity-projects-fail-because-audiopluginmshrtfdll-is-missing"></a>Unity 專案的 Arm64 組建失敗，因為遺漏 AudioPluginMsHRTF.dll

`AudioPluginMsHRTF.dll`Arm64 已新增至3.0.1 版中的*Windows Mixed Reality*套件* (xr. windowsmr. metro) * 。 確定您已透過 Unity 封裝管理員安裝3.0.1 版或更新版本。 從 Unity 功能表列，流覽至 [ *Window >] 封裝管理員* ，然後尋找 *Windows Mixed Reality* 套件。

## <a name="native-c-based-application-does-not-compile"></a>以原生 c + + 為基礎的應用程式不會編譯

### <a name="library-not-found-error-for-uwp-application-or-dll"></a>UWP 應用程式或 Dll 發生「找不到程式庫」錯誤

在 c + + Nuget 套件中，有檔案檔案會 `microsoft.azure.remoterendering.Cpp.targets` 定義要使用的二進位類別。 若要識別 `UWP` ，檔案中的條件會檢查 `ApplicationType == 'Windows Store'` 。 因此必須確保專案中已設定此類型。 當您透過 Visual Studio 的專案嚮導建立 UWP 應用程式或 Dll 時，就會發生這種情況。

## <a name="unstable-holograms"></a>不穩定的全像投影

如果轉譯的物件看起來與前端同時移動，您可能遇到了*延遲階段重新投影* (LSR) 的問題。 如需有關如何處理這類情況的指引，請參閱[延遲階段重新投影](../overview/features/late-stage-reprojection.md)一節。

全像投影不穩定 (晃動、變形、抖動 或全像投影跳動) 的另一個原因可能是網路連線不佳，特別是網路頻寬不足，或延遲過高。 網路連線品質良好的指標是 [效能統計資料](../overview/features/performance-queries.md) 值 `ARRServiceStats.VideoFramesReused`。 重複使用的畫面表示需要在用戶端重複使用舊影片畫面的情況 (因為沒有新的影片畫面可用)，例如因為封包遺失或網路延遲等的差異。 如果 `ARRServiceStats.VideoFramesReused` 經常大於零，這就表示網路有問題。

另一個要查看的值是 `ARRServiceStats.LatencyPoseToReceiveAvg`。 此值應始終低於 100 毫秒。 如果您看到較高的值，這表示您連線的資料中心距離太遠。

如需各種可能降低風險方式的清單，請參閱[網路連線指導方針](../reference/network-requirements.md#guidelines-for-network-connectivity)。

## <a name="z-fighting"></a>Z-fighting

雖然 ARR 提供了 [z 對抗的緩和功能](../overview/features/z-fighting-mitigation.md)，但仍會在場景中顯示 z 對抗。 本指南的目標是針對這些剩餘的問題進行疑難排解。

### <a name="recommended-steps"></a>建議的步驟

您可以使用下列工作流程來減少 z 時：

1. 在) 上使用 ARR 的預設設定來測試場景 (z-對抗緩和措施

1. 透過其[API](../overview/features/z-fighting-mitigation.md)停用 z 減少緩和措施 

1. 將相機近和遠的平面變更為較接近的範圍

1. 透過下一節針對場景進行疑難排解

### <a name="investigating-remaining-z-fighting"></a>調查剩餘的 z-對抗

如果上述步驟已經用盡，而且無法接受剩餘的 z 操作，就必須調查 z 的根本原因。 如同在 [ [z-對抗緩和功能] 頁面](../overview/features/z-fighting-mitigation.md)中所述，有兩個主要原因： z：深度範圍最一端的深度精確度遺失，以及在共置時相交的表面。 深度精確度遺失是數學可能性，而且只有在上述步驟3之後才能緩和。 共面表面表示來源資產的瑕疵，並且在來源資料中更妥善修正。

ARR 有一項功能，可用來判斷表面是否可以進行 z 打擊：棋盤反白 [顯示](../overview/features/z-fighting-mitigation.md)。 您也可以在視覺上判斷造成 z 向的原因。 下列第一個動畫顯示距離的深度精確度遺失範例，而第二個動畫顯示近接面表面的範例：

![動畫顯示距離的深度精確度遺失範例。](./media/depth-precision-z-fighting.gif)  ![動畫顯示將近共面表面的範例。](./media/coplanar-z-fighting.gif)

將這些範例與您的 z 進行比較，以判斷原因，或選擇性地遵循這個逐步工作流程：

1. 將相機定位在 z 對抗表面上方，以直接查看介面。
1. 慢慢地將相機移離表面。
1. 如果所有時間都可以看見 z 對，表面就是完全共置。 
1. 如果大部分的時間都能看到 z 對，表面幾乎是共置的。
1. 如果從最遠的程度來看，無法進行 z 對向，原因是缺乏深度精確度。

共面表面可能有許多不同的原因：

* 由於發生錯誤或不同的工作流程方法，匯出應用程式複製了物件。

    請使用個別的應用程式和應用程式支援來檢查這些問題。

* 介面會複製並翻轉，以在使用正面臉部或背面剔除的轉譯器中出現。

    透過 [模型轉換](../how-tos/conversion/model-conversion.md) 匯入會決定模型的主體 sidedness。 雙 sidedness 會假設為預設值。 介面將會轉譯為具有從兩端實際正確光源的細牆。 單一 sidedness 可由來源資產中的旗標隱含，或在 [模型轉換](../how-tos/conversion/model-conversion.md)期間明確強制執行。 此外，您可以選擇性地將 [單面模式](../overview/features/single-sided-rendering.md) 設定為 [正常]。

* 來源資產中的物件交集。

     以某些表面重迭的方式轉換的物件也會建立 z 的轉換。 在 ARR 的匯入場景中轉換場景樹狀結構的元件也會產生這個問題。

* 表面是以觸控形式特意撰寫的，例如 decals 或牆上的文字。



## <a name="next-steps"></a>後續步驟

* [系統需求](../overview/system-requirements.md)
* [網路需求](../reference/network-requirements.md)
