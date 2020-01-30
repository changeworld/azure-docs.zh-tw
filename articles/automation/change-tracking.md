---
title: 使用 Azure 自動化追蹤變更
description: 「變更追蹤」解決方案可協助您識別您環境中發生的軟體及 Windows 服務變更。
services: automation
ms.subservice: change-inventory-management
ms.date: 01/28/2019
ms.topic: conceptual
ms.openlocfilehash: 83babd65fdf22ab40b0137d93a1cbe7f1fd7ff04
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844797"
---
# <a name="track-changes-in-your-environment-with-the-change-tracking-solution"></a>使用變更追蹤解決方案來追蹤環境中的變更

本文將協助您使用變更追蹤解決方案，輕鬆地找出您環境中的變更。 解決方案會追蹤下列設定變更，以協助您找出操作問題：

- Windows 軟體
- Linux 軟體（套件）

    >[!NOTE]
    >變更追蹤只會追蹤使用散發套件管理員所管理的軟體。

- Windows 和 Linux 檔案
- Windows 登錄機碼
- Windows 服務
- Linux 精靈

受監視伺服器上安裝的軟體、Windows 服務、Windows 登錄和檔案以及 Linux 精靈的變更，會傳送至雲端中的 Azure 監視器服務進行處理。 會將邏輯套用至接收的資料，且雲端服務會記錄資料。 使用 [變更追蹤] 儀表板上的資訊，您可以輕鬆地看到您的伺服器基礎結構中所做的變更。

> [!NOTE]
> Azure 自動化變更追蹤會追蹤虛擬機器中的變更。 若要追蹤 Azure Resource Manager 屬性變更，請參閱 Azure Resource Graph 的[變更歷程記錄](../governance/resource-graph/how-to/get-resource-changes.md)。

## <a name="supported-windows-operating-systems"></a>支援的 Windows 作業系統

Windows 代理程式正式支援下列 Windows 作業系統版本：

* Windows Server 2008 R2 或更新版本

## <a name="supported-linux-operating-systems"></a>支援的 Linux 作業系統

以下為正式支援的 Linux 散發套件。 不過，Linux 代理程式也可能在未列出的其他散發套件上執行。 除非另有說明，列出的每個主要版本都支援所有次要版本。

### <a name="64-bit"></a>64 位元

* CentOS 6 和 7
* Amazon Linux 2017.09
* Oracle Linux 6 和 7
* Red Hat Enterprise Linux Server 6 和 7
* Debian GNU/Linux 8 和 9
* Ubuntu Linux 14.04 LTS、16.04 LTS 和 18.04 LTS
* SUSE Linux Enterprise Server 12

### <a name="32-bit"></a>32 位元

* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 和 9
* Ubuntu Linux 14.04 LTS 和 16.04 LTS

## <a name="onboard"></a>啟用變更追蹤和清查

若要開始追蹤變更，您必須啟用變更追蹤和清查解決方案。 有許多方法可讓機器上線來變更追蹤和清查。 下列是使解決方案上線的建議和支援方式。

* [從虛擬機器](automation-onboard-solutions-from-vm.md)
* [從瀏覽多部電腦](automation-onboard-solutions-from-browse.md)
* [從自動化帳戶](automation-onboard-solutions-from-automation-account.md)
* [使用 Azure 自動化 Runbook](automation-onboard-solutions.md)

## <a name="configuring-change-tracking-and-inventory"></a>設定變更追蹤和清查

若要了解如何將電腦登入解決方案，請瀏覽：[登入自動化解決方案](automation-onboard-solutions-from-automation-account.md)。 一旦您的電腦上架了變更追蹤和清查解決方案，您就可以設定要追蹤的專案。當您啟用要追蹤的新檔案或登錄機碼時，它會同時啟用變更追蹤和清查。

如需追蹤 Windows 和 Linux 上的檔案變更，則會使用檔案的 MD5 雜湊。 這些雜湊接著用來偵測自上次清查後是否已進行變更。

### <a name="file-integrity-monitoring-in-azure-security-center"></a>Azure 資訊安全中心的檔案完整性監視

Azure 資訊安全中心已新增 Azure 變更追蹤上建立的檔案完整性監視（FIM）。 雖然 FIM 只會監視檔案和登錄，但完整的變更追蹤解決方案也包含：

- 軟體變更
- Windows 服務
- Linux 守護程式

如果您已經啟用 FIM，而且想要試用完整的變更追蹤解決方案，您需要執行下列步驟。 這個進程不會移除您的設定。

> [!NOTE]
> 啟用完整變更追蹤解決方案可能會產生額外的費用。如需詳細資訊，請參閱[自動化定價](https://azure.microsoft.com/pricing/details/automation/)。

1. 流覽至工作區，然後在[已安裝的監視解決方案清單](../azure-monitor/insights/solutions.md#list-installed-monitoring-solutions)中尋找，以移除監視解決方案。
2. 按一下解決方案的名稱以開啟其 [摘要] 頁面，然後按一下 [刪除]，如[移除監視解決方案](../azure-monitor/insights/solutions.md#remove-a-monitoring-solution)中所述。
3. 流覽至 [自動化帳戶]，然後從 [設定**管理**] 下的 [資源] 功能表中選取 [**變更追蹤**]，以重新啟用解決方案。
4. 確認您的工作區設定詳細資料，然後按一下 [**啟用**]。

### <a name="configure-linux-files-to-track"></a>設定要追蹤的 Linux 檔案

使用下列步驟，設定要在 Linux 電腦上追蹤的檔案：

1. 從您的自動化帳戶中，選取 [組態管理] 下的 [變更追蹤]。 按一下 [編輯設定] \(齒輪符號)。
2. 在 [變更追蹤] 頁面上選取 [Linux 檔案]，然後按一下 [+ 新增]，以新增要追蹤的新檔案。
3. 在 [為變更追蹤新增 Linux 檔案] 上，輸入要追蹤的檔案或目錄資訊，然後按一下 [儲存]。

|屬性  |說明  |
|---------|---------|
|啟用     | 判斷是否已套用設定。        |
|項目名稱     | 要追蹤之檔案的易記名稱。        |
|群組     | 以邏輯方式分組檔案的群組名稱。        |
|輸入路徑     | 要檢查檔案的路徑。 例如："/etc/*.conf"       |
|路徑類型     | 要追蹤的項目類型，可能值為 [檔案] 和 [目錄]。        |
|遞迴     | 決定在尋找所要追蹤的項目時是否使用遞迴。        |
|使用 Sudo     | 此設定會決定在檢查項目時是否使用 sudo。         |
|連結     | 此設定會決定在周遊目錄時處理符號連結的方式。<br> **忽略** - 忽略符號連結，而不包含參考的檔案/目錄。<br>**遵循** - 在遞迴期間遵循符號連結，並且包含參考的檔案/目錄。<br>**管理** - 遵循符號連結並允許變更傳回的內容。     |
|上傳所有的檔案內容設定| 開啟或關閉追蹤變更上的檔案內容上傳。 可用的選項：**True** 或 **False**。|

> [!NOTE]
> 不建議選擇「管理」連結選項。 不支援檔案內容擷取。

### <a name="configure-windows-files-to-track"></a>設定要追蹤的 Windows 檔案

使用下列步驟，設定要在 Windows 電腦上追蹤的檔案：

1. 從您的自動化帳戶中，選取 [組態管理] 下的 [變更追蹤]。 按一下 [編輯設定] \(齒輪符號)。
2. 在 [變更追蹤] 頁面上選取 [Windows 檔案]，然後按一下 [+ 新增]，以新增要追蹤的新檔案。
3. 在 [為變更追蹤新增 Windows 檔案] 上，輸入要追蹤之檔案的資訊，然後按一下 [儲存]

|屬性  |說明  |
|---------|---------|
|啟用     | 判斷是否已套用設定。        |
|項目名稱     | 要追蹤之檔案的易記名稱。        |
|群組     | 以邏輯方式分組檔案的群組名稱。        |
|輸入路徑     | 要檢查檔案的路徑，例如："c:\temp\\\*.txt"<br>您也可以使用環境變數，例如 "%winDir%\System32\\\*.*"       |
|遞迴     | 決定在尋找所要追蹤的項目時是否使用遞迴。        |
|上傳所有的檔案內容設定| 開啟或關閉追蹤變更上的檔案內容上傳。 可用的選項：**True** 或 **False**。|

## <a name="wildcard-recursion-and-environment-settings"></a>萬用字元、遞迴和環境設定

遞迴可讓您指定萬用字元，以簡化跨目錄的追蹤，而環境變數則可讓您以多個或動態的磁碟機名稱跨環境追蹤檔案。 以下清單顯示您在設定遞迴時所應知悉的一般資訊：

* 追蹤多個檔案時需使用萬用字元
* 使用萬用字元時，這些字元只能在路徑的最後一個部分使用。 (例如 `c:\folder\*file*` 或 `/etc/*.conf`)
* 如果環境變數具有無效路徑，驗證仍會成功，但執行清查時，該路徑將會失敗。
* 在設定路徑應避免使用 `c:\*.*` 這類的一般路徑，因為這會導致過多資料夾的周遊。

## <a name="configure-file-content-tracking"></a>設定檔案內容追蹤

您可以使用檔案內容變更追蹤，以檢視檔案變更之前和之後的內容。 此功能可用於 Windows 和 Linux 檔案。對於檔案的每項變更，檔案的內容會儲存在儲存體帳戶中，並以內嵌或並排方式來顯示變更之前和之後的檔案。 若要進一步了解，請參閱[檢視追蹤檔案的內容](change-tracking-file-contents.md)。

![檢視檔案中的變更](./media/change-tracking-file-contents/view-file-changes.png)

### <a name="configure-windows-registry-keys-to-track"></a>設定要追蹤的 Windows 登錄機碼

使用下列步驟，設定要在 Windows 電腦上追蹤的登錄機碼：

1. 從您的自動化帳戶中，選取 [組態管理] 下的 [變更追蹤]。 按一下 [編輯設定] \(齒輪符號)。
2. 在 [變更追蹤] 頁面上選取 [Windows 登錄]，然後按一下 [+ 新增]，以新增要追蹤的新登錄機碼。
3. 在 [為變更追蹤新增 Windows 登錄] 上，輸入要追蹤之機碼的資訊，然後按一下 [儲存]。

|屬性  |說明  |
|---------|---------|
|啟用     | 判斷是否已套用設定。        |
|項目名稱     | 要追蹤之登錄機碼的易記名稱。        |
|群組     | 以邏輯方式分組登錄機碼的群組名稱。        |
|Windows 登錄機碼   | 要檢查登錄機碼的路徑。 例如："HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup"      |

## <a name="limitations"></a>限制

變更追蹤解決方案目前不支援下列項目︰

* Windows 登錄追蹤的遞迴
* 網路檔案系統
* 不會追蹤不同的安裝方法
* \* .exe 檔案不會針對 Windows 追蹤

其他限制：

* [最大檔案大小] 資料行和值未用於目前實作中。
* 如果您在 30 分鐘的收集週期收集超過 2500 個檔案，則解決方案效能可能會下降。
* 網路流量高時，變更記錄最多可能需要六個小時才會顯示。
* 如果您在關閉電腦時修改組態，電腦可能會提出屬於舊有組態的變更。

## <a name="known-issues"></a>已知問題

「變更追蹤」解決方案目前有下列問題︰

* Windows Server 2016 Core RS3 機器不會收集 Hotfix 更新。
* 即使沒有任何變更，Linux 守護程式也可能會顯示已變更的狀態。 這是因為 `SvcRunLevels` 欄位的捕捉方式所致。

## <a name="change-tracking-data-collection-details"></a>「變更追蹤」資料收集詳細資訊

下表顯示各變更類型的資料收集頻率。 對於每個類型，目前狀態的資料快照集也至少會每隔 24 小時重新整理一次：

| **變更類型** | **頻率** |
| --- | --- |
| Windows 登錄 | 50 分鐘 |
| Windows 檔案 | 30 分鐘 |
| Linux 檔案 | 15 分鐘 |
| Windows 服務 | 10 秒到 30 分鐘</br> 預設值：30 分鐘 |
| Linux 精靈 | 5 分鐘 |
| Windows 軟體 | 30 分鐘 |
| Linux 軟體軟體 | 5 分鐘 |

下表顯示對於變更追蹤所追蹤的每個機器項目限制。

| **Resource** | **限制**| **注意事項** |
|---|---|---|
|檔案|500||
|登錄|250||
|Windows 軟體|250|不包含軟體修補程式|
|Linux 套件|1250||
|服務|250||
|精靈|250||

使用變更追蹤與詳細目錄之機器的平均 Log Analytics 資料使用量大約是每月 40 MB。 這只是近似值，它會根據您的環境而變更。 建議您監視您的環境，查看您的實際使用量。

### <a name="windows-service-tracking"></a>Windows 服務追蹤

Windows 服務的預設收集頻率為 30 分鐘。 若要設定頻率，請移至**變更追蹤**。 在 [Windows 服務] 索引標籤上的 [編輯設定] 下有一個滑桿，可以將 Windows 服務的收集頻率從 10 秒變更為長達 30 分鐘。 將滑桿移至您想要的頻率，它會自動儲存。

![Windows 服務滑桿](./media/change-tracking/windowservices.png)

代理程式只會追蹤變更，這樣可最佳化代理程式的效能。 設定較高的閾值時，若服務還原成原始狀態，則可能會遺漏變更。 若將頻率設為較小的值，則可以攔截可能遺漏的變更。

> [!NOTE]
> 雖然代理程式可以追蹤 10 秒間隔內的變更，但資料仍需要幾分鐘才會顯示在入口網站中。 在入口網站中顯示所需時間內仍會追蹤並記錄變更。

### <a name="registry-key-change-tracking"></a>登錄機碼變更追蹤

監視登錄機碼變更的目的是找出第三方程式碼和惡意程式碼可啟用的擴充點。 下列清單顯示預先設定的登錄機碼清單。 這些機碼已設定，但並未啟用。 若要追蹤這些登錄機碼，必須每個都啟用。

> [!div class="mx-tdBreakAll"]
> |登錄金鑰 | 目的 |
> |---------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | 監視一般自動啟動項目，而這些項目直接連結到 Windows 檔案總管，而且通常會使用 Explorer.exe 以內含方式執行。
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup` | 監視在啟動時執行的指令碼。
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown` | 監視在關機時執行的指令碼。
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run` | 監視使用者登入其 Windows 帳戶之前載入的金鑰。 此金鑰供 64 位元電腦上執行的 32 位元程式使用。
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components` | 監視應用程式設定的變更。
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | 監視一般自動啟動項目，而這些項目直接連結到 Windows 檔案總管，而且通常會使用 Explorer.exe 以內含方式執行。
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers` | 監視一般自動啟動項目，而這些項目直接連結到 Windows 檔案總管，而且通常會使用 Explorer.exe 以內含方式執行。
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | 監視圖示覆疊處理常式註冊。
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | 針對在 64 位元電腦上執行的 32 位元程式，監視圖示覆疊處理常式註冊。
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Internet Explorer 之新瀏覽器協助程式物件外掛程式的監視器。 用以存取當前頁面的文件物件模型 (DOM) 並控制瀏覽。
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Internet Explorer 之新瀏覽器協助程式物件外掛程式的監視器。 用以存取目前頁面的文件物件模型 (DOM) 並控制 64 位元電腦上執行之 32 位元程式的瀏覽。
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Internet Explorer\Extensions` | 監視新的 Internet Explorer 擴充功能，例如自訂工具功能表和自訂工具列按鈕。
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions` | 針對在 64 位元電腦上執行的 32 位元程式，監視新的 Internet Explorer 擴充功能，例如自訂工具功能表和自訂工具列按鈕。
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32` | 監視與 wavemapper、wave1 和 wave2、msacm.imaadpcm、.msadpcm、.msgsm610 和 vidc 相關聯的 32 位元驅動程式。 類似 SYSTEM.INI 檔案中的 [drivers] 區段。
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32` | 針對在 64 位元電腦上執行的 32 位元程式，監視與 wavemapper、wave1 和 wave2、msacm.imaadpcm、.msadpcm、.msgsm610 和 vidc 相關聯的 32 位元驅動程式。 類似 SYSTEM.INI 檔案中的 [drivers] 區段。
> |`HKEY\LOCAL\MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls` | 監視已知或常用系統 DLL 清單；此系統可防止人員放入特洛伊木馬病毒版本的系統 DLL 來利用弱式應用程式目錄權限。
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify` | 監視可從 Winlogon 接收事件通知的套件清單，而 Winlogon 是 Windows 作業系統的互動式登入支援模型。

## <a name="network-requirements"></a>網路需求

以下為「變更追蹤」特別需要的位址。 這些位址的通訊皆經由連接埠 443 進行。

|Azure 公用  |Azure 政府機構  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|
|*.azure-automation.net|*.azure-automation.us|

## <a name="use-change-tracking"></a>使用變更追蹤

此解決方案啟用後，您可以在自動化帳戶中選取 [組態管理] 下的 [變更追蹤]，以檢視受監視電腦的變更摘要。

您可以檢視電腦的變更，然後向下鑽研每個事件的詳細資料。 您可以使用圖表頂端的下拉式清單，依據變更類型和時間範圍來限制圖表和詳細資訊。 您也可以按住並拖曳圖表，以選取自訂時間範圍。 **變更類型**將會是下列其中一個值 **：事件**、守護**程式、檔案、登錄、** **軟體** **、** **Windows 服務**。 類別會顯示變更的類型，而且可以**新增**、**修改**或**移除**。

![變更追蹤儀表板的影像](./media/change-tracking/change-tracking-dash01.png)

按一下變更或事件，會顯示關於該變更的詳細資訊。 如範例所示，服務的啟動類型已從 [手動] 變更為 [自動]。

![變更追蹤詳細資料的影像](./media/change-tracking/change-tracking-details.png)

## <a name="search-logs"></a>搜尋記錄

除了入口網站中提供的詳細資訊以外，您也可以對記錄執行搜尋。 開啟 [**變更追蹤**] 頁面，按一下 [ **Log Analytics**]，這會開啟 [**記錄**檔] 頁面。

### <a name="sample-queries"></a>範例查詢

下表提供此解決方案所收集之變更記錄的記錄搜尋範例：

|查詢  |說明  |
|---------|---------|
|ConfigurationData<br>&#124; where   ConfigDataType == "WindowsServices" and SvcStartupType == "Auto"<br>&#124; where SvcState == "Stopped"<br>&#124; summarize arg_max(TimeGenerated, *) by SoftwareName, Computer         | 針對已設為 [自動]、但回報為 [正在停止] 的 Windows 服務顯示最新的清查記錄<br>結果會限定為該 SoftwareName 和電腦的最新記錄      |
|ConfigurationChange<br>&#124; where ConfigChangeType == "Software" and ChangeCategory == "Removed"<br>&#124; order by TimeGenerated desc|顯示已移除之軟體的變更記錄|

## <a name="alert-on-changes"></a>變更警示

變更追蹤和清查的重要功能，就是能夠對組態狀態和混合式環境組態狀態的任何變更提出警示。

在下列範例中，螢幕擷取畫面顯示電腦上的檔案 `C:\windows\system32\drivers\etc\hosts` 已修改。 這個檔案很重要，因為 Windows 會使用 Hosts 檔案來解析 IP 位址的主機名稱，其優先順序甚至高於 DNS，這可能會導致連線問題或流量重新導向至惡意或者危險的網站。

![顯示 hosts 檔案變更的圖表](./media/change-tracking/changes.png)

若要進一步分析這項變更，請按一下 **Log Analytics** 移至記錄搜尋。 在記錄搜尋中，透過查詢 `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"` 搜尋 Hosts 檔案的內容變更。 此查詢會尋找變更，其中包括完整路徑包含 "hosts" 這個字的檔案的檔案內容變更。 您也可以將路徑部分變更為其完整形式 (例如 `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"`)，以要求特定檔案。

在查詢傳回所需的結果之後，按一下記錄搜尋體驗中的 [新增警示規則] 按鈕，以開啟警示建立頁面。 您也可以在 Azure 入口網站中透過 [Azure 監視器] 瀏覽至此體驗。 在警示建立體驗中，再次檢查我們的查詢並修改警示邏輯。 在此情況下，即使在環境中的所有電腦上偵測到一項變更，也要觸發警示。

![顯示變更查詢以便追蹤 hosts 檔案變更的影像](./media/change-tracking/change-query.png)

設定條件邏輯之後，指派動作群組來執行動作以回應正在觸發的警示。 在此情況下，我已設定要傳送的電子郵件和要建立的 ITSM 票證。  此外，也可以採取許多其他有用的動作，例如觸發 Azure 函式、自動化 Runbook、Webhook 或邏輯應用程式。

![設定警示變更的動作群組的影像](./media/change-tracking/action-groups.png)

設定所有的參數和邏輯之後，我們可以在環境中套用警示。

### <a name="alert-suggestions"></a>警示建議

雖然對變更追蹤或清查資料而言，Hosts 檔案的變更警示是一個很好的警示應用，但是有更多警示案例，包括已定義的情況及其在下一節的範例查詢。

|查詢  |說明  |
|---------|---------|
|ConfigurationChange <br>&#124; where ConfigChangeType == "Files" and FileSystemPath contains " c:\\windows\\system32\\drivers\\"|適用於追蹤系統重要檔案的變更|
|ConfigurationChange <br>&#124; where FieldsChanged contains "FileContentChecksum" and FileSystemPath == "c:\\windows\\system32\\drivers\\etc\\hosts"|適用於追蹤重要組態檔的修改|
|ConfigurationChange <br>&#124; where ConfigChangeType == "WindowsServices" and SvcName contains "w3svc" and SvcState == "Stopped"|適用於追蹤系統重要服務的變更|
|ConfigurationChange <br>&#124; where ConfigChangeType == "Daemons" and SvcName contains "ssh" and SvcState != "Running"|適用於追蹤系統重要服務的變更|
|ConfigurationChange <br>&#124; where ConfigChangeType == "Software" and ChangeCategory == "Added"|適用於需要鎖定軟體組態的環境|
|ConfigurationData <br>&#124; where SoftwareName contains "Monitoring Agent" and CurrentVersion != "8.0.11081.0"|適用於查看哪些電腦已過時或不符合所安裝軟體版本的規範。 它會報告上次所回報的組態狀態，毫無變更。|
|ConfigurationChange <br>&#124;where RegistryKey = = @ "HKEY_LOCAL_MACHINE\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\QualityCompat"| 適用於追蹤重要防毒金鑰的變更|
|ConfigurationChange <br>&#124;其中，RegistryKey 包含 @ "HKEY_LOCAL_MACHINE\\SYSTEM\\CurrentControlSet\\Services\\Win2k3 sharedaccess\\Parameters\\FirewallPolicy"| 適用於追蹤防火牆設定的變更|

## <a name="next-steps"></a>後續步驟

瀏覽變更追蹤的教學課程，以深入了解如何使用此解決方案：

> [!div class="nextstepaction"]
> [對您環境中的變更進行疑難排解](automation-tutorial-troubleshoot-changes.md)

* 使用[Azure 監視器記錄檔中的記錄檔搜尋](../log-analytics/log-analytics-log-searches.md)，以查看詳細的變更追蹤資料。
