---
title: Azure 自動化中的 Runbook 執行
description: 描述如何處理 Azure 自動化中的 Runbook 的詳細資料。
services: automation
ms.subservice: process-automation
ms.date: 04/14/2020
ms.topic: conceptual
ms.openlocfilehash: 053a506ad28978404a147e0604fe731f0b474225
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2020
ms.locfileid: "82855732"
---
# <a name="runbook-execution-in-azure-automation"></a>Azure 自動化中的 Runbook 執行

Azure 自動化中的進程自動化，可讓您建立和管理 PowerShell、PowerShell 工作流程和圖形化 runbook。 如需詳細資訊，請參閱[Azure 自動化 runbook](automation-runbook-types.md)。 

自動化會根據在其中定義的邏輯來執行 runbook。 如果 runbook 中斷，它會在一開始就重新開機。 若發生暫時性問題，您需要撰寫支援重新開機的 runbook。

在 Azure 自動化中啟動 runbook 會建立作業，這是 runbook 的單一執行實例。 每個作業都會透過連線到您的 Azure 訂用帳戶來存取 Azure 資源。 如果可從公用雲端存取這些資源，則作業只能存取您資料中心內的資源。

Azure 自動化指派背景工作角色，以在 runbook 執行期間執行每個作業。 雖然背景工作是由多個 Azure 帳戶共用，來自不同自動化帳戶的工作會彼此隔離。 您無法控制您的作業要求的工作者服務。

當您查看 Azure 入口網站中的 runbook 清單時，它會顯示每個 runbook 已啟動之每個工作的狀態。 Azure 自動化儲存最多30天的作業記錄。

下圖顯示[powershell runbook](automation-runbook-types.md#powershell-runbooks)、 [powershell 工作流程 runbook](automation-runbook-types.md#powershell-workflow-runbooks)和[圖形化 runbook](automation-runbook-types.md#graphical-runbooks)之 runbook 作業的生命週期。

![工作狀態 -PowerShell 工作流程](./media/automation-runbook-execution/job-statuses.png)

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 如需有關混合式 Runbook 背景工作角色的 Az 模組安裝指示，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 針對您的自動化帳戶，您可以使用[如何更新 Azure 自動化中的 Azure PowerShell 模組](automation-update-azure-modules.md)，將模組更新為最新版本。

## <a name="security"></a>安全性

Azure 自動化使用[Azure 資訊安全中心（ASC）](https://docs.microsoft.com/azure/security-center/security-center-introAzure)來為您的資源提供安全性，並在 Linux 系統中偵測到危害。 無論資源是否在 Azure 中，都能在您的工作負載中提供安全性。 請參閱[Azure 自動化中的驗證簡介](https://docs.microsoft.com/azure/automation/automation-security-overview)。

ASC 會對可在 VM 上執行任何腳本（已簽署或未簽署）的使用者施加條件約束。 如果您是具有 VM 根存取權的使用者，您必須使用數位簽章明確設定電腦，或將它關閉。 否則，您只能在建立自動化帳戶並啟用適當功能之後，執行腳本以套用作業系統更新。

## <a name="subscriptions"></a>訂用帳戶

Azure[訂](https://docs.microsoft.com/office365/enterprise/subscriptions-licenses-accounts-and-tenants-for-microsoft-cloud-offerings)用帳戶是 Microsoft 的合約，可讓您使用一或多個以雲端為基礎的服務，您需支付費用。 針對 Azure 自動化，每個訂用帳戶都會連結到 Azure 自動化帳戶，而您可以在帳戶中[建立多個訂閱](manage-runbooks.md#work-with-multiple-subscriptions)。

## <a name="azure-monitor"></a>Azure 監視器

Azure 自動化利用[Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/overview)來監視其機器作業。 作業需要 Log Analytics 工作區和[Log analytics 代理](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent)程式。

### <a name="log-analytics-agent-for-windows"></a>適用於 Windows 的 Log Analytics 代理程式

適用于[windows 的 Log Analytics 代理程式](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windowsmonitor)適用于管理 windows vm 和實體電腦的 Azure 監視器。 這些機器可以在 Azure 中或非 Azure 環境中執行，例如本機資料中心。 您必須將代理程式設定為向一或多個 Log Analytics 工作區報告。

>[!NOTE]
>適用于 Windows 的 Log Analytics 代理程式先前稱為 Microsoft Monitoring Agent （MMA）。

### <a name="log-analytics-agent-for-linux"></a>Log Analytics Linux 代理程式

[Log Analytics linux 代理程式](https://docs.microsoft.com/azure/azure-monitor/platform/agent-linux)的運作方式類似于 Windows 的代理程式，但會將 Linux 電腦連接到 Azure 監視器。 代理程式會以**nxautomation**使用者帳戶安裝，允許執行需要根許可權的命令，例如在混合式 Runbook 背景工作角色上。 **Nxautomation**帳戶是不需要密碼的系統帳戶。 

在[Linux 混合式 Runbook 背景工作角色安裝](automation-linux-hrw-install.md)期間，必須要有具有對應 sudo 許可權的**nxautomation**帳戶。 如果您嘗試安裝背景工作，但帳戶不存在或沒有適當的許可權，則安裝會失敗。

Log Analytics 代理程式和**nxautomation**帳戶可用的記錄如下：

* /var/opt/microsoft/omsagent/log/omsagent.log-Log Analytics 代理程式記錄檔 
* /var/opt/microsoft/omsagent/run/automationworker/worker.log-自動化背景工作記錄檔

## <a name="runbook-execution-environment"></a>Runbook 執行環境

Azure 自動化中的 runbook 可以在 Azure 沙箱或[混合式 Runbook 背景工作角色](automation-hybrid-runbook-worker.md)上執行。 

當 runbook 設計用來對 Azure 中的資源進行驗證和執行時，它們會在 Azure 沙箱中執行，這是多個工作可以使用的共用環境。 使用相同沙箱的作業均受限於該沙箱的資源限制。 Azure 沙箱環境不支援互動式作業。 它會防止存取所有跨進程的 COM 伺服器。 它也需要針對進行 Win32 呼叫的 runbook 使用本機 MOF 檔案。

您也可以使用[混合式 Runbook 背景工作](automation-hybrid-runbook-worker.md)角色，直接在裝載角色的電腦上，以及針對環境中的本機資源執行 runbook。 Azure 自動化會儲存並管理 runbook，然後將其傳遞至一或多個指派的電腦。

>[!NOTE]
>若要在 Linux 混合式 Runbook 背景工作角色上執行，您的腳本必須經過簽署，並據以設定背景工作角色。 或者，簽章[驗證必須關閉](https://docs.microsoft.com/azure/automation/automation-linux-hrw-install#turn-off-signature-validation)。 

下表列出一些 runbook 執行工作，其中列出每個的建議執行環境。

|工作|建議|備忘錄|
|---|---|---|
|與 Azure 資源整合|Azure 沙箱|在 Azure 中託管，驗證會比較簡單。 如果您在 Azure VM 上使用混合式 Runbook 背景工作角色，您可以[使用 Runbook 驗證搭配受控](automation-hrw-run-runbooks.md#runbook-auth-managed-identities)識別。|
|取得最佳效能來管理 Azure 資源|Azure 沙箱|腳本會在相同的環境中執行，但延遲較少。|
|將營運成本最小化|Azure 沙箱|沒有任何計算額外負荷，也不需要 VM。|
|執行長時間執行的腳本|Hybrid Runbook Worker|Azure 沙箱具有[資源限制](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)。|
|與本機服務互動|Hybrid Runbook Worker|可以直接存取主機電腦、其他雲端環境中的資源，或是您的內部部署環境。 |
|需要協力廠商軟體和可執行檔|Hybrid Runbook Worker|您可以管理作業系統並安裝軟體。|
|使用 Runbook 監視檔案或資料夾|Hybrid Runbook Worker|在混合式 Runbook 背景工作角色上使用監看[員工作。](automation-watchers-tutorial.md)|
|執行耗用大量資源的腳本|Hybrid Runbook Worker| Azure 沙箱具有[資源限制](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)。|
|使用具有特定需求的模組| Hybrid Runbook Worker|部份範例如下：</br> WinSCP：winscp.exe 上的相依性 </br> IIS 系統管理-啟用或管理 IIS 的相依性。|
|使用安裝程式安裝模組|Hybrid Runbook Worker|沙箱的模組必須支援複製。|
|使用需要不同于4.7.2 之 .NET Framework 版本的 runbook 或模組|Hybrid Runbook Worker|Automation 沙箱支援 .NET Framework 4.7.2，但不支援升級至不同版本。|
|執行需要提高許可權的腳本|Hybrid Runbook Worker|沙箱不允許提高許可權。 使用混合式 Runbook 背景工作角色時，您可以關閉 UAC，並在執行需要提高許可權的命令時使用[Invoke 命令](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-7)。|
|執行需要存取 Windows Management Instrumentation （WMI）的腳本|Hybrid Runbook Worker|在雲端沙箱中執行的作業無法存取 WMI 提供者。 |

## <a name="resources"></a>資源

您的 runbook 必須包含邏輯來處理資源，例如 Vm、網路和網路上的資源。 資源會系結至 Azure 訂用帳戶，而 runbook 需要適當的認證才能存取任何資源。 請參閱[資源](https://docs.microsoft.com/rest/api/resources/resources)。 如需在 runbook 中處理資源的範例，請參閱[處理資源](manage-runbooks.md#handle-resources)。 

## <a name="credentials"></a>認證

Runbook 需要適當的[認證](shared-resources/credentials.md)，才能存取任何資源，不論是針對 Azure 或協力廠商系統。 這些認證會儲存在 Azure 自動化、Key Vault 等。  

## <a name="runbook-permissions"></a>Runbook 權限

Runbook 需要透過認證向 Azure 進行驗證的許可權。 您可以使用來提供認證：

- 存取本機資源的本機使用者帳戶
- Azure[資源的受控](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)識別，適用于在 azure 上執行的 vm
- 自動化執行身分帳戶，可讓您在 VM 上存取自動化帳戶的憑證，並在本機使用它們進行驗證

## <a name="modules"></a>單元

Azure 自動化支援數個預設模組，包括 AzureRM 模組（AzureRM）和包含數個內部 Cmdlet 的模組。 也支援可安裝的模組，包括 Az 模組（Az. Automation），目前正用於 AzureRM 模組的喜好設定。 如需 runbook 和 DSC 設定可用模組的詳細資訊，請參閱[管理 Azure 自動化中的模組](shared-resources/modules.md)。

## <a name="certificates"></a>憑證

Azure 自動化使用[憑證](shared-resources/certificates.md)向 azure 進行驗證，或將它們新增至 azure 或協力廠商資源。 憑證會安全地儲存，以供 runbook 和 DSC 設定存取。 

您的 runbook 可以使用自我簽署憑證，而不是由憑證授權單位單位（CA）簽署。 請參閱[建立新的憑證](shared-resources/certificates.md#create-a-new-certificate)。

## <a name="jobs"></a>工作

Azure 自動化支援環境，以從相同的自動化帳戶執行作業。 單一 Runbook 一次可以執行許多工作。 同時執行的作業越多，越常會分派到相同的沙箱。 

在相同沙箱進程中執行的作業可能會互相影響。 其中一個範例是執行[Disconnect-disconnect-azaccount](https://docs.microsoft.com/powershell/module/az.accounts/disconnect-azaccount?view=azps-3.7.0) Cmdlet。 執行此 Cmdlet 會中斷共用沙箱進程中的每個 runbook 工作。 如需詳細資訊，請參閱[預防並行作業](manage-runbooks.md#prevent-concurrent-jobs)。

>[!NOTE]
>從在 Azure 沙箱中執行的 runbook 啟動的 PowerShell 工作可能無法以完整[PowerShell 語言模式](/powershell/module/microsoft.powershell.core/about/about_language_modes)執行。 

### <a name="job-statuses"></a>工作狀態

下表描述作業可能的狀態。 您可以在 Azure 入口網站中查看所有 runbook 作業的狀態摘要，或深入瞭解特定 runbook 作業的詳細資料。 您也可以設定與 Log Analytics 工作區的整合，以轉送 Runbook 作業狀態和作業串流。 如需與 Azure 監視器記錄整合的詳細資訊，請參閱[從自動化將作業狀態和作業串流轉送到 Azure 監視器記錄](automation-manage-send-joblogs-log-analytics.md)。 如需在 runbook 中使用狀態的範例，另請參閱[取得作業狀態](manage-runbooks.md#obtain-job-statuses)。

| 狀態 | 描述 |
|:--- |:--- |
| Completed |工作已成功完成。 |
| Failed |無法編譯圖形化或 PowerShell 工作流程 runbook。 PowerShell 腳本 runbook 無法啟動，或作業發生例外狀況。 請參閱[Azure 自動化的 runbook 類型](automation-runbook-types.md)。|
| 處理失敗，正在等候資源 |工作失敗，因為其達到 [公平共用](#fair-share) 的三次上限，且每次從相同的檢查點或啟動 Runbook 開始。 |
| 已排入佇列 |作業正在等候自動化背景工作角色上的資源變成可用，以便可以啟動它。 |
| 啟動中 |此作業已指派給背景工作角色，並且系統正在進行啟動。 |
| 繼續中 |工作暫停後，系統正在繼續工作。 |
| 執行中 |工作正在執行。 |
| 執行中，正在等候資源 |工作已卸載，因為已達到 公平共用 上限。 工作會很快地從其上一個檢查點繼續。 |
| 已停止 |工作完成之前已由使用者停止。 |
| 停止中 |系統正在停止作業。 |
| 暫止 |僅適用于[圖形化和 PowerShell 工作流程 runbook](automation-runbook-types.md) 。 工作已由使用者、系統或 Runbook 中的命令暫停。 如果 runbook 沒有檢查點，則會從頭開始。 如果它有檢查點，則可重新啟動並從其最後一個檢查點繼續。 只有在發生例外狀況時，系統才會暫停 runbook。 根據預設， `ErrorActionPreference`變數會設定為 [繼續]，表示作業會在發生錯誤時持續執行。 如果喜好設定變數設為 [停止]，則作業會在發生錯誤時暫停。  |
| Suspending |僅適用于[圖形化和 PowerShell 工作流程 runbook](automation-runbook-types.md) 。 因使用者要求，系統正在嘗試暫停工作。 Runbook 必須達到其下一個檢查點才能暫停。 如果它已經通過其最後一個檢查點，它就會在暫停之前完成。 |

## <a name="activity-logging"></a>活動記錄

在 Azure 自動化中執行 runbook 會在自動化帳戶的活動記錄檔中寫入詳細資料。 如需使用記錄的詳細資訊，請參閱[從活動記錄抓取詳細資料](manage-runbooks.md#retrieve-details-from-activity-log)。 

## <a name="exceptions"></a>例外狀況

本節說明在您的 runbook 中處理例外狀況或間歇性問題的一些方式。 例如 WebSocket 例外狀況。 正確的例外狀況處理可防止暫時性的網路失敗，而導致您的 runbook 失敗。 

### <a name="erroractionpreference"></a>ErrorActionPreference

[ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference)變數會決定 PowerShell 如何回應非終止錯誤。 終止錯誤一律會終止，而且不會`ErrorActionPreference`受到的影響。

當 runbook 使用`ErrorActionPreference`時，一般非終止的錯誤（例如`PathNotFound`從[get-childitem](https://docs.microsoft.com/powershell/module/microsoft.powershell.management/get-childitem?view=powershell-7) Cmdlet）會阻止 runbook 完成。 下列範例示範 `ErrorActionPreference` 的用法。 當腳本停止時，最後的[寫入輸出](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/write-output?view=powershell-7)命令永遠不會執行。

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-ChildItem -path nofile.txt
Write-Output "This message will not show"
```

### <a name="try-catch-finally"></a>嘗試 Catch Finally

[Try Catch 最後](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally)會在 PowerShell 腳本中用來處理終止錯誤。 腳本可以使用此機制來攔截特定例外狀況或一般例外狀況。 `catch`語句應該用來追蹤或嘗試處理錯誤。 下列範例會嘗試下載不存在的檔案。 它會攔截`System.Net.WebException`例外狀況，並傳回任何其他例外狀況的最後一個值。

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

[Throw](/powershell/module/microsoft.powershell.core/about/about_throw)可以用來產生終止錯誤。 在 runbook 中定義您自己的邏輯時，這項機制會很有用。 如果腳本符合應停止的準則，則可以使用`throw`語句來停止。 下列範例會使用這個語句來顯示必要的函式參數。

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

## <a name="errors"></a>Errors

您的 runbook 必須處理錯誤。 Azure 自動化支援兩種類型的 PowerShell 錯誤，終止和非終止。 

終止錯誤發生時，會停止 runbook 執行。 Runbook 停止，作業狀態為 [失敗]。

非終止錯誤可讓腳本在執行後仍繼續。 非終止錯誤的範例之一，就是當 runbook 使用`Get-ChildItem` Cmdlet 時所發生的路徑不存在。 PowerShell 發現路徑不存在，則會擲回錯誤，並繼續下一步資料夾。 在此情況下，此錯誤不會將 runbook 作業狀態設定為 [失敗]，甚至可能會完成作業。 若要強制 Runbook 在非終止錯誤時停止，您可以在 Cmdlet 上使用 `ErrorAction Stop`。

## <a name="executables-or-calling-processes"></a>可執行檔或呼叫進程

在 Azure 沙箱中執行的 runbook 不支援呼叫進程，例如可執行檔（**.exe**檔）或子流程。 這是因為 Azure 沙箱是在容器中執行的共用進程，可能無法存取所有基礎 Api。 對於需要協力廠商軟體或呼叫子流程的案例，您應該在[混合式 runbook 背景工作角色](automation-hybrid-runbook-worker.md)上執行 runbook。

## <a name="access-to-device-and-application-characteristics"></a>存取裝置和應用程式特性

在 Azure 沙箱中執行的 Runbook 作業無法存取任何裝置或應用程式特性。 最常用來查詢 Windows 效能計量的 API 是 WMI，其中一些常見的計量是記憶體和 CPU 使用量。 不過，使用什麼 API 並不重要，因為在雲端中執行的工作無法存取 Microsoft 的 Web 架構企業管理（WBEM）。 此平臺是以通用訊息模型（CIM）為基礎，提供定義裝置和應用程式特性的業界標準。

## <a name="webhooks"></a>Webhook

外部服務（例如 Azure DevOps Services 和 GitHub）可以在 Azure 自動化中啟動 runbook。 為了執行這種類型的啟動，服務會透過單一 HTTP 要求來使用[webhook](automation-webhooks.md) 。 使用 webhook 可讓 runbook 啟動，而不需要執行完整的 Azure 自動化解決方案。 

## <a name="shared-resources-among-runbooks"></a><a name="fair-share"></a>Runbook 之間的共用資源

若要在雲端中的所有 runbook 之間共用資源，Azure 自動化暫時卸載或停止已執行超過三小時的任何工作。 [PowerShell runbook](automation-runbook-types.md#powershell-runbooks)和[Python runbook](automation-runbook-types.md#python-runbooks)的作業會停止且不會重新開機，且作業狀態會變成 [已停止]。

針對長時間執行的工作，建議使用混合式 Runbook 背景工作角色。 混合式 Runbook 背景工作角色並未受限於公平共用，而且未限制 Runbook 執行時間長度。 其他作業[限制](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)會套用至 Azure 沙箱和混合式 Runbook 背景工作角色。 雖然混合式 Runbook 背景工作角色不受限於3小時的公平共用限制，但您仍應開發 runbook，以便在支援從非預期的本機基礎結構問題重新開機的背景工作上執行。

另一個選項是使用子 runbook 將 runbook 優化。 例如，您的 runbook 可能會在數個資源上迴圈執行相同的函式，例如數個資料庫上的資料庫作業。 您可以將此函式移至[子 runbook](automation-child-runbooks.md) ，並讓您的 Runbook 使用[AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0)呼叫它。 子 runbook 會以平行方式在個別的進程中執行。

使用子 runbook 可減少完成父 runbook 所需的總時間。 您的 runbook 可以使用[AzAutomationJob](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationjob?view=azps-3.7.0)指令程式，檢查子 runbook 的作業狀態（如果它在子系完成後仍有更多的操作）。

## <a name="next-steps"></a>後續步驟

* 若要瞭解如何使用 runbook，請參閱[管理 Azure 自動化中的 runbook](manage-runbooks.md)。
* 若要深入瞭解可在 Azure 自動化中用來啟動 runbook 的方法，請參閱[在 Azure 自動化中啟動 runbook](automation-starting-a-runbook.md)。
* 如需 PowerShell 的詳細資訊 (包括語言參考和學習模組)，請參閱 [PowerShell 文件](https://docs.microsoft.com/powershell/scripting/overview)。
* 如需 PowerShell Cmdlet 參考，請參閱 [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)。
