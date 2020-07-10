---
title: Azure 自動化的變更追蹤和清查概觀
description: 本文說明變更追蹤和清查功能，可協助您識別環境中的軟體和 Microsoft 服務變更。
services: automation
ms.subservice: change-inventory-management
ms.date: 06/08/2020
ms.topic: conceptual
ms.openlocfilehash: 192fd0fe73a34ca4d6ffc49badeac7ca8a080793
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86185580"
---
# <a name="change-tracking-and-inventory-overview"></a>變更追蹤和清查概觀

本文將介紹 Azure 自動化中的變更追蹤和清查功能。 這項功能會追蹤虛擬機器和伺服器基礎結構中的變更，以協助您找出散發套件管理員所管理的軟體有哪些操作和環境問題。 變更追蹤和清查所追蹤的項目包括： 

- Windows 軟體
- Linux 軟體 (套件)
- Windows 和 Linux 檔案
- Windows 登錄機碼
- Microsoft 服務
- Linux 精靈

> [!NOTE]
> 若要追蹤 Azure Resource Manager 的屬性變更，請參閱 Azure Resource Graph 的[變更歷程記錄](../governance/resource-graph/how-to/get-resource-changes.md)。

變更追蹤和清查會從 Azure 監視器取得其資料。 連線至 Log Analytics 工作區的虛擬機器會使用 Log Analytics 代理程式，來收集受監視伺服器上已安裝軟體、Microsoft 服務、Windows 登錄和檔案以及 Linux 守護程式變更的相關資料。 當資料可供使用時，代理程式會將其傳送至 Azure 監視器進行處理。 Azure 監視器會將邏輯套用至接收的資料、進行記錄並使其可供使用。 

> [!NOTE]
> 若要使用變更追蹤和清查功能，您必須在與自動化帳戶相同的訂用帳戶和區域中找出所有 VM。

變更追蹤和清查目前不支援下列項目：

- Windows 登錄追蹤的遞迴
- 網路檔案系統
- 不同安裝方法
- Windows 的 * **.exe**檔案

其他限制：

- [最大檔案大小] 資料行和值未用於目前實作中。
- 如果您在30分鐘的收集週期內收集超過2500個檔案，變更追蹤和清查效能可能會降低。
- 網路流量高時，變更記錄最多可能需要六個小時才會顯示。
- 如果您在關閉電腦時修改組態，電腦可能會提出屬於舊有組態的變更。

變更追蹤和清查目前遇到下列問題：

- 在 Windows Server 2016 Core RS3 電腦上不會收集修補程式更新。

- 即使未發生任何變更，Linux 精靈程式也可能會顯示已變更的狀態。 發生此問題的原因 `SvcRunLevels` 是 Azure 監視器[ConfigurationChange](/azure/azure-monitor/reference/tables/configurationchange)記錄中的資料被捕捉到的方式。

## <a name="supported-operating-systems"></a>支援的作業系統

所有符合 Log Analytics 代理程式需求的作業系統都可支援變更追蹤和清查。 正式的作業系統版本為 Windows Server 2008 SP1 或更新版本，以及 Windows 7 SP1 或更新版本。 許多 Linux 作業系統也支援此功能。 如需支援 Log Analytics 的作業系統，請參閱[Log analytics 代理程式總覽](../azure-monitor/platform/log-analytics-agent.md)。

若要瞭解 TLS 1.2 的用戶端需求，請參閱[Azure 自動化的 tls 1.2 強制](automation-managing-data.md#tls-12-enforcement-for-azure-automation)。

## <a name="network-requirements"></a>網路需求

變更追蹤和清查特別需要下表所列的網路位址。 這些位址的通訊會使用連接埠 443。

|Azure 公用  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com    | *.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net | *.blob.core.usgovcloudapi.net|
|*.azure-automation.net | *.azure-automation.us|

## <a name="change-tracking-and-inventory-user-interface"></a>變更追蹤和清查的使用者介面

使用 Azure 入口網站中的變更追蹤和清查，來檢視受監視電腦的變更摘要。 若要使用此功能，請在自動化帳戶中 [設定管理] 底下的 [變更追蹤] 或 [清查] 中，選取其中一個新增 VM 選項。  

![變更追蹤儀表板](./media/change-tracking/change-tracking-dash01.png)

您可以使用儀表板頂端的下拉式清單，依據變更類型和時間範圍來限制變更追蹤圖表和詳細資訊。 您也可以按住並拖曳圖表，以選取自訂時間範圍。 

您可以按一下變更或事件，以顯示其詳細資料。 可用的變更類型如下：

- 事件
- 精靈
- 檔案
- 登錄
- 軟體
- Microsoft 服務

您可以新增、修改或移除每項變更。 下列範例顯示從手動到自動的服務啟動類型變更。

![變更追蹤和清查詳細資料](./media/change-tracking/change-tracking-details.png)

## <a name="fim-support-in-azure-security-center"></a>Azure 資訊安全中心中的 FIM 支援

變更追蹤和清查會使用 [Azure 資訊安全中心檔案完整性監視 (FIM)](../security-center/security-center-file-integrity-monitoring.md)。 雖然 FIM 只會監視檔案和登錄，但完整的變更追蹤和清查功能也包括追蹤下列項目：

- 軟體變更
- Microsoft 服務
- Linux 精靈

> [!NOTE]
> 啟用完整的變更追蹤和清查功能，可能會產生額外的費用。 請參閱 [Automation 定價](https://azure.microsoft.com/pricing/details/automation/)。 您可以從 Azure 入口網站所提供的[已安裝監視解決方案清單](../azure-monitor/insights/solutions.md#list-installed-monitoring-solutions)中刪除 FIM。 請參閱[移除監視解決方案](../azure-monitor/insights/solutions.md#remove-a-monitoring-solution)。

## <a name="tracking-of-file-changes"></a>檔案變更的追蹤

為了同時追蹤 Windows 和 Linux 上檔案的變更，變更追蹤和清查會使用檔案的 MD5 雜湊。 此功能會使用雜湊來偵測自上次清查之後是否發生變更。

## <a name="tracking-of-file-content-changes"></a>檔案內容變更的追蹤

變更追蹤和清查可讓您查看 Windows 或 Linux 檔案的內容。 對於檔案的每項變更，變更追蹤和清查都會將檔案的內容儲存在 [Azure 儲存體帳戶](../storage/common/storage-account-create.md)中。 當您要追蹤檔案時，可以在變更前後查看其內容。 檔案內容可以內嵌或並排查看。 

![檢視檔案中的變更](./media/change-tracking/view-file-changes.png)

## <a name="tracking-of-registry-keys"></a>登錄機碼的追蹤

變更追蹤和清查允許監視 Windows 登錄機碼的變更。 監視可讓您找出第三方程式碼和惡意程式碼可啟用的擴充點。 下表列出預先設定 (但未啟用) 的登錄機碼。 若要追蹤這些機碼，則必須啟用每個機碼。

> [!div class="mx-tdBreakAll"]
> |登錄金鑰 | 目的 |
> | --- | --- |
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup` | 監視在啟動時執行的指令碼。
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown` | 監視在關機時執行的指令碼。
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run` | 監視使用者登入 Windows 帳戶之前載入的金鑰。 此金鑰供 64 位元電腦上執行的 32 位元應用程式使用。
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components` | 監視應用程式設定的變更。
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | 監視直接連結到 Windows Explorer 的操作功能表處理常式，而且通常會以**explorer.exe**執行同進程。
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers` | 監視直接與 Windows Explorer 連結的複製攔截處理常式，而且通常會以**explorer.exe**執行同進程。
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | 監視圖示覆疊處理常式註冊。
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | 針對在 64 位元電腦上執行的 32 位元應用程式，監視圖示覆疊處理常式註冊。
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Internet Explorer 之新瀏覽器協助程式物件外掛程式的監視器。 用以存取當前頁面的文件物件模型 (DOM) 並控制瀏覽。
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Internet Explorer 之新瀏覽器協助程式物件外掛程式的監視器。 用以存取目前頁面的文件物件模型 (DOM) 並控制 64 位元電腦上執行之 32 位元應用程式的瀏覽。
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Internet Explorer\Extensions` | 監視新的 Internet Explorer 擴充功能，例如自訂工具功能表和自訂工具列按鈕。
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions` | 針對在 64 位元電腦上執行的 32 位元應用程式，監視新的 Internet Explorer 擴充功能，例如自訂工具功能表和自訂工具列按鈕。
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32` | 監視與 wavemapper、wave1 和 wave2、msacm.imaadpcm、.msadpcm、.msgsm610 和 vidc 相關聯的 32 位元驅動程式。 類似 **system.ini** 檔案中的 [drivers] 區段。
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32` | 針對在 64 位元電腦上執行的 32 位元應用程式，監視與 wavemapper、wave1 和 wave2、msacm.imaadpcm、.msadpcm、.msgsm610 和 vidc 相關聯的 32 位元驅動程式。 類似 **system.ini** 檔案中的 [drivers] 區段。
> |`HKEY\LOCAL\MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls` | 監視已知或常用系統 DLL 的清單。 監視會藉由卸載系統 Dll 的木馬程式版本，防止人們利用弱式應用程式目錄許可權。
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify` | 監視可從 **winlogon.exe** (Windows 的互動式登入支援模型) 接收事件通知的套件清單。

## <a name="recursion-support"></a>遞迴支援

變更追蹤和清查支援遞迴，可讓您指定萬用字元以簡化跨目錄的追蹤。 遞迴也提供了環境變數，可讓您在具有多個或動態磁碟機名稱的環境中追蹤檔案。 下列清單包含設定遞迴時應該知道的一般資訊：

- 追蹤多個檔案時需要萬用字元。

- 您只能在檔案路徑的最後一個區段中使用萬用字元，例如， **c:\folder \\ file*** 或 **/etc/*. 會議**。

- 如果環境變數具有不正確路徑，驗證雖然會成功，但路徑會在執行期間失敗。

- 設定路徑時，您應該避免一般路徑名稱，因為這種設定可能會導致無法遍歷太多資料夾。

## <a name="change-tracking-and-inventory-data-collection"></a>變更追蹤和清查的資料收集

下表顯示變更追蹤和清查所支援變更類型的資料收集頻率。 對於每個類型，目前狀態的資料快照集也至少會每隔 24 小時重新整理一次。

| **變更類型** | **頻率** |
| --- | --- |
| Windows 登錄 | 50 分鐘 |
| Windows 檔案 | 30 分鐘 |
| Linux 檔案 | 15 分鐘 |
| Microsoft 服務 | 10 秒到 30 分鐘</br> 預設值：30 分鐘 |
| Linux 精靈 | 5 分鐘 |
| Windows 軟體 | 30 分鐘 |
| Linux 軟體軟體 | 5 分鐘 |

下表顯示對於變更追蹤和清查所追蹤的每個機器項目限制。

| **Resource** | **限制** |
|---|---|---|
|檔案|500|
|登錄|250|
|Windows 軟體 (不包括修正程式) |250|
|Linux 套件|1250|
|服務|250|
|精靈|250|

對於使用變更追蹤和清查的機器，其 Log Analytics 資料使用量平均大約是每月 40 MB (視您的環境而定)。 使用 Log Analytics 工作區的 [使用量和估計成本] 功能，您可以在使用量圖表中變更追蹤和清查來查看資料內嵌。 使用此資料檢視來評估您的資料使用量，並判斷它如何影響您的帳單。 請參閱[了解您的使用量並估算成本](../azure-monitor/platform/manage-cost-storage.md#understand-your-usage-and-estimate-costs)。

### <a name="microsoft-service-data"></a>Microsoft 服務資料

Microsoft 服務的預設收集頻率為 30 分鐘。 在 [編輯設定] 底下的 [Microsoft 服務] 索引標籤上，您可以使用滑杆來設定頻率。

![Microsoft 服務滑桿](./media/change-tracking/windowservices.png)

為了將效能最佳化，Log Analytics 代理程式只會追蹤變更。 設定較高的閾值時，若服務還原成其原始狀態，則可能會遺漏變更。 若將頻率設為較小的值，則可以攔截可能遺漏的變更。

> [!NOTE]
> 雖然代理程式可以追蹤 10 秒間隔內的變更，但資料仍需要幾分鐘才會顯示在 Azure 入口網站中。 在入口網站中顯示期間發生的變更仍會加以追蹤並記錄。

## <a name="support-for-alerts-on-configuration-state"></a>設定狀態的警示支援

變更追蹤和清查的重要功能，就是能夠針對混合式環境組態狀態的變更提出警示。 有許多實用動作可在警示的回應中觸發，例如 Azure 函式、自動化 Runbook 及 Webhook 等動作。 對電腦的**c:\windows\system32\drivers\etc\hosts**檔案進行變更時發出警示，是變更追蹤和清查資料警示的一個良好應用。 此外還有許多警示案例，包括下表中定義的查詢案例。

|查詢  |描述  |
|---------|---------|
|ConfigurationChange <br>&#124; where ConfigChangeType == "Files" and FileSystemPath contains " c:\\windows\\system32\\drivers\\"|適用於追蹤系統重要檔案的變更。|
|ConfigurationChange <br>&#124; where FieldsChanged contains "FileContentChecksum" and FileSystemPath == "c:\\windows\\system32\\drivers\\etc\\hosts"|適用於追蹤重要組態檔的修改。|
|ConfigurationChange <br>&#124; where ConfigChangeType == "WindowsServices" and SvcName contains "w3svc" and SvcState == "Stopped"|適用於追蹤系統重要服務的變更。|
|ConfigurationChange <br>&#124; where ConfigChangeType == "Daemons" and SvcName contains "ssh" and SvcState!= "Running"|適用於追蹤系統重要服務的變更。|
|ConfigurationChange <br>&#124; where ConfigChangeType == "Software" and ChangeCategory == "Added"|適用於需要鎖定軟體組態的環境。|
|ConfigurationData <br>&#124; where SoftwareName contains "Monitoring Agent" and CurrentVersion!= "8.0.11081.0"|適用於查看哪些電腦已過時或不符合所安裝軟體版本的規範。 此查詢會報告上次回報的組態狀態，但不會報告變更。|
|ConfigurationChange <br>&#124; where RegistryKey == @"HKEY_LOCAL_MACHINE\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\QualityCompat"| 適用於追蹤重要防毒金鑰的變更。|
|ConfigurationChange <br>&#124; where RegistryKey contains @"HKEY_LOCAL_MACHINE\\SYSTEM\\CurrentControlSet\\Services\\SharedAccess\\Parameters\\FirewallPolicy"| 適用於追蹤防火牆設定的變更。|

## <a name="next-steps"></a>後續步驟

- 若要從自動化帳戶啟用此功能，請參閱[從自動化帳戶啟用變更追蹤和清查](automation-enable-changes-from-auto-acct.md)。

- 若要流覽 Azure 入口網站來啟用此功能，請參閱[從 Azure 入口網站啟用變更追蹤和清查](automation-onboard-solutions-from-browse.md)。

- 若要從 Runbook 啟用此功能，請參閱[從 Runbook 啟用變更追蹤和清查](automation-enable-changes-from-runbook.md)。

- 若要從 Azure VM 啟用此功能，請參閱[從 Azure VM 啟用變更追蹤和清查](automation-enable-changes-from-vm.md)。
