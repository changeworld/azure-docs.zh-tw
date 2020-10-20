---
title: Azure 自動化的變更追蹤和清查概觀
description: 本文說明變更追蹤和清查功能，可協助您識別環境中的軟體和 Microsoft 服務變更。
services: automation
ms.subservice: change-inventory-management
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 9654529723b5b69c15358be9e06db4f8cbed35e3
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2020
ms.locfileid: "92209588"
---
# <a name="change-tracking-and-inventory-overview"></a>變更追蹤和清查概觀

本文將介紹 Azure 自動化中的變更追蹤和清查功能。 這項功能會追蹤 Azure、內部部署和其他雲端環境中裝載之虛擬機器的變更，以協助您找出散發封裝管理員管理的軟體的操作和環境問題。 變更追蹤和清查所追蹤的項目包括：

- Windows 軟體
- Linux 軟體 (套件)
- Windows 和 Linux 檔案
- Windows 登錄機碼
- Microsoft 服務
- Linux 精靈

> [!NOTE]
> 若要追蹤 Azure Resource Manager 的屬性變更，請參閱 Azure Resource Graph 的[變更歷程記錄](../../governance/resource-graph/how-to/get-resource-changes.md)。

變更追蹤和清查利用 Azure 資訊安全中心檔案 [完整性監視 (FIM) ](../../security-center/security-center-file-integrity-monitoring.md) 檢查作業系統和應用程式檔，以及 Windows 登錄。 雖然 FIM 會監視這些實體，但變更追蹤和清查本身就會追蹤：

- 軟體變更
- Microsoft 服務
- Linux 精靈

啟用變更追蹤和清查中包含的所有功能可能會導致額外的費用。 繼續之前，請先參閱 [自動化定價](https://azure.microsoft.com/pricing/details/automation/) 和 [Azure 監視器定價](https://azure.microsoft.com/pricing/details/monitor/)。 

變更追蹤和清查會將資料轉送至 Azure 監視器記錄，而此收集的資料會儲存在 Log Analytics 工作區中。 只有在啟用 **適用于伺服器的 Azure Defender** 時，才能使用檔案完整性監視 (FIM) 功能。 若要深入瞭解，請參閱 Azure 資訊安全中心 [定價](../../security-center/security-center-pricing.md) 。 FIM 會將資料上傳至與建立的相同 Log Analytics 工作區，以儲存來自變更追蹤和清查的資料。 建議您監視連結的 Log Analytics 工作區，以追蹤確切的使用量。 如需分析 Azure 監視器記錄資料使用量的詳細資訊，請參閱 [管理使用量和成本](../../azure-monitor/platform/manage-cost-storage.md)。

連線到 Log Analytics 工作區的電腦會使用 [Log analytics 代理程式](../../azure-monitor/platform/log-analytics-agent.md) ，來收集受監視伺服器上已安裝的軟體、Microsoft 服務、Windows 登錄和檔案，以及 Linux 守護程式之變更的相關資料。 當資料可供使用時，代理程式會將它傳送給 Azure 監視器記錄以進行處理。 Azure 監視器記錄會將邏輯套用至接收的資料、記錄它，並使其可供分析。

> [!NOTE]
> 變更追蹤和清查需要將 Log Analytics 工作區連結至您的自動化帳戶。 如需支援區域的確切清單，請參閱 [Azure 工作區對應](../how-to/region-mappings.md)。 區域對應並不會影響從您的自動化帳戶，在不同區域中管理 VM 的能力。

## <a name="current-limitations"></a>目前的限制

變更追蹤和清查不支援或有下列限制：

- Windows 登錄追蹤的遞迴
- 網路檔案系統
- 不同安裝方法
- *儲存在 Windows 上的 **.exe**檔案
- [最大檔案大小] 資料行和值未用於目前實作中。
- 如果您嘗試在30分鐘的收集週期中收集超過2500的檔案，變更追蹤和清查效能可能會降低。
- 如果網路流量很高，變更記錄最多可能需要六個小時的時間才會顯示。
- 如果您在電腦或伺服器關閉時修改設定，可能會公佈屬於先前設定的變更。
- 收集 Windows Server 2016 Core RS3 電腦上的修正程式更新。
- 即使未發生任何變更，Linux 精靈程式也可能會顯示已變更的狀態。 發生此問題的原因 `SvcRunLevels` 是撰寫 Azure 監視器 [ConfigurationChange](/azure/azure-monitor/reference/tables/configurationchange) 資料表中的資料的方式。

## <a name="supported-operating-systems"></a>支援的作業系統

所有符合 Log Analytics 代理程式需求的作業系統都可支援變更追蹤和清查。 請參閱 [支援的作業系統](../../azure-monitor/platform/agents-overview.md#supported-operating-systems) ，以取得 Log Analytics 代理程式目前支援的 Windows 和 Linux 作業系統版本清單。

若要瞭解 TLS 1.2 的用戶端需求，請參閱 [Azure 自動化的 tls 1.2 強制](../automation-managing-data.md#tls-12-enforcement-for-azure-automation)。

## <a name="network-requirements"></a>網路需求

以下是變更追蹤和清查所需的特定位址。 與這些位址的通訊皆經由連接埠 443 進行。

|Azure 公用  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com    | *.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net | *.blob.core.usgovcloudapi.net|
|*.azure-automation.net | *.azure-automation.us|

當您建立網路群組安全性規則或設定 Azure 防火牆以允許流量流向自動化服務和 Log Analytics 工作區時，請使用 [服務](../../virtual-network/service-tags-overview.md#available-service-tags)標籤 **GuestAndHybridManagement** 和 **AzureMonitor**。 這可簡化網路安全性規則的持續管理。 若要安全且私下地從您的 Azure Vm 連接到自動化服務，請參閱 [使用 Azure Private Link](../how-to/private-link-security.md)。 若要取得目前的服務標籤和範圍資訊，以納入您的內部部署防火牆設定，請參閱 [可下載的 JSON](../../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)檔案。

## <a name="enable-change-tracking-and-inventory"></a>啟用變更追蹤和清查

以下是您可以啟用變更追蹤和清查，以及選取要管理之機器的方式：

* [從 Azure 虛擬機器](enable-from-vm.md)。
* [從流覽多部 Azure 虛擬機器](enable-from-portal.md)。
* [從 Azure 自動化帳戶](enable-from-automation-account.md)。
* 若為已啟用 Arc 的伺服器或非 Azure 機器，請使用 [VM 延伸](../../azure-arc/servers/manage-vm-extensions.md) 模組，從已啟用 Azure Arc 的伺服器安裝 Log Analytics 代理程式，然後 [讓工作區中的電腦](enable-from-automation-account.md#enable-machines-in-the-workspace) 變更追蹤和清查。
* [使用自動化 runbook](enable-from-runbook.md)。

## <a name="tracking-file-changes"></a>追蹤檔案變更

為了同時追蹤 Windows 和 Linux 上檔案的變更，變更追蹤和清查會使用檔案的 MD5 雜湊。 此功能會使用雜湊來偵測自上次清查之後是否發生變更。

## <a name="tracking-file-content-changes"></a>追蹤檔案內容變更

變更追蹤和清查可讓您查看 Windows 或 Linux 檔案的內容。 對於檔案的每項變更，變更追蹤和清查都會將檔案的內容儲存在 [Azure 儲存體帳戶](/storage/common/storage-account-create)中。 當您要追蹤檔案時，可以在變更之前或之後查看其內容。 檔案內容可以內嵌或並排查看。

![檢視檔案中的變更](./media/overview/view-file-changes.png)

## <a name="tracking-of-registry-keys"></a>登錄機碼的追蹤

變更追蹤和清查允許監視 Windows 登錄機碼的變更。 監視可讓您找出第三方程式碼和惡意程式碼可啟用的擴充點。 下表列出預先設定 (但未啟用) 的登錄機碼。 若要追蹤這些機碼，則必須啟用每個機碼。

> [!div class="mx-tdBreakAll"]
> |登錄金鑰 | 目的 |
> | --- | --- |
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup` | 監視在啟動時執行的指令碼。
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown` | 監視在關機時執行的指令碼。
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run` | 監視使用者登入 Windows 帳戶之前載入的金鑰。 此金鑰供 64 位元電腦上執行的 32 位元應用程式使用。
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components` | 監視應用程式設定的變更。
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | 監視直接與 Windows 檔案總管連結的內容功能表處理常式，且通常會與 **explorer.exe**執行同進程。
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers` | 監視複製攔截處理常式，該處理常式會直接連結到 Windows 檔案總管，且通常會與 **explorer.exe**執行同進程。
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | 監視圖示覆疊處理常式註冊。
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | 針對在 64 位元電腦上執行的 32 位元應用程式，監視圖示覆疊處理常式註冊。
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Internet Explorer 之新瀏覽器協助程式物件外掛程式的監視器。 用以存取當前頁面的文件物件模型 (DOM) 並控制瀏覽。
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Internet Explorer 之新瀏覽器協助程式物件外掛程式的監視器。 用以存取目前頁面的文件物件模型 (DOM) 並控制 64 位元電腦上執行之 32 位元應用程式的瀏覽。
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Internet Explorer\Extensions` | 監視新的 Internet Explorer 擴充功能，例如自訂工具功能表和自訂工具列按鈕。
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions` | 針對在 64 位元電腦上執行的 32 位元應用程式，監視新的 Internet Explorer 擴充功能，例如自訂工具功能表和自訂工具列按鈕。
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32` | 監視與 wavemapper、wave1 和 wave2、msacm.imaadpcm、.msadpcm、.msgsm610 和 vidc 相關聯的 32 位元驅動程式。 類似 **system.ini** 檔案中的 [drivers] 區段。
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32` | 針對在 64 位元電腦上執行的 32 位元應用程式，監視與 wavemapper、wave1 和 wave2、msacm.imaadpcm、.msadpcm、.msgsm610 和 vidc 相關聯的 32 位元驅動程式。 類似 **system.ini** 檔案中的 [drivers] 區段。
> |`HKEY\LOCAL\MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls` | 監視已知或常用系統 DLL 的清單。 監視可透過卸載系統 Dll 的特洛伊木馬版，防止人們利用弱式應用程式目錄許可權。
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify` | 監視可從 **winlogon.exe** (Windows 的互動式登入支援模型) 接收事件通知的套件清單。

## <a name="recursion-support"></a>遞迴支援

變更追蹤和清查支援遞迴，可讓您指定萬用字元以簡化跨目錄的追蹤。 遞迴也提供了環境變數，可讓您在具有多個或動態磁碟機名稱的環境中追蹤檔案。 下列清單包含您在設定遞迴時應知道的一般資訊：

- 追蹤多個檔案時需要萬用字元。

- 您只能在檔案路徑的最後一個區段中使用萬用字元，例如， **c:\folder \\ file*** 或 **/etc/*.**

- 如果環境變數具有不正確路徑，驗證雖然會成功，但路徑會在執行期間失敗。

- 在設定路徑時，您應該避免一般路徑名稱，因為這種類型的設定可能會導致無法遍歷太多資料夾。

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

對於使用變更追蹤和清查的機器，其 Log Analytics 資料使用量平均大約是每月 40 MB (視您的環境而定)。 使用 Log Analytics 工作區的使用量和估計成本功能，您可以在使用量圖表中變更追蹤和清查來查看內嵌的資料。 使用此資料檢視來評估您的資料使用量，並判斷它對您的帳單有何影響。 請參閱[了解您的使用量並估算成本](/azure-monitor/platform/manage-cost-storage#understand-your-usage-and-estimate-costs)。

### <a name="microsoft-service-data"></a>Microsoft 服務資料

Microsoft 服務的預設收集頻率為 30 分鐘。 在 [編輯設定] 底下的 [Microsoft 服務] 索引標籤上，您可以使用滑杆來設定頻率。

![Microsoft 服務滑桿](./media/overview/windowservices.png)

為了將效能最佳化，Log Analytics 代理程式只會追蹤變更。 如果服務回到其原始狀態，則設定高臨界值可能會遺漏變更。 若將頻率設為較小的值，則可以攔截可能遺漏的變更。

> [!NOTE]
> 雖然代理程式可以追蹤 10 秒間隔內的變更，但資料仍需要幾分鐘才會顯示在 Azure 入口網站中。 在入口網站中顯示期間發生的變更仍會加以追蹤並記錄。

## <a name="support-for-alerts-on-configuration-state"></a>設定狀態的警示支援

變更追蹤和清查的重要功能，就是能夠針對混合式環境組態狀態的變更提出警示。 有許多有用的動作可用來觸發，以回應警示。 例如，對 Azure 函式、自動化 runbook、webhook 和等的動作。 針對變更追蹤和清查資料，對電腦的 **C:\windows\system32\drivers\etc\hosts** 檔案所做的變更警示是一個良好的應用程式。 此外還有許多警示案例，包括下表中定義的查詢案例。

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

- 若要從自動化帳戶啟用，請參閱 [從自動化帳戶啟用變更追蹤和清查](enable-from-automation-account.md)。

- 若要從 Azure 入口網站啟用，請參閱 [從 Azure 入口網站啟用變更追蹤和清查](enable-from-portal.md)。

- 若要從 runbook 啟用，請參閱 [從 Runbook 啟用變更追蹤和清查](enable-from-runbook.md)。

- 若要從 Azure VM 啟用，請參閱 [啟用 AZURE vm 的變更追蹤和清查](enable-from-vm.md)。
