---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/11/2018
ms.author: rogarana
ms.openlocfilehash: aeb15fbb8da44a203789e06a359cb664998602ab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
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
3. **使用 Microsoft Update 修補檔或 .msp 可執行檔，修補現有的 Azure 檔案同步代理程式。您可以從 [Microsoft Update 類別目錄](https://www.catalog.update.microsoft.com/Search.aspx?q=Azure%20File%20Sync)下載最新的 Azure 檔案同步更新套件。**  
    執行 .msp 可執行檔將會使用與 Microsoft Update 在先前升級路徑中自動使用的相同方法，來升級 Azure 檔案同步安裝。 套用 Microsoft Update 修補程式將會執行 Azure 檔案同步安裝的就地升級。
4. **請從 [Microsoft 下載中心](https://go.microsoft.com/fwlink/?linkid=858257)下載最新的 Azure 檔案同步代理程式安裝程式。**  
    若要升級現有的 Azure 檔案同步代理程式安裝，請將舊版解除安裝，然後從所下載的安裝程式安裝最新版本。 Azure 檔案同步安裝程式會維護伺服器註冊、同步群組和任何其他設定。

#### <a name="automatic-agent-lifecycle-management"></a>自動代理程式生命週期管理
在代理程式第6版中，檔案同步小組引進了代理程式自動升級功能。 您可以選取兩種模式的其中一種，並指定要在伺服器上嘗試升級的維護視窗。 這項功能的設計目的是為了協助您進行代理程式生命週期管理，方法是提供 guardrail，讓您的代理程式無法過期或不需要保持最新的設定。
1. **預設設定**會嘗試防止代理程式到期。 在代理程式張貼到期日的21天內，代理程式會嘗試自我升級。 它會在到期前的21天內，以及在選取的維護時段開始嘗試升級一次。 **此選項不會消除定期 Microsoft Update 修補程式的需求。**
1. （選擇性）您可以選擇在新的代理程式版本可用 (目前不適用於) 的叢集伺服器時，代理程式會自動升級本身。 這項更新會在選取的維護期間發生，並可讓您的伺服器在新功能正式推出後立即受益于這些新功能和增強功能。 這是建議的無煩惱設定，可提供主要的代理程式版本，以及伺服器的定期更新修補程式。 每個發行的代理程式都是 GA 品質。 如果您選取此選項，Microsoft 會將最新的代理程式版本傳送給您。 排除叢集伺服器。 試驗完成後，代理程式也將可在 [Microsoft 下載中心](https://go.microsoft.com/fwlink/?linkid=858257) aka.ms/AFS/agent 取得。

 ##### <a name="changing-the-auto-upgrade-setting"></a>變更自動升級設定

下列指示說明如何在您完成安裝程式之後變更設定（如果您需要進行變更）。

開啟 PowerShell 主控台並流覽至您安裝同步代理程式的目錄，然後匯入伺服器 Cmdlet。 根據預設，這會看起來像這樣：
```powershell
cd 'C:\Program Files\Azure\StorageSyncAgent'
Import-Module -Name .\StorageSync.Management.ServerCmdlets.dll
```

您可以執行 `Get-StorageSyncAgentAutoUpdatePolicy` 以檢查目前的原則設定，並判斷是否要變更它。

若要將目前的原則設定變更為延遲的更新追蹤，您可以使用：
```powershell
Set-StorageSyncAgentAutoUpdatePolicy -PolicyMode UpdateBeforeExpiration
```

若要將目前的原則設定變更為立即更新追蹤，您可以使用：
```powershell
Set-StorageSyncAgentAutoUpdatePolicy -PolicyMode InstallLatest
```

#### <a name="agent-lifecycle-and-change-management-guarantees"></a>代理程式生命週期和變更管理保證
Azure 檔案同步是一項雲端服務，持續推出新功能和增強功能。 這表示特定的 Azure 檔案同步代理程式版本只會支援一段有限的時間。 為了加速您的部署，下列規則保證您有足夠的時間和通知，可在您的變更管理程式中容納代理程式更新/升級：

- 主要代理程式版本從初始發行日期算起會獲得至少六個月的支援。
- 我們保證主要代理程式版本之間的支援至少有三個月的重疊。 
- 系統會在到期至少三個月之前，使用即將到期代理程式向已註冊的伺服器發出警告。 您可以在儲存體同步服務的已註冊伺服器區段之下，檢查已註冊的伺服器是否使用舊版的代理程式。
- 次要代理程式版本的存留期會繫結至相關聯的主要版本。 例如，當代理程式 3.0 版發行時，代理程式 2.\* 版全都會設定為一起過期。

> [!Note]
> 安裝具有到期警告的代理程式版本時會顯示警告，但仍會成功。 不支援嘗試安裝或與已過期的代理程式版本連線，而且會加以封鎖。
