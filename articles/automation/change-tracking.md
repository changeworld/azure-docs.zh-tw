---
title: Azure 自動化中的變更追蹤和清查總覽
description: 變更追蹤和清查可協助您識別在您的環境中發生的軟體和 Microsoft 服務變更。
services: automation
ms.subservice: change-inventory-management
ms.date: 01/28/2019
ms.topic: conceptual
ms.openlocfilehash: 1208e08f7b85e893ba754bdbdf71a2da4f68c90a
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82509055"
---
# <a name="overview-of-change-tracking-and-inventory"></a>變更追蹤和清查的總覽

本文將介紹如何在 Azure 自動化中變更追蹤和清查。 這項功能會追蹤虛擬機器和伺服器基礎結構中的變更，以協助您找出散發套件管理員所管理之軟體的操作和環境問題。 變更追蹤和清查追蹤的專案包括： 

- Windows 軟體
- Linux 軟體（套件）
- Windows 和 Linux 檔案
- Windows 登錄機碼
- Microsoft 服務
- Linux 精靈

變更追蹤和清查會從雲端中的 Azure 監視器服務取得其資料。 Azure 會將受監視伺服器上已安裝的軟體、Microsoft 服務、Windows 登錄和檔案以及 Linux 守護程式的變更傳送到 Azure 監視器進行處理。 雲端服務會將邏輯套用至接收的資料、加以記錄並使其可供使用。 

> [!NOTE]
> 若要追蹤 Azure Resource Manager 的屬性變更，請參閱 Azure Resource Graph[變更歷程記錄](../governance/resource-graph/how-to/get-resource-changes.md)。

變更追蹤和清查目前不支援下列專案：

* Windows 登錄追蹤的遞迴
* 網路檔案系統
* 不同的安裝方法
* *適用于 Windows 的 **.exe**檔案

其他限制：

* [最大檔案大小]**** 資料行和值未用於目前實作中。
* 如果您在30分鐘的收集週期內收集超過2500個檔案，解決方案效能可能會降低。
* 當網路流量很高時，變更記錄最多可能需要六個小時才會顯示。
* 如果您在電腦關閉時修改設定，則電腦可能會公佈屬於先前設定的變更。

變更追蹤和清查目前遇到下列問題：

* Windows Server 2016 Core RS3 機器不會收集 Hotfix 更新。
* 即使未發生任何變更，Linux 守護程式也可能會顯示已變更的狀態。 發生此問題的原因是 Azure 監視器`SvcRunLevels` [ConfigurationChange](https://docs.microsoft.com/azure/azure-monitor/reference/tables/configurationchange)記錄中的資料被捕捉到的方式。

## <a name="supported-operating-systems"></a>支援的作業系統

Windows 和 Linux 作業系統都支援變更追蹤和清查和 Azure 監視器 Log Analytics 代理程式。

### <a name="windows-operating-systems"></a>Windows 作業系統

正式支援的 Windows 作業系統版本為 Windows Server 2008 R2 或更新版本。

### <a name="linux-operating-systems"></a>Linux 作業系統

以下所討論的 Linux 散發套件已正式支援適用于 Linux 的 Log Analytics 代理程式。 不過，Linux 代理程式也可能在未列出的其他散發套件上執行。 除非另有說明，列出的每個主要版本都支援所有次要版本。

#### <a name="64-bit-linux-operating-systems"></a>64位 Linux 作業系統

* CentOS 6 和 7
* Amazon Linux 2017.09
* Oracle Linux 6 和 7
* Red Hat Enterprise Linux Server 6 和 7
* Debian GNU/Linux 8 和 9
* Ubuntu Linux 14.04 LTS、16.04 LTS 和 18.04 LTS
* SUSE Linux Enterprise Server 12

#### <a name="32-bit-linux-operating-systems"></a>32位 Linux 作業系統

* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 和 9
* Ubuntu Linux 14.04 LTS 和 16.04 LTS

## <a name="network-requirements"></a>網路需求

變更追蹤和清查特別需要下表所列的網路位址。 這些位址的通訊會使用埠443。

|Azure 公用  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|
|*.azure-automation.net|*.azure-automation.us|

## <a name="change-tracking-and-inventory-user-interface"></a>變更追蹤和清查使用者介面

使用 Azure 入口網站中的變更追蹤和清查，來查看受監視電腦的變更摘要。 在您的自動化帳戶中選取 [設定**管理**] 底下的 [**變更追蹤**]，即可使用此功能。 

![變更追蹤儀表板](./media/change-tracking/change-tracking-dash01.png)

下拉式清單可在儀表板頂端取得，以根據變更類型和時間範圍來限制變更追蹤圖表和詳細資訊。 您也可以按住並拖曳圖表，以選取自訂時間範圍。 

您可以按一下變更或事件，以顯示其詳細資料。 可用的變更類型如下：

* 事件
* 精靈
* 檔案
* 登錄
* 軟體
* Microsoft 服務

您可以新增、修改或移除每項變更。 在下列範例中，您可以看到服務啟動類型的變更，從手動到自動。

![變更追蹤詳細資料](./media/change-tracking/change-tracking-details.png)

## <a name="tracking-of-file-changes"></a>檔案變更的追蹤

若要追蹤 Windows 和 Linux 上檔案的變更，變更追蹤和清查都會使用檔案的 MD5 雜湊。 此功能使用雜湊來偵測自上次清查之後是否已進行變更。

## <a name="tracking-of-file-content-changes"></a>檔案內容變更的追蹤

變更追蹤和清查可讓您在檔案變更之前和之後，查看 Windows 或 Linux 檔案的內容。 對於檔案的每項變更，變更追蹤和清查都會將檔案的內容儲存在[Azure 儲存體帳戶](../storage/common/storage-create-storage-account.md)中。 當您要追蹤檔案時，可以在變更前後查看其內容。 您可以直接或並排地查看內容。 

![查看檔案中的變更](./media/change-tracking/view-file-changes.png)

## <a name="tracking-of-registry-keys"></a>登錄機碼的追蹤

變更追蹤和清查可讓您監視登錄機碼的變更。 監視可讓您找出可啟動協力廠商程式碼和惡意程式碼的擴充點。 下表列出預先設定（但未啟用）的登錄機碼。 若要追蹤這些索引鍵，您必須啟用每一個金鑰。

> [!div class="mx-tdBreakAll"]
> |登錄金鑰 | 目的 |
> | --- | --- |
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | 監視直接與 Windows Explorer 連結的一般自動啟動專案，而且通常會使用**Explorer**以同進程方式執行。
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup` | 監視在啟動時執行的指令碼。
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown` | 監視在關機時執行的指令碼。
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run` | 監視在使用者登入 Windows 帳戶之前載入的金鑰。 金鑰用於64位電腦上執行的32位應用程式。
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components` | 監視應用程式設定的變更。
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | 監視直接與 Windows Explorer 連結的一般自動啟動專案，而且通常會使用**Explorer**以同進程方式執行。
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers` | 監視直接與 Windows Explorer 連結的一般自動啟動專案，而且通常會使用**Explorer**以同進程方式執行。
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | 監視圖示覆疊處理常式註冊。
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | 監視在64位電腦上執行之32位應用程式的圖示覆迭處理常式註冊。
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Internet Explorer 之新瀏覽器協助程式物件外掛程式的監視器。 用以存取當前頁面的文件物件模型 (DOM) 並控制瀏覽。
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Internet Explorer 之新瀏覽器協助程式物件外掛程式的監視器。 用來存取目前頁面的檔物件模型（DOM），以及控制在64位電腦上執行32位應用程式的導覽。
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Internet Explorer\Extensions` | 監視新的 Internet Explorer 擴充功能，例如自訂工具功能表和自訂工具列按鈕。
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions` | 監視新的 Internet Explorer 延伸模組，例如自訂工具功能表和自訂工具列按鈕，用於64位電腦上執行的32位應用程式。
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32` | 監視與 wavemapper、wave1 和 wave2、msacm.imaadpcm、、.msadpcm、. msadpcm 和 msgsm610 相關聯的32位驅動程式。 類似于**system .ini**檔案中的 [驅動程式] 區段。
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32` | 監視與 wavemapper、wave1 和 wave2、msacm.imaadpcm、、.msadpcm、. msadpcm 和 msgsm610 相關聯的32位驅動程式，以供在64位電腦上執行的32位應用程式使用。 類似于**system .ini**檔案中的 [驅動程式] 區段。
> |`HKEY\LOCAL\MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls` | 監視已知或常用系統 Dll 的清單。 這個系統會藉由卸載系統 Dll 的木馬程式版本，防止人們利用弱式應用程式目錄許可權。
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify` | 監視可從**winlogon**接收事件通知的套件清單，這是 Windows 的互動式登入支援模型。

## <a name="support-for-file-integrity-monitoring-in-azure-security-center"></a>支援 Azure 資訊安全中心中的檔案完整性監視

變更追蹤和清查會使用 Azure 資訊安全中心的檔案[完整性監視（FIM）](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring)。 雖然 FIM 只會監視檔案和登錄，但完整的變更追蹤和清查功能也包括追蹤：

- 軟體變更
- Microsoft 服務
- Linux 精靈

## <a name="recursion-support"></a>遞迴支援

變更追蹤和清查支援遞迴，其可讓您指定萬用字元以簡化跨目錄的追蹤。 遞迴也提供了環境變數，可讓您在具有多個或動態磁碟磁碟機名稱的環境中追蹤檔案。 下列清單包含設定遞迴時應該知道的一般資訊：

* 追蹤多個檔案時需要萬用字元。
* 萬用字元只能用在路徑的最後一個區段中，例如， ** \\c:\folder file*** 或 **/etc/*. 會議**。
* 如果環境變數具有不正確路徑，驗證會成功，但路徑會在執行期間失敗。
* 設定路徑時，請避免一般路徑名稱，因為這種設定可能會導致無法遍歷太多資料夾。

## <a name="change-tracking-and-inventory-data-collection"></a>變更追蹤和清查資料收集

下表顯示變更追蹤和清查所支援變更類型的資料收集頻率。 針對每個類型，目前狀態的資料快照集也會至少每隔24小時重新整理一次。

| **變更類型** | **頻率** |
| --- | --- |
| Windows 登錄 | 50 分鐘 |
| Windows 檔案 | 30 分鐘 |
| Linux 檔案 | 15 分鐘 |
| Microsoft 服務 | 10 秒到 30 分鐘</br> 預設值：30 分鐘 |
| Linux 精靈 | 5 分鐘 |
| Windows 軟體 | 30 分鐘 |
| Linux 軟體軟體 | 5 分鐘 |

下表顯示每一部電腦的追蹤專案限制，以供變更追蹤和清查。

| **Resource** | **限制** |
|---|---|---|
|檔案|500|
|登錄|250|
|Windows 軟體（不包括修補程式） |250|
|Linux 套件|1250|
|服務|250|
|精靈|250|

使用變更追蹤和清查之機器的平均 Log Analytics 資料使用量大約為每月 40 MB。 這只是近似值，它會根據您的環境而變更。 建議您監視您的環境，查看您的實際使用量。

### <a name="microsoft-service-data"></a>Microsoft 服務資料

Microsoft 服務的預設收集頻率為30分鐘。 您可以在 [**編輯設定**] 底下的 [ **Microsoft 服務**] 索引標籤上使用滑杆來設定頻率。 

![Microsoft 服務滑杆](./media/change-tracking/windowservices.png)

為了將效能優化，Log Analytics 代理程式只會追蹤變更。 如果服務還原為其原始狀態，則設定高閾值可能會遺漏變更。 將頻率設定為較小的值，可讓您攔截可能遺漏的變更。

> [!NOTE]
> 雖然代理程式可以將變更追蹤到10秒的間隔，但資料仍然需要幾分鐘的時間才會顯示在 Azure 入口網站中。 在入口網站中顯示期間所發生的變更仍然會進行追蹤和記錄。

## <a name="support-for-alerts-on-configuration-state"></a>支援設定狀態的警示

變更追蹤和清查的主要功能，是針對混合式環境的設定狀態變更發出警示。 有許多有用的動作可用於觸發以回應警示，例如 Azure 函式、自動化 runbook、webhook 等動作。 對電腦的**C:\windows\system32\drivers\etc\hosts**檔案進行變更時發出警示，是變更追蹤和清查資料警示的一個良好應用。 此外還有許多警示案例，包括下表中定義的查詢案例。 

|查詢  |描述  |
|---------|---------|
|ConfigurationChange <br>&#124; where ConfigChangeType == "Files" and FileSystemPath contains " c:\\windows\\system32\\drivers\\"|適用于追蹤系統關鍵檔案的變更。|
|ConfigurationChange <br>&#124; where FieldsChanged contains "FileContentChecksum" and FileSystemPath == "c:\\windows\\system32\\drivers\\etc\\hosts"|適用于追蹤金鑰設定檔的修改。|
|ConfigurationChange <br>&#124;，其中 ConfigChangeType = = "Microsoft services" 和 SvcName 包含 "w3svc"，SvcState = = "已停止"|適用于追蹤系統重要服務的變更。|
|ConfigurationChange <br>&#124;，其中 ConfigChangeType = = "守護程式" 和 SvcName 包含 "ssh"，SvcState！ = "執行中"|適用于追蹤系統重要服務的變更。|
|ConfigurationChange <br>&#124; where ConfigChangeType == "Software" and ChangeCategory == "Added"|適用于需要鎖定軟體設定的環境。|
|ConfigurationData <br>&#124;，其中 SoftwareName 包含 "Monitoring Agent" 和 CurrentVersion！ = "8.0.11081.0"|適用于查看哪些機器已安裝過期或不相容的軟體版本。 此查詢會報告上次回報的設定狀態，但不會報告變更。|
|ConfigurationChange <br>&#124;，其中 RegistryKey = = @ "\\HKEY_LOCAL_MACHINE\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion QualityCompat"| 適用于追蹤重要防毒軟體金鑰的變更。|
|ConfigurationChange <br>&#124;，其中 RegistryKey 包含 @ "\\HKEY_LOCAL_MACHINE\\SYSTEM\\CurrentControlSet\\Services\\win2k3 sharedaccess\\Parameters FirewallPolicy"| 適用于追蹤防火牆設定的變更。|

## <a name="next-steps"></a>後續步驟

* 若要在 runbook 中使用變更追蹤和清查，請參閱[管理變更追蹤和清查](change-tracking-file-contents.md)。
* 若要解決變更追蹤和清查的錯誤，請參閱[疑難排解變更追蹤和清查](automation-tutorial-troubleshoot-changes.md)。
* 使用[Azure 監視器記錄檔中的記錄檔搜尋](../log-analytics/log-analytics-log-searches.md)，以查看詳細的變更追蹤資料。
