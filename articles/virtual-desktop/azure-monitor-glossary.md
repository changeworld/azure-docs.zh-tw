---
title: 監視 Windows 虛擬桌面預覽版詞彙-Azure
description: 與 Windows 虛擬桌面 Azure 監視器相關的詞彙和概念術語。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f13909d3835bdbd2931277a88244abfae3f80759
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466358"
---
# <a name="azure-monitor-for-windows-virtual-desktop-preview-glossary"></a>適用于 Windows 虛擬桌面 (preview) 詞彙的 Azure 監視器

>[!IMPORTANT]
>適用于 Windows 虛擬桌面的 Azure 監視器目前處於公開預覽狀態。 此預覽版本是在沒有服務等級協定的情況下提供，不建議您將其用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本文列出並簡短說明與 Windows 虛擬桌面 (preview) Azure 監視器相關的主要詞彙和概念。

## <a name="alerts"></a>警示

您在訂用帳戶上設定並分類為 [嚴重性 1](#severity-1-alerts) 的任何作用中 Azure 監視器警示都會顯示在 [總覽] 頁面中。 若要瞭解如何設定警示，請參閱 [使用 Azure 監視器警示來回應事件](../azure-monitor/learn/tutorial-response.md)。

## <a name="available-sessions"></a>可用的工作階段

可用的會話會顯示主機集區中的可用會話數目。 服務會將 (Vm 的虛擬機器數目乘以每個虛擬機器允許的會話數目上限) ，然後減去會話總數，以計算此數目。

## <a name="connection-success"></a>連接成功

此專案會顯示連接健全狀況。 「連線成功」表示連接可以觸達主機，由該虛擬機器上的堆疊確認。 連接失敗表示連線無法連線到主機。

## <a name="daily-active-users-dau"></a>每日有效使用者 (DAU) 

過去24小時內已啟動會話的使用者總數。

## <a name="daily-alerts"></a>每日警示

過去24小時內觸發的 [嚴重性1警示](#severity-1-alerts) 總數。

## <a name="daily-connections-and-reconnections"></a>每日連線和重新連線

過去24小時內啟動或完成的連線和重新連接總數。

## <a name="daily-connected-hours"></a>每日連線時數

過去24小時內跨使用者連線到會話所花費的總時數。

## <a name="diagnostics-and-errors"></a>診斷和錯誤

在 Windows 虛擬桌面的 Azure 監視器中出現錯誤或警示時，會將其分類為三個專案：

- 活動類型：此類別是 Windows 虛擬桌面診斷將錯誤分類的方式。 這些類別是管理活動、摘要、連接、主機註冊、錯誤和檢查點。 若要深入瞭解這些類別， [請參閱使用 Log Analytics 的診斷功能](diagnostics-log-analytics.md)。

- 種類：此類別會顯示錯誤的位置。 

     - 在 Windows 虛擬桌面服務中，標示為「服務」或「ServiceError = TRUE」的錯誤發生。
     - 標示為「部署」或標記為 "ServiceError = FALSE" 的錯誤發生在 Windows 虛擬桌面服務之外。
     - 若要深入瞭解 ServiceError 標記，請參閱 [常見的錯誤案例](diagnostics-role-service.md#common-error-scenarios)。

- 來源：此類別可提供錯誤發生位置的更具體描述。

     - 診斷：負責監視和報表服務活動的服務角色，讓使用者可以觀察及診斷部署問題。

     - RDBroker：負責協調部署活動、維護物件狀態、驗證驗證等的服務角色。

     - RDGateway：負責處理使用者和虛擬機器之間的網路連線能力的服務角色。

     - RDStack：在您的 Vm 上安裝的軟體元件，可讓它們與 Windows 虛擬桌面服務進行通訊。

     - 用戶端：在終端使用者電腦上執行的軟體，提供 Windows 虛擬桌面服務的介面。 它會顯示已發佈資源的清單，並在您進行選取後裝載遠端桌面連線。

每個診斷問題或錯誤都包含說明發生錯誤的訊息。 若要深入瞭解錯誤的疑難排解，請參閱 [識別及診斷 Windows 虛擬桌面問題](diagnostics-role-service.md)。

## <a name="input-delay"></a>輸入延遲

Windows 虛擬桌面 Azure 監視器中的「輸入延遲」表示每個會話的 [每個進程的輸入延遲] 效能計數器。 在 <aka.ms/azmonwvdi> 的 [主機效能] 頁面中，此效能計數器設定為每隔30秒就會將報告傳送至服務一次。 這30秒的間隔稱為「範例」，而在該視窗中回報最糟的案例。 中間值和 p95 值會反映所有樣本之間的中間值和第95個百分位數。

在 [ **依主機輸入延遲**] 下，您可以選取工作階段主機列，將頁面中的所有其他視覺效果篩選到該主控制項。 您也可以選取處理常式名稱，以篩選一段時間內的中位數輸入延遲圖表。

我們將延遲分為下列幾個類別：

- 良好：低於150毫秒。
- 可接受：150-500 毫秒。
- 差： 500-2000 毫秒 (低於2秒) 。
- 錯誤：超過2000毫秒 (2 秒，) 。

若要深入瞭解輸入延遲計數器的運作方式，請參閱 [使用者輸入延遲效能計數器](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/)。

##  <a name="monthly-active-users-mau"></a>每月使用中的使用者 (MAU)

過去28天內已啟動會話的使用者總數。 如果您儲存30天以內的資料，您可能會在有少於28天可用資料的期間內看到低於預期的 MAU 與連接值。

## <a name="performance-counters"></a>效能計數器

效能計數器會顯示硬體元件、作業系統和應用程式的效能。

下表列出 Azure 監視器用於 Windows 虛擬桌面的建議效能計數器和時間間隔：

|效能計數器名稱|時間間隔|
|---|---|
|邏輯磁片 (C： ) \\ 平均磁片佇列長度|30 秒|
|邏輯磁片 (C： ) \\ Avg. Disk sec/Transfer|60 秒|
|邏輯磁片 (C： ) \\ 目前磁片佇列長度|30 秒|
|記憶體 (\*) \\ 可用 mb|30 秒|
|記憶體 (\*) \\ 分頁錯誤數/秒|30 秒|
|記憶體 (\*) \\ 頁/秒|30 秒|
|記憶體 (\*) \\ % 認可的使用中位元組|30 秒|
|PhysicalDisk (\*) \\ Avg. Disk Queue Length|30 秒|
|PhysicalDisk (\*) \\ Avg. Disk Sec/Read|30 秒|
|PhysicalDisk (\*) \\ Avg. Disk Sec/Transfer|30 秒|
|PhysicalDisk (\*) \\ Avg. Disk Sec/Write|30 秒|
|進程 (\*) \\ % 處理器時間|20 秒|
|進程 (\*) \\ % 使用者時間|30 秒|
|進程 (\*) \\ 執行緒計數|30 秒|
|處理 (\*) \\ IO 寫入作業/秒|30 秒|
|處理 (\*) \\ IO 讀取作業/秒|30 秒|
|處理器資訊 (_Total) \\ % 處理器時間|30 秒|
|終端機服務 () 作用中 \* \\ 會話|60 秒|
|終端機服務 (\*) 非使用中 \\ 會話|60 秒|
|終端機服務 (\*) \\ 會話總數|60 秒|
|\*每一進程的使用者輸入延遲 (\*) \\ 最大輸入處理|30 秒|
|\*每個會話的使用者輸入延遲 (\*) \\ 最大輸入延遲|30 秒|
|RemoteFX 網路 (\*) \\ 目前的 TCP RTT|30 秒|
|RemoteFX 網路 (\*) \\ 目前的 UDP 頻寬|30 秒|

若要深入瞭解如何讀取效能計數器，請參閱設定 [效能計數器](../azure-monitor/platform/data-sources-performance-counters.md)。

若要深入瞭解輸入延遲效能計數器，請參閱 [使用者輸入延遲效能計數器](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/)。

## <a name="potential-connectivity-issues"></a>潛在的連接問題

可能的連線問題顯示主機、使用者、已發佈的資源，以及連線失敗率較高的用戶端。 一旦您選擇「報表依據」篩選準則，您就可以藉由檢查這些資料行中的值來評估問題的嚴重性：

- 嘗試 (連接嘗試次數) 
- 已發佈的應用程式或桌上型電腦的資源 (數目) 
- 裝載 (Vm 數目) 
- 用戶端

例如，如果您選取 [ **依使用者** 篩選]，可以在 [ **嘗試** ] 欄位中查看每個使用者的連接嘗試。

如果您發現連接問題跨越多個主機、使用者、資源或用戶端，則可能是問題會影響整個系統。 如果不是，則會是較低優先順序的較小問題。

您也可以選取專案來查看其他資訊。 您可以查看與問題相關的主機、資源和用戶端版本。 顯示器也會顯示連接嘗試期間所報告的任何錯誤。

## <a name="round-trip-time-rtt"></a>來回時間 (RTT) 

來回時間 (RTT) 是在使用者的位置和 VM 的 Azure 區域之間的連接往返時間估計。 若要查看哪些位置有最大的延遲，請在 [Windows 虛擬桌面體驗估算器工具](https://azure.microsoft.com/services/virtual-desktop/assessment/)中尋找您想要的位置。

## <a name="session-history"></a>工作階段歷程

[ **會話** ] 專案會顯示所有會話、已連線和已中斷連線的狀態。 **閒置會話** 只會顯示已中斷連線的會話。

## <a name="severity-1-alerts"></a>嚴重性1警示

您必須立即處理的最緊急專案。 如果您沒有解決這些問題，可能會導致您的 Windows 虛擬桌面部署停止運作。

## <a name="time-to-connect"></a>要連線的時間

[連線時間] 是使用者開始其會話和會計入服務的時間之間的時間。 建立新連接的時間通常比重新建立現有的連接還要久。

## <a name="user-report"></a>使用者報表

[使用者報表] 頁面可讓您查看特定使用者的連接歷程記錄和診斷資訊。 每個使用者報表都會顯示使用模式、使用者意見反應，以及使用者在其會話期間遇到的任何錯誤。 最小的問題可透過使用者意見來解決。 如果您需要更深入瞭解，您也可以篩選特定連接識別碼或一段時間的相關資訊。

## <a name="users-per-core"></a>每個核心的使用者

這是每個虛擬機器核心中的使用者數目。 追蹤一段時間內每個核心的使用者數目上限，可協助您識別環境是否以每個核心的高、低或變動的使用者數目一致地執行。 知道有多少使用者在使用中，將可協助您有效率地調整環境的資源和規模。

## <a name="windows-events"></a>Windows 事件

Windows 事件記錄檔是 Windows 虛擬機器上的 Log Analytics 代理程式所收集的資料來源。 您可以從標準記錄（例如系統和應用程式），以及您需要監視之應用程式所建立的自訂記錄收集事件。

下表列出適用于 Windows 虛擬桌面的 Azure 監視器所需的 Windows 事件：

|事件名稱|事件類型|
|---|---|
|應用程式|錯誤和警告|
|Microsoft-Windows-Microsoft-windows-terminalservices-gateway-RemoteConnectionManager/Admin|錯誤、警告和資訊|
|Microsoft-Windows-Microsoft-windows-terminalservices-gateway-LocalSessionManager/Operational|錯誤、警告和資訊|
|系統|錯誤和警告|
| Microsoft-FSLogix-應用程式/操作|錯誤、警告和資訊|
|Microsoft-FSLogix-應用程式/系統管理員|錯誤、警告和資訊|

若要深入瞭解 Windows 事件，請參閱 [windows 事件記錄屬性](../azure-monitor/platform/data-sources-windows-events.md)。

## <a name="next-steps"></a>後續步驟

若要開始使用適用于 Windows 虛擬桌面的 Azure 監視器，請參閱下列文章：

- [使用適用于 Windows 虛擬桌面的 Azure 監視器來監視您的部署](azure-monitor.md)
- [適用于 Windows 虛擬桌面的 Azure 監視器疑難排解](troubleshoot-azure-monitor.md)

您也可以設定 Azure Advisor，以協助您瞭解如何解決或防止常見的問題。 深入瞭解 [使用 Windows 虛擬桌面的 Azure Advisor](azure-advisor.md)。

如果您需要協助或有任何疑問，請參閱我們的社區資源：

- 在 [Windows 虛擬桌面 >techcommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)提出問題或對社區提出建議。
   
- 若要瞭解如何離開意見反應，請參閱 [疑難排解總覽、意見反應，以及 Windows 虛擬桌面的支援](troubleshoot-set-up-overview.md#report-issues)。

- 您也可以在 [Windows 虛擬桌面意見反應中樞](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app) 或 [UserVoice 論壇](https://windowsvirtualdesktop.uservoice.com/forums/921118-general)中留下 windows 虛擬桌面的意見反應。
