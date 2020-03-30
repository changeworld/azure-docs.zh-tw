---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/11/2018
ms.author: rogarana
ms.openlocfilehash: aeb15fbb8da44a203789e06a359cb664998602ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77123243"
---
Azure 檔案同步代理程式會定期更新，以新增功能和解決問題。 建議您設定 Microsoft Update，以取得 Azure 檔案同步代理程式的最新更新。

#### <a name="major-vs-minor-agent-versions"></a>主要與次要代理程式版本
* 主要代理程式版本通常會包含新功能，且會使用遞增的數字作為版本號碼的第一個部分。 例如：\*2.\*.\*\*
* 次要代理程式版本也稱為「修補」，且會比主要版本更常發行。 它們通常包含錯誤修正和小幅改善，但是沒有任何新功能。 例如：\*\*.3.\*\*

#### <a name="upgrade-paths"></a>升級路徑
有四個經核准及測試的方式可用來安裝 Azure 檔案同步代理程式更新。 
1. **(慣用) 設定 Microsoft Update，以自動下載並安裝代理程式更新。**  
    建議一律採用每個 Azure 檔案同步更新，以確保您可存取伺服器代理程式的最新修正程式。 Microsoft Update 會為您自動下載和安裝更新，讓這個程序順暢進行。
2. **使用 AfsUpdater.exe 下載並安裝代理程式更新。**  
    AfsUpdater.exe 位於代理程式安裝目錄中。 按兩下可執行檔，即可下載並安裝代理程式的更新。 
3. **使用 Microsoft 更新修補程式檔或 .msp 可執行檔修補現有 Azure 檔同步代理。最新的 Azure 檔同步更新包可以從[Microsoft 更新目錄](https://www.catalog.update.microsoft.com/Search.aspx?q=Azure%20File%20Sync)下載。**  
    執行 .msp 可執行檔將會使用與 Microsoft Update 在先前升級路徑中自動使用的相同方法，來升級 Azure 檔案同步安裝。 套用 Microsoft Update 修補程式將會執行 Azure 檔案同步安裝的就地升級。
4. **從[微軟下載中心](https://go.microsoft.com/fwlink/?linkid=858257)下載最新的 Azure 檔同步代理安裝程式。**  
    若要升級現有的 Azure 檔案同步代理程式安裝，請將舊版解除安裝，然後從所下載的安裝程式安裝最新版本。 Azure 檔案同步安裝程式會維護伺服器註冊、同步群組和任何其他設定。

#### <a name="automatic-agent-lifecycle-management"></a>自動代理生命週期管理
使用代理版本 6，檔同步團隊引入了代理自動升級功能。 您可以選擇兩種模式之一，並指定在伺服器上嘗試升級的維護視窗。 此功能旨在通過提供防止代理過期的護欄或允許無憂、保持當前設置來説明您進行代理生命週期管理。
1. **預設設置**將嘗試防止代理過期。 在代理髮布到期日期後的 21 天內，代理將嘗試自我升級。 它將在過期前 21 天內在選定的維護視窗中開始嘗試每週升級一次。 **此選項不會消除使用常規 Microsoft 更新修補程式的需要。**
1. 或者，您可以選擇代理在新代理版本可用（當前不適用於叢集服務器）後立即自動升級自身。 此更新將在選定的維護時段進行，並允許伺服器在新功能和改進普遍可用後立即從中受益。 這是推薦的無憂設置，它將為主要代理版本以及定期補救伺服器修補程式。 釋放的每個代理都處於 GA 品質。 如果選擇此選項，Microsoft 將您提供最新的代理版本。 叢集服務器被排除。 飛行完成後，代理也將在[Microsoft 下載中心](https://go.microsoft.com/fwlink/?linkid=858257)aka.ms/AFS/agent上可用。

 ##### <a name="changing-the-auto-upgrade-setting"></a>更改自動升級設置

以下說明描述在完成安裝程式後如何更改設置（如果需要進行更改）。

打開 PowerShell 主控台並導航到安裝同步代理的目錄，然後導入伺服器 Cmdlet。 預設情況下，如下所示：
```powershell
cd 'C:\Program Files\Azure\StorageSyncAgent'
Import-Module -Name .\StorageSync.Management.ServerCmdlets.dll
```

可以運行`Get-StorageSyncAgentAutoUpdatePolicy`以檢查當前策略設置並確定是否要更改它。

要將當前策略設置更改為延遲更新軌道，可以使用：
```powershell
Set-StorageSyncAgentAutoUpdatePolicy -PolicyMode UpdateBeforeExpiration
```

要將當前策略設置更改為即時更新軌道，可以使用：
```powershell
Set-StorageSyncAgentAutoUpdatePolicy -PolicyMode InstallLatest
```

#### <a name="agent-lifecycle-and-change-management-guarantees"></a>代理程式生命週期和變更管理保證
Azure 檔同步是一種雲服務，它不斷引入新功能和改進。 這表示特定的 Azure 檔案同步代理程式版本只會支援一段有限的時間。 為了便於部署，以下規則保證您有足夠的時間和通知來適應更改管理過程中的代理更新/升級：

- 主要代理程式版本從初始發行日期算起會獲得至少六個月的支援。
- 我們保證主要代理程式版本之間的支援至少有三個月的重疊。 
- 系統會在到期至少三個月之前，使用即將到期代理程式向已註冊的伺服器發出警告。 您可以在儲存體同步服務的已註冊伺服器區段之下，檢查已註冊的伺服器是否使用舊版的代理程式。
- 次要代理程式版本的存留期會繫結至相關聯的主要版本。 例如，當代理程式 3.0 版發行時，代理程式 2.\* 版全都會設定為一起過期。

> [!Note]
> 安裝具有到期警告的代理程式版本時會顯示警告，但仍會成功。 不支援嘗試安裝或與已過期的代理程式版本連線，而且會加以封鎖。
