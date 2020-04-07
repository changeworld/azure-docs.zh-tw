---
title: Arr 檢查器檢查工具
description: Arr 檢查器工具的使用者手冊
author: florianborn71
ms.author: flborn
ms.date: 03/09/2020
ms.topic: article
ms.openlocfilehash: e3acfc15b0c12822e48009bef4aabadac701fb2d
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680072"
---
# <a name="the-arrinspector-inspection-tool"></a>Arr 檢查器檢查工具

ArrInspector 是一個基於 Web 的工具,用於檢查正在運行的 Azure 遠端呈現作業階段。 它用於調試目的,用於檢查正在呈現的場景的結構、顯示日誌消息和監視伺服器上的即時性能。

![阿勒檢驗員](./media/arr-inspector.png)

## <a name="connecting-to-the-arrinspector"></a>連接到 Arr 檢查器

獲取 ARR 伺服器的主`mixedreality.azure.com`機名稱 (以 )結尾後,請使用[ConnectToArrInspectorAsync](../../how-tos/frontend-apis.md#connect-to-arr-inspector)進行連接。 此函數在應用程式`StartArrInspector.html`執行的裝置上建立 。 要啟動 ArrInspector,在 PC 上使用瀏覽器(邊緣、火狐或 Chrome)打開該檔。 該檔僅有效 24 小時。

如果呼叫`ConnectToArrInspectorAsync`的應用程式已在 PC 上執行:

* 如果您使用的是 Unity 整合,它可能會自動為您啟動。
* 否則,您將在*使用者資料夾\\本地AppData\\\\[your_app]\\AC 臨時中*找到該檔。

如果套用在 HoloLens 執行:

1. 使用[Windows 設備門戶](https://docs.microsoft.com/windows/mixed-reality/using-the-windows-device-portal)訪問 HoloLens。
1. 跳到*系統>檔案資源管理員*。
1. 瀏覽到*使用者資料夾\\本地AppData\\\\\\[your_app] AC 溫度*。
1. 將*StartArr 檢查器.html*儲存到電腦。
1. 開啟*StartArr 檢查器.html*以載入作業階段的 Arr 檢查器。

## <a name="the-performance-panel"></a>效能面板

![效能面板](./media/performance-panel.png)

此面板顯示伺服器公開的所有每幀性能值的圖形。 這些值當前包括幀時間、FPS、CPU 和記憶體使用方式、記憶體統計資訊(如總體 RAM 使用方式、物件計數等)。

要可視化這些參數之一,請按下「**添加新」** 按鈕並選擇對話框中顯示的可用值之一。 此操作向面板添加新滾動圖表,即時跟蹤值。 在其右側,您可以看到*最小*值、*最大值*和*當前*值。

但是,您可以通過使用滑鼠拖動圖形內容來平移圖形,但是,只有在 ArrInspector 處於暫停狀態時,才可以水準平移。

拖動時按住 CTRL,可以縮放。 水準變焦也可以用底部的滑塊控制。

預設情況下,垂直範圍根據當前顯示的值進行計算,最小值和最大值顯示在右側的文本框中。 手動設置值時,通過直接在文本框中鍵入這些值,或者通過平移/縮放來設置這些值,圖形將使用這些值。 要復原自動垂直框架,請按一下右上角的圖示。

![垂直範圍](./media/vertical-range.png)

## <a name="the-log-panel"></a>紀錄面板

![紀錄面板](./media/log-panel.png)

記錄面板顯示伺服器端生成的日誌訊息的清單。 在連接時,它最多會顯示 200 條以前的日誌消息,並在它們發生時列印新的日誌消息。

您可以使用頂部的按鈕根據日誌類型`[Error/Warning/Info/Debug]`篩選清單。
![紀錄篩選器按鈕](./media/log-filter.png)

## <a name="the-timing-data-capture-panel"></a>計時資料擷取面板

![計時資料捕捉](./media/timing-data-capture.png)

此面板用於從伺服器捕獲計時資訊並下載它。 此檔案使用[Chrome 追蹤 JSON 格式](https://docs.google.com/document/d/1CvAClvFfyA5R-PhYUmn5OOQtYMH4h6I0nSsKchNAySU/edit)。 要檢查數據,請打開`Chrome://tracing`URL 上的 Chrome,並將下載的檔拖放到頁面。 計時數據在固定大小的環形緩衝區中連續收集。 寫入時,捕獲僅包含有關直接過去的資訊,這意味著幾秒鐘到幾分鐘。

## <a name="the-scene-inspection-panel"></a>場景檢查面板

![現場檢查面板](./media/scene-inspection-panel.png)

此面板顯示渲染場景的結構。 物件層次結構在左側,所選物件的內容在右側。 面板是唯讀的,並即時更新。

## <a name="the-vm-debug-information-panel"></a>VM 除錯資訊面板

![VM 除錯資訊面板](./media/state-debugger-panel.png)

此面板提供一些調試功能。

### <a name="restart-service"></a>重新啟動服務

**重新啟動服務**按鈕重新啟動 arrInspector 連接到的虛擬機上的執行時。 任何連接的用戶端都將斷開連接,並且必須重新載入 arrInspector 頁才能連接到重新啟動的服務。

### <a name="collect-debug-information"></a>收集除錯資訊

「**收集 VM 除錯資訊**」按鈕將開啟一個對話框,允許您觸發 ARR 實體以在 VM 上收集除錯資訊:

![VM 除錯資訊對話框](./media/state-debugger-dialog.png)

除錯資訊可説明 Azure 遠端呈現團隊分析正在運行的 ARR 實例中出現的任何問題。 該對話框具有一個文本欄位,用於提供其他詳細資訊,例如重現問題的步驟。

按下「**開始收集」** 按鈕後,對話框將關閉,收集過程將開始。 在 VM 上收集資訊可能需要幾分鐘時間。

![VM 除錯資訊收集正在進行中](./media/state-debugger-panel-in-progress.png)

收集完成後,您將在 ArrInspector 視窗中收到通知。 此通知包含標識此特定集合的 ID。 請務必保存此 ID 以將其傳遞給 Azure 遠端呈現團隊。

![VM 除錯資訊收集成功](./media/state-debugger-snackbar-success.png)

> [!IMPORTANT]
> 您無法下載或以其他方式存取 VM 除錯資訊。 只有 Azure 遠程呈現團隊有權訪問收集的數據。 您需要聯繫我們並發送收款 ID,以便我們調查您看到的問題。

## <a name="pause-mode"></a>暫停模式

在右上角,交換機允許您暫停面板的即時更新。 此模式可用於仔細檢查特定狀態。

![暫停模式](./media/pause-mode.png)

重新啟用即時更新時,將重置所有面板。

## <a name="host-configuration"></a>主機組態

默認情況下,該工具連接到在同一主機上運行的 ARR 伺服器,為 ArrInspector 提供服務。 但是,您可以將其配置為檢查另一台伺服器,前提是它運行的是打開工具埠的 ARR 實例。

為此,請造訪標題列左邊的主選單,然後選擇*主機設定*。 按下「**添加新主機**」,然後輸入名稱和主機名。 對於*主機名*,僅使用`.mixedreality.azure.com`以結尾 的主`http://`機名,請 不包含 或埠。

![主機設定](./media/host-configuration.png)

要快速從一個主機切換到另一個主機,請使用右上角的下拉。

![主機組合](./media/host-switch-combo.png)

主機清單儲存在瀏覽器本地存儲中,因此在重新打開同一瀏覽器時將保留該清單。
