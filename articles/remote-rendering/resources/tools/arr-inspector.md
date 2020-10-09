---
title: ArrInspector 檢查工具
description: ArrInspector 工具的使用者手冊
author: florianborn71
ms.author: flborn
ms.date: 03/09/2020
ms.topic: article
ms.openlocfilehash: e3acfc15b0c12822e48009bef4aabadac701fb2d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "80680072"
---
# <a name="the-arrinspector-inspection-tool"></a>ArrInspector 檢查工具

ArrInspector 是一種 web 工具，可用來檢查執行中的 Azure 遠端轉譯會話。 它的目的是要用於偵錯工具，以檢查正在轉譯的場景結構、顯示記錄檔訊息，以及監視伺服器上的即時效能。

![ArrInspector](./media/arr-inspector.png)

## <a name="connecting-to-the-arrinspector"></a>連接至 ArrInspector

一旦您取得主機名稱 (在 `mixedreality.azure.com` ARR 伺服器) 中結束，請使用 [ConnectToArrInspectorAsync](../../how-tos/frontend-apis.md#connect-to-arr-inspector)進行連接。 此函數 `StartArrInspector.html` 會在執行應用程式的裝置上建立。 若要啟動 ArrInspector，請在電腦上使用瀏覽器 (Edge、Firefox 或 Chrome) 開啟該檔案。 檔案只會在24小時內有效。

如果呼叫的應用程式 `ConnectToArrInspectorAsync` 已在電腦上執行：

* 如果您使用 Unity 整合，則可能會自動為您啟動。
* 否則，您會在 *[使用者資料夾 \\ \\ ] LocalAppData [your_app] \\ AC \\ Temp*中找到檔案。

如果應用程式是在 HoloLens 上執行：

1. 使用 [Windows 裝置入口網站](https://docs.microsoft.com/windows/mixed-reality/using-the-windows-device-portal)存取 HoloLens。
1. 移至 [ *系統 >] 檔案總管*。
1. 流覽至 *[使用者資料夾] \\ LocalAppData \\ [your_app] \\ AC \\ Temp*。
1. 將 *StartArrInspector.html* 儲存到您的電腦。
1. 開啟 *StartArrInspector.html* 以載入會話的 ArrInspector。

## <a name="the-performance-panel"></a>效能面板

![效能面板](./media/performance-panel.png)

此面板會顯示伺服器所公開的所有每個畫面格效能值的圖表。 這些值目前包含框架時間、FPS、CPU 和記憶體使用量、記憶體統計資料，例如整體 RAM 使用量、物件計數等等。

若要將其中一個參數視覺化，請按一下 [ **加入新** 的] 按鈕，然後選取對話方塊中顯示的其中一個可用值。 此動作會將新的滾動圖加入至面板，即時追蹤這些值。 您可以在右側看到最 *小*值、 *最大* 值和 *目前* 值。

您可以使用滑鼠拖曳圖形內容來移動圖形，不過，只有當 ArrInspector 處於暫停狀態時，才可以水準移動。

拖曳時按住 CTRL 可讓您縮放。 水準縮放也可以使用底部的滑杆來控制。

垂直範圍預設會根據目前顯示的值來計算，而最小值和最大值會顯示在右邊的文字方塊中。 以手動方式設定值時，只要將它們直接輸入 textbox 或移動流覽/縮放，圖形就會使用這些值。 若要還原自動垂直框架，請按一下右上角的圖示。

![垂直範圍](./media/vertical-range.png)

## <a name="the-log-panel"></a>記錄面板

![記錄面板](./media/log-panel.png)

[記錄] 面板會顯示伺服器端所產生的記錄檔訊息清單。 連接時，會顯示最多200個先前的記錄訊息，並會在發生時列印新的記錄訊息。

您可以 `[Error/Warning/Info/Debug]` 使用頂端的按鈕，根據記錄類型來篩選清單。
![記錄篩選按鈕](./media/log-filter.png)

## <a name="the-timing-data-capture-panel"></a>計時資料捕獲面板

![計時資料捕獲](./media/timing-data-capture.png)

此面板可用來從伺服器捕獲計時資訊並加以下載。 檔案使用 [Chrome 追蹤 JSON 格式](https://docs.google.com/document/d/1CvAClvFfyA5R-PhYUmn5OOQtYMH4h6I0nSsKchNAySU/edit)。 若要檢查資料，請在 URL 上開啟 Chrome `Chrome://tracing` ，然後將下載的檔案拖放到頁面中。 計時資料會以固定大小的環形緩衝區持續收集。 寫出時，capture 只會包含有關過去的資訊，這表示需要幾秒鐘到幾分鐘的時間。

## <a name="the-scene-inspection-panel"></a>場景檢查面板

![場景檢查面板](./media/scene-inspection-panel.png)

此面板會顯示所呈現場景的結構。 物件階層位於左側，所選物件的內容位於右邊。 面板是唯讀的，會即時更新。

## <a name="the-vm-debug-information-panel"></a>VM 調試資訊面板

![VM 調試資訊面板](./media/state-debugger-panel.png)

此面板提供一些調試功能。

### <a name="restart-service"></a>重新啟動服務

[ **重新開機服務** ] 按鈕會重新開機 arrInspector 所連接之虛擬機器上的執行時間。 任何附加的用戶端都會中斷連線，而且必須重載 arrInspector 頁面，才能連接到重新開機的服務。

### <a name="collect-debug-information"></a>收集調試資訊

[ **收集 vm 的偵錯工具資訊** ] 按鈕會開啟一個對話方塊，讓您觸發 ARR 實例以收集 VM 上的偵錯工具資訊：

![VM 調試資訊對話方塊](./media/state-debugger-dialog.png)

偵錯工具資訊可協助 Azure 遠端轉譯團隊分析執行中 ARR 實例中發生的任何問題。 對話方塊有文字欄位可提供其他詳細資料，例如重現問題的步驟。

按一下 [ **開始收集** ] 按鈕之後，對話方塊會關閉並開始收集程式。 收集 VM 上的資訊可能需要幾分鐘的時間。

![正在收集 VM 調試資訊](./media/state-debugger-panel-in-progress.png)

收集完成後，您會在 [ArrInspector] 視窗中收到通知。 此通知包含識別此特定集合的識別碼。 請務必儲存此識別碼，以將其傳遞給 Azure 遠端轉譯團隊。

![VM 調試資訊收集成功](./media/state-debugger-snackbar-success.png)

> [!IMPORTANT]
> 您無法下載或以其他方式存取 VM 的調試資訊。 只有 Azure 遠端轉譯團隊可以存取所收集的資料。 您需要聯繫我們，並傳送收集識別碼，讓我們調查您看到的問題。

## <a name="pause-mode"></a>暫停模式

在右上角，交換器可讓您暫停面板的即時更新。 這種模式有助於仔細檢查特定狀態。

![暫停模式](./media/pause-mode.png)

重新啟用即時更新時，所有面板都會重設。

## <a name="host-configuration"></a>主機組態

根據預設，此工具會連接到在服務 ArrInspector 的相同主機上執行的 ARR 伺服器。 不過，您可以將它設定為檢查另一部伺服器，但前提是它正在執行已開啟工具埠的 ARR 實例。

若要這樣做，請存取標題列左邊的主功能表，然後選取 [ *主機*設定]。 按一下 [ **新增主機**]，然後輸入名稱和主機名稱。 針對 *主機名稱* ，請使用結尾為的主機名稱 `.mixedreality.azure.com` ，不要包含 `http://` 或埠。

![主機設定](./media/host-configuration.png)

若要快速地從一部主機切換到另一部主機，請使用右上方的下拉式清單。

![主機下拉式方塊](./media/host-switch-combo.png)

主機清單會儲存在瀏覽器本機儲存體中，因此在重新開啟相同的瀏覽器時，將會保留該清單。
