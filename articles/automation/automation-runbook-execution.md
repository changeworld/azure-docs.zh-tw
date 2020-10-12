---
title: Azure 自動化中的 Runbook 執行
description: 本文概述 Azure 自動化中的 runbook 處理。
services: automation
ms.subservice: process-automation
ms.date: 10/06/2020
ms.topic: conceptual
ms.openlocfilehash: 883cf48fd38d79544d08a68f2c18fc2d2efb4706
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91776284"
---
# <a name="runbook-execution-in-azure-automation"></a>Azure 自動化中的 Runbook 執行

Azure 自動化中的程序自動化，可讓您建立和管理 PowerShell、PowerShell 工作流程和圖形化 Runbook。 如需詳細資訊，請參閱 [Azure 自動化 Runbook](automation-runbook-types.md)。 

自動化會根據在 Runbook 中定義的邏輯來執行 Runbook。 如果 Runbook 中斷，則會從頭重新啟動。 前提是 Runbook 必須撰寫成發生暫時性問題時可重新啟動。

在 Azure 自動化中啟動 Runbook 時會建立作業，此為 Runbook 的單一執行個體。 每個作業會連線到您的 Azure 訂用帳戶，以存取 Azure 資源。 資料中心內的資源必須開放給公用雲端存取，作業才能存取這些資源。

在 Runbook 執行期間，Azure 自動化會指派背景工作角色來執行每個作業。 雖然背景工作是由多個 Azure 帳戶共用，來自不同自動化帳戶的工作會彼此隔離。 您無法控制由哪個背景工作角色來處理作業要求。

當您在 Azure 入口網站中檢視 Runbook 清單時，可看到已針對每個 Runbook 啟動的每個作業的狀態。 Azure 自動化最多儲存 30 天的作業記錄。

下圖顯示 [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks)、[PowerShell 工作流程 Runbook](automation-runbook-types.md#powershell-workflow-runbooks) 和[圖形化 Runbook](automation-runbook-types.md#graphical-runbooks) 的 Runbook 作業生命週期。

![工作狀態 -PowerShell 工作流程](./media/automation-runbook-execution/job-statuses.png)

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="runbook-execution-environment"></a>Runbook 執行環境

Azure 自動化中的 Runbook 可以在 Azure 沙箱或[混合式 Runbook 背景工作角色](automation-hybrid-runbook-worker.md)上執行。 

如果 Runbook 設計為對 Azure 中的資源進行驗證和執行，則會在 Azure 沙箱中執行，此為多個作業可共用的環境。 使用相同沙箱的作業均受限於該沙箱的資源限制。 Azure 沙箱環境不支援互動式作業。 禁止存取所有跨流程 COM 伺服器。 還需要使用本機 MOF 檔案，Runbook 才能發出 Win32 呼叫。

您也可以使用[混合式 Runbook 背景工作角色](automation-hybrid-runbook-worker.md)，直接在裝載角色的電腦上執行 Runbook，以及針對環境中的本機資源執行 Runbook。 Azure 自動化會儲存並管理 Runbook，然後將 Runbook 傳遞至一或多個指派的電腦。

>[!NOTE]
>若要在 Linux 混合式 Runbook 背景工作角色上執行，您的指令碼必須經過簽署，也要相應地設定背景工作角色。 或者，[必須關閉簽章驗證](automation-linux-hrw-install.md#turn-off-signature-validation)。

下表列出一些 Runbook 執行工作，並列出針對各 Runbook 建議的執行環境。

|Task|建議|注意|
|---|---|---|
|與 Azure 資源整合|Azure 沙箱|裝載於 Azure 中，驗證比較簡單。 如果您在 Azure VM 上使用混合式 Runbook 背景工作角色，您可以[搭配受控識別使用 Runbook 驗證](automation-hrw-run-runbooks.md#runbook-auth-managed-identities)。|
|獲得最佳效能來管理 Azure 資源|Azure 沙箱|指令碼在相同環境中執行，延遲較少。|
|將營運成本最小化|Azure 沙箱|沒有任何計算額外負荷，也不需要 VM。|
|執行長時間執行的指令碼|Hybrid Runbook Worker|Azure 沙箱有[資源限制](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)。|
|與本機服務互動|Hybrid Runbook Worker|直接存取主機電腦，或其他雲端環境或內部部署環境中的資源。 |
|需要協力廠商軟體和可執行檔|Hybrid Runbook Worker|您負責管理作業系統且可以安裝軟體。|
|使用 Runbook 監視檔案或資料夾|Hybrid Runbook Worker|在混合式 Runbook 背景工作角色上使用[監看員工作](automation-watchers-tutorial.md)。|
|執行耗用大量資源的指令碼|Hybrid Runbook Worker| Azure 沙箱有[資源限制](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)。|
|使用具有特定需求的模組| Hybrid Runbook Worker|部份範例如下：</br> WinSCP - 相依於 winscp.exe </br> IIS 系統管理 - 相依於啟用或管理 IIS|
|使用安裝程式來安裝模組|Hybrid Runbook Worker|沙箱的模組必須支援複製。|
|使用需要 .NET Framework 4.7.2 以外版本的 Runbook 或模組|Hybrid Runbook Worker|Azure 沙箱支援 .NET Framework 4.7.2，不支援升級至不同版本。|
|執行需要提高權限的指令碼|Hybrid Runbook Worker|沙箱不允許提高權限。 使用混合式 Runbook 背景工作角色時，您可以關閉 UAC，並使用 [Invoke-Command](/powershell/module/microsoft.powershell.core/invoke-command) 來執行需要提高權限的命令。|
|執行需要存取 Windows Management Instrumentation (WMI) 的指令碼|Hybrid Runbook Worker|在雲端的沙箱中執行的作業無法存取 WMI 提供者。 |

## <a name="temporary-storage-in-a-sandbox"></a>沙箱中的暫存儲存體

如果您需要建立暫存檔案做為 runbook 邏輯的一部分，您可以使用 Temp 資料夾 (也就是在 azure `$env:TEMP` 沙箱中針對在 azure 中執行的 runbook) 。 唯一的限制是，您不能使用超過 1 GB 的磁碟空間，也就是每個沙箱的配額。 使用 PowerShell 工作流程時，此案例可能會造成問題，因為 PowerShell 工作流程會使用檢查點，而且可以在不同的沙箱中重試腳本。

使用混合式沙箱，您可以 `C:\temp` 根據混合式 Runbook 背景工作角色上的儲存體可用性來使用。 不過，根據 Azure VM 的建議，您不應該使用 Windows 或 Linux 上的 [暫存磁片](../virtual-machines/managed-disks-overview.md#temporary-disk) 來取得需要保存的資料。

## <a name="resources"></a>資源

您的 Runbook 必須包含邏輯來處理[資源](/rest/api/resources/resources)，例如 VM、網路和網路上的資源。 資源繫結至 Azure 訂用帳戶，而 Runbook 需要適當的認證才能存取任何資源。 關於在 Runbook 中處理資源的範例，請參閱[處理資源](manage-runbooks.md#handle-resources)。

## <a name="security"></a>安全性

Azure 自動化使用 [Azure 資訊安全中心 (ASC)](../security-center/security-center-intro.md) 來確保資源的安全性，並偵測 Linux 系統是否遭到入侵。 無論資源是否在 Azure 中，所有工作負載都受到保護。 請參閱 [Azure 自動化中的驗證簡介](automation-security-overview.md)。

ASC 會限制哪些使用者可在 VM 上執行任何指令碼 (已簽署或未簽署)。 如果您是對 VM 具有 root 存取權的使用者，則必須以數位簽章來明確設定機器或關閉機器。 否則，只有在建立自動化帳戶並啟用適當功能之後，您才能執行指令碼來套用作業系統更新。

## <a name="subscriptions"></a>訂用帳戶

Azure [訂用帳戶](/office365/enterprise/subscriptions-licenses-accounts-and-tenants-for-microsoft-cloud-offerings)是您為了使用一或多個雲端式服務，而與 Microsoft 簽訂的合約，需要付費。 對於 Azure 自動化，每個訂用帳戶都連結到 Azure 自動化帳戶，而您可以在此帳戶中[建立多個訂用帳戶](manage-runbooks.md#work-with-multiple-subscriptions)。

## <a name="credentials"></a>認證

不論是在 Azure 或協力廠商系統上，Runbook 都需要適當的[認證](shared-resources/credentials.md)，才能存取任何資源。 這些認證儲存在 Azure 自動化、Key Vault 等。  

## <a name="azure-monitor"></a>Azure 監視器

Azure 自動化利用 [Azure 監視器](../azure-monitor/overview.md) 來監視其電腦操作。 作業需要 Log Analytics 工作區和 [Log analytics 代理程式](../azure-monitor/platform/log-analytics-agent.md)。

### <a name="log-analytics-agent-for-windows"></a>適用於 Windows 的 Log Analytics 代理程式

[適用於 Windows 的 Log Analytics 代理程式](../azure-monitor/platform/agent-windows.md)可搭配 Azure 監視器，一起管理 Windows VM 和實體電腦。 這些機器可以在 Azure 或非 Azure 環境中執行，例如本地資料中心。

>[!NOTE]
>適用於 Windows 的 Log Analytics 代理程式先前稱為 Microsoft Monitoring Agent (MMA)。

### <a name="log-analytics-agent-for-linux"></a>Log Analytics Linux 代理程式

[適用於 Linux 的 Log Analytics 代理程式](../azure-monitor/platform/agent-linux.md)運作方式類似適用於 Windows 的代理程式，但會將 Linux 電腦連線到 Azure 監視器。 代理程式是以 **>nxautomation** 使用者帳戶安裝，允許執行需要根許可權的命令，例如在混合式 Runbook 背景工作角色上執行。 **nxautomation** 帳戶是不需要密碼的系統帳戶。

在[安裝 Linux 混合式背景工作角色](automation-linux-hrw-install.md)期間，必須有具備對應 sudo 權限的 **nxautomation** 帳戶。 如果您嘗試安裝背景工作角色，但此帳戶不存在或沒有適當的權限，則安裝會失敗。

您不應該變更 `sudoers.d` 資料夾或其擁有權的許可權。 **>nxautomation**帳戶需要 Sudo 許可權，而且不應移除許可權。 將此限制為某些資料夾或命令可能會導致中斷性變更。

Log Analytics 代理程式和 **nxautomation** 帳戶可用的記錄如下：

* /var/opt/microsoft/omsagent/log/omsagent.log - Log Analytics 代理程式記錄
* /var/opt/microsoft/omsagent/run/automationworker/worker.log - 自動化背景工作角色記錄

>[!NOTE]
>隨著更新管理而啟用的 **nxautomation** 使用者，只會執行已簽署的 Runbook。

## <a name="runbook-permissions"></a>Runbook 權限

Runbook 需要有權限來透過認證向 Azure 進行驗證。 請參閱[管理 Azure 自動化執行身分帳戶](manage-runas-account.md)。

## <a name="modules"></a>模組

Azure 自動化支援許多預設模組，包括一些 AzureRM 模組 (AzureRM.Automation)，以及一個含有幾個內部 Cmdlet 的模組。 也支援可安裝的模組，包括 Az 模組 (Az.Automation)，目前比 AzureRM 模組更優先使用。 關於 Runbook 和 DSC 設定可用的模組，如需詳細資訊，請參閱[在 Azure 自動化中管理模組](shared-resources/modules.md)。

## <a name="certificates"></a>憑證

Azure 自動化使用[憑證](shared-resources/certificates.md)向 Azure 進行驗證，或將憑證新增至 Azure 或協力廠商資源。 憑證都妥善儲存，供 Runbook 和 DSC 設定來存取。

您的 Runbook 可以使用自我簽署憑證，這種憑證不是由憑證授權單位 (CA) 簽署。 請參閱[建立新的憑證](shared-resources/certificates.md#create-a-new-certificate)。

## <a name="jobs"></a>工作

Azure 自動化支援一個從相同自動化帳戶來執行作業的環境。 單一 Runbook 一次可以執行許多工作。 同時執行的作業越多，越常會分派到相同的沙箱。 

相同沙箱流程中執行的作業可能影響彼此。 例如執行 [Disconnect-AzAccount](/powershell/module/az.accounts/disconnect-azaccount) Cmdlet。 執行此 Cmdlet 會中斷連接共用沙箱流程中的每個 Runbook 作業。 如需有關解決此情況的範例，請參閱[防止同時作業](manage-runbooks.md#prevent-concurrent-jobs)。

>[!NOTE]
>如果 PowerShell 作業是從 Azure 沙箱中執行的 Runbook 啟動，可能無法以完整的 [PowerShell 語言模式](/powershell/module/microsoft.powershell.core/about/about_language_modes)執行。

### <a name="job-statuses"></a>工作狀態

下表描述作業可能的狀態。 您可以在 Azure 入口網站中，檢視所有 Runbook 作業的狀態摘要，或深入了解特定 Runbook 作業的詳細資料。 您也可以設定與 Log Analytics 工作區的整合，以轉送 Runbook 作業狀態和作業串流。 如需與 Azure 監視器記錄整合的詳細資訊，請參閱[將作業狀態和作業串流從自動化轉送到 Azure 監視器記錄](automation-manage-send-joblogs-log-analytics.md)。 如需在 Runbook 中處理狀態的範例，另請參閱[取得作業狀態](manage-runbooks.md#obtain-job-statuses)。

| 狀態 | 描述 |
|:--- |:--- |
| Completed |工作已成功完成。 |
| 失敗 |無法編譯圖形化 Runbook 或 PowerShell 工作流程 Runbook。 PowerShell Runbook 無法啟動，或作業發生例外狀況。 請參閱 [Azure 自動化 Runbook 類型](automation-runbook-types.md)。|
| 處理失敗，正在等候資源 |工作失敗，因為其達到 [公平共用](#fair-share) 的三次上限，且每次從相同的檢查點或啟動 Runbook 開始。 |
| 已排入佇列 |作業正在等候自動化背景工作角色上的資源變成可用，如此才能啟動作業。 |
| 繼續中 |工作暫停後，系統正在繼續工作。 |
| 執行中 |工作正在執行。 |
| 執行中，正在等候資源 |作業已卸載，因為已達到公平共用限制。 工作會很快地從其上一個檢查點繼續。 |
| 啟動中 |此作業已指派給背景工作角色，並且系統正在進行啟動。 |
| 已停止 |工作完成之前已由使用者停止。 |
| 停止中 |系統正在停止作業。 |
| 暫止 |只適用於[圖形化 Runbook 和 PowerShell 工作流程 Runbook](automation-runbook-types.md)。 工作已由使用者、系統或 Runbook 中的命令暫停。 如果 Runbook 沒有檢查點，則會從頭開始。 如果它有檢查點，則可重新啟動並從其最後一個檢查點繼續。 只有在發生例外狀況時，系統才會暫止 Runbook。 根據預設，`ErrorActionPreference` 會設定為 Continue，表示作業在發生錯誤時繼續執行。 如果此喜好設定變數設定為 Stop，則作業在發生錯誤時會暫止。  |
| Suspending |只適用於[圖形化 Runbook 和 PowerShell 工作流程 Runbook](automation-runbook-types.md)。 因使用者要求，系統正在嘗試暫停工作。 Runbook 必須達到其下一個檢查點才能暫停。 如果已通過最後一個檢查點，則在暫止之前就會完成。 |

## <a name="activity-logging"></a>活動記錄

在 Azure 自動化中執行 Runbook 時會將詳細資料寫入自動化帳戶的活動記錄中。 如需使用此記錄的詳細資訊，請參閱[從活動記錄取出詳細資料](manage-runbooks.md#retrieve-details-from-activity-log)。

## <a name="exceptions"></a>例外狀況

本節說明在 Runbook 中處理例外狀況或間歇問題的一些方法。 例如 WebSocket 例外狀況。 正確處理例外狀況可防止因為暫時性網路失敗而造成 Runbook 失敗。

### <a name="erroractionpreference"></a>ErrorActionPreference

[ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference) 變數決定 PowerShell 如何回應非終止錯誤。 終止錯誤一律會終止，不受 `ErrorActionPreference` 所影響。

當 Runbook 使用 `ErrorActionPreference` 時，平常的非終止錯誤 (例如來自 [Get-ChildItem](/powershell/module/microsoft.powershell.management/get-childitem) Cmdlet 的 `PathNotFound`) 會阻止 Runbook 完成。 下列範例示範 `ErrorActionPreference` 的用法。 當指令碼停止時，最後的 [Write-Output](/powershell/module/microsoft.powershell.utility/write-output) 命令永遠不會執行。

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-ChildItem -path nofile.txt
Write-Output "This message will not show"
```

### <a name="try-catch-finally"></a>Try Catch Finally

[Try Catch Finally](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally) 用於 PowerShell 指令碼中來處理終止錯誤。 指令碼可以使用此機制來攔截特定例外狀況或一般例外狀況。 `catch` 陳述式應該用來追蹤或嘗試處理錯誤。 下列範例嘗試下載不存在的檔案。 此範例會攔截 `System.Net.WebException` 例外狀況，並傳回其他任何例外狀況的最後一個值。

```powershell-interactive
try
{
   $wc = new-object System.Net.WebClient
   $wc.DownloadFile("http://www.contoso.com/MyDoc.doc")
}
catch [System.Net.WebException]
{
    "Unable to download MyDoc.doc from http://www.contoso.com."
}
catch
{
    "An error occurred that could not be resolved."
}
```

### <a name="throw"></a>Throw

[Throw](/powershell/module/microsoft.powershell.core/about/about_throw) 可以用來產生終止錯誤。 在 Runbook 中定義您自己的邏輯時，這項機制很有用。 如果指令碼符合準則而應該停止，則可以使用 `throw` 陳述式來停止。 下列範例使用此陳述式來顯示必要的函式參數。

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

## <a name="errors"></a>Errors

您的 Runbook 必須處理錯誤。 Azure 自動化支援兩種 PowerShell 錯誤：終止和非終止。 

終止錯誤發生時會停止 Runbook 執行。 Runbook 會以作業狀態 Failed 停止。

非終止錯誤即使發生，仍允許指令碼繼續。 例如，當 Runbook 在 `Get-ChildItem` Cmdlet 中使用不存在的路徑時，就會發生非終止錯誤。 PowerShell 發現路徑不存在，則會擲回錯誤，並繼續下一步資料夾。 此情況下的錯誤不會將 Runbook 作業狀態設為 Failed，作業甚至可能完成。 若要強制 Runbook 在非終止錯誤時停止，您可以在 Cmdlet 上使用 `ErrorAction Stop`。

## <a name="calling-processes"></a>呼叫流程

在 Azure 沙箱中執行的 Runbook 不支援呼叫流程，例如可執行檔 ( **.exe** 檔案) 或子流程。 這是因為 Azure 沙箱是容器中執行的共用流程，可能無法存取所有基礎 API。 如果需要協力廠商軟體或呼叫子流程，您應該在 [混合式 Runbook 背景工作角色](automation-hybrid-runbook-worker.md)上執行 Runbook。

## <a name="device-and-application-characteristics"></a>裝置和應用程式特性

Azure 沙箱中的 Runbook 作業無法存取任何裝置或應用程式特性。 Windows 上最常用來查詢效能計量的 API 是 WMI，記憶體和 CPU 使用量就是其中一些常用的計量。 然而，無論使用什麼 API，雲端執行的作業都無法存取 Microsoft 的 Web 架構企業管理 (WBEM) 實作。 此平台以通用訊息模型 (CIM) 為基礎，提供業界標準來定義裝置和應用程式特性。

## <a name="webhooks"></a>Webhook

外部服務 (例如 Azure DevOps Services 和 GitHub) 可以在 Azure 自動化中啟動 Runbook。 為了以這種方式啟動，服務需要透過單一 HTTP 要求來使用 [Webhook](automation-webhooks.md)。 使用 Webhook 時，Runbook 不需要實作完整的 Azure 自動化功能就能啟動。

## <a name="shared-resources"></a><a name="fair-share"></a>共用資源

為了讓雲端的所有 Runbook 共用資源，Azure 採用所謂的「公平共用」概念。 基於公平共用，Azure 會暫時卸載或停止已執行超過三小時的任何作業。 [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks) 和 [Python Runbook](automation-runbook-types.md#python-runbooks) 的作業會停止且不會重新啟動，而作業狀態會變成 Stopped。

對於長時間執行的 Azure 自動化工作，建議使用混合式 Runbook 背景工作角色。 混合式 Runbook 背景工作角色並未受限於公平共用，而且未限制 Runbook 執行時間長度。 其他作業[限制](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)會套用至 Azure 沙箱和混合式 Runbook 背景工作角色。 雖然混合式 Runbook 背景工作角色未受限於三小時的公平共用限制，但是您應開發 runbook，以在支援從未預期的本機基礎結構問題重新開機的背景工作角色上執行。

另一個選項是使用子 Runbook 將 Runbook 最佳化。 例如，您的 Runbook 可能在數個資源上重複執行同一個函式，例如，在數個資料庫上執行資料庫作業。 您可以將此函式移至[子 Runbook](automation-child-runbooks.md)，然後讓您的 Runbook 使用 [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook) 來呼叫此函式。 子 Runbook 會個別的流程中平行執行。

使用子 Runbook 可縮短父 Runbook 完成所需的總時間。 如果您的 Runbook 在子系完成後，仍然還有其他作業，則可以使用 [Get-AzAutomationJob](/powershell/module/az.automation/get-azautomationjob) Cmdlet 來檢查子 Runbook 的作業狀態。

## <a name="next-steps"></a>後續步驟

* 若要開始使用 PowerShell Runbook，請參閱[教學課程：建立 PowerShell Runbook](learn/automation-tutorial-runbook-textual-powershell.md)。
* 若要使用 Runbook，請參閱[在 Azure 自動化中管理 Runbook](manage-runbooks.md)。
* 如需 PowerShell 的詳細資料，請參閱 [PowerShell 文件](/powershell/scripting/overview)。
* 如需 PowerShell Cmdlet 參考，請參閱 [Az.Automation](/powershell/module/az.automation#automation)。
