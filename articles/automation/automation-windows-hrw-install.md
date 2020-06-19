---
title: 在 Azure 自動化中部署 Windows 混合式 Runbook 背景工作角色
description: 本文說明如何部署混合式 Runbook 背景工作角色，以用於本地資料中心或雲端環境中的 Windows 電腦上執行 Runbook。
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: e4b8bcf2b6ed5ab9c1160df49b1a6082aaf02f65
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/25/2020
ms.locfileid: "83830458"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>部署 Windows 混合式 Runbook 背景工作角色

您可以使用 Azure 自動化的混合式 Runbook 背景工作角色功能，直接在裝載角色的電腦上以及針對環境中的資源執行 Runbook，從而管理這些本機資源。 Azure 自動化會儲存並管理 Runbook，然後將 Runbook 傳遞至一或多部指定的電腦。 本文說明如何在 Windows 機器上部署混合式 Runbook 背景工作角色。

在您成功部署 Runbook 背景工作角色後，請檢閱[在混合式 Runbook 背景工作角色上執行 Runbook](automation-hrw-run-runbooks.md)，以了解如何設定 Runbook 以將內部部署資料中心或其他雲端環境中的程序自動化。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="windows-hybrid-runbook-worker-installation-and-configuration"></a>Windows 混合式 Runbook 背景工作角色安裝和設定

若要安裝及設定 Windows 混合式 Runbook 背景工作角色，您有下列兩種方法可用。

* 針對 Azure VM，使用[適用於 Windows 的虛擬機器擴充功能](../virtual-machines/extensions/oms-windows.md)，安裝適用於 Windows 的 Log Analytics 代理程式。 此擴充會在 Azure 虛擬機器上安裝 Log Analytics 代理程式，並使用 Azure Resource Manager 範本或 PowerShell，將虛擬機器註冊到現有的 Log Analytics 工作區。 安裝代理程式之後，您就可以將 VM 新增至自動化帳戶中的混合式 Runbook 背景工作角色群組。 請參閱[手動部署](#manual-deployment)一節中的步驟 3 和 4。

* 使用自動化 Runbook，將設定 Windows 電腦的流程完全自動化。 對於資料中心或其他雲端環境中的機器，建議採用此方法。

* 遵循逐步程序，在非 Azure VM 上手動安裝和設定混合式 Runbook 背景工作角色。

> [!NOTE]
> 對於支援混合式 Runbook 背景工作角色的伺服器，若要使用 Desired State Configuration (DSC) 來管理其設定，您必須將伺服器新增為 DSC 節點。

### <a name="minimum-requirements-for-windows-hybrid-runbook-worker"></a>Windows 混合式 Runbook 背景工作角色的最低需求

「Windows 混合式 Runbook 背景工作角色」的最低需求如下：

* Windows Server 2012 或更新版本
* Windows PowerShell 5.1 或更新版本 ([下載 WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616))
* .NET Framework 4.6.2 或更新版本
* 雙核心
* 4 GB 的 RAM
* 連接埠 443 (輸出)

### <a name="network-configuration"></a>網路組態

若要了解「混合式 Runbook 背景工作角色」的其他網路需求，請參閱[設定網路](automation-hybrid-runbook-worker.md#network-planning)。

### <a name="enabling-servers-for-management-with-azure-automation-state-configuration"></a>啟用伺服器以交由「Azure 自動化狀態設定」管理

如需啟用伺服器以交由「Azure 自動化狀態設定」管理的相關資訊，請參閱[啟用機器以交由「Azure 自動化狀態設定」管理](automation-dsc-onboarding.md)。

如果啟用 Azure 自動化[更新管理](automation-update-management.md)，則會自動將任何連線到 Log Analytics 工作區的 Windows 電腦，設定為「混合式 Runbook 背景工作角色」，以支援 Runbook 更新。 不過，此背景工作角色不會向自動化帳戶中已定義的任何「混合式 Runbook 背景工作角色」群組註冊。

### <a name="addition-of-the-computer-to-a-hybrid-runbook-worker-group"></a>將電腦新增至混合式 Runbook 背景工作角色群組

您可以將背景工作角色電腦新增至自動化帳戶中的「混合式 Runbook 背景工作角色」群組。 請注意，只要 Azure 自動化功能和「混合式 Runbook 背景工作角色」群組成員資格都使用相同的帳戶，您就必須支援自動化 Runbook。 此功能已新增至 Hybrid Runbook Worker 7.2.12024.0 版。

## <a name="automated-deployment"></a>自動化部署

在目標電腦上，執行下列步驟以自動安裝和設定 Windows 混合式背景工作角色。

### <a name="step-1---download-the-powershell-script"></a>步驟 1 - 下載 PowerShell 指令碼

從 [PowerShell 資源庫](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker)下載 **New-OnPremiseHybridWorker.ps1** 指令碼。 應該直接從執行「混合式 Runbook 背景工作角色」的電腦，或從環境中的另一部電腦來下載。 下載指令碼之後，複製到背景工作角色。 **New-OnPremiseHybridWorker.ps1** 指令碼在執行期間會使用如下所述的參數。

| 參數 | 狀態 | 描述 |
| --------- | ------ | ----------- |
| `AAResourceGroupName` | 強制性 | 與您的自動化帳戶相關聯的資源群組名稱。 |
| `AutomationAccountName` | 強制性 | 自動化帳戶的名稱。
| `Credential` | 選用 | 登入 Azure 環境時所要使用的認證。 |
| `HybridGroupName` | 強制性 | 您針對支援此案例的 Runbook，指定作為目標的「混合式 Runbook 背景工作角色」群組名稱。 |
| `OMSResourceGroupName` | 選用 | Log Analytics 工作區的資源群組名稱。 如果未指定此資源群組，則會使用 `AAResourceGroupName` 的值。 |
| `SubscriptionID` | 強制性 | 識別碼，代表與自動化帳戶相關聯的 Azure 訂用帳戶。 |
| `TenantID` | 選用 | 識別碼，代表與自動化帳戶相關聯的租用戶組織。 |
| `WorkspaceName` | 選用 | Log Analytics 工作區名稱。 如果您沒有 Log Analytics 工作區，此指令碼就會建立並設定一個 Log Analytics 工作區。 |

> [!NOTE]
> 啟用功能時，Azure 自動化只支援特定區域才能連結 Log Analytics 工作區和自動化帳戶。 如需支援的對應配對清單，請參閱[自動化帳戶和 Log Analytics 工作區的區域對應](how-to/region-mappings.md)。

### <a name="step-2---open-windows-powershell-command-line-shell"></a>步驟 2 - 開啟 Windows PowerShell 命令列殼層

從 [開始] 畫面，以系統管理員模式開啟 **Windows PowerShell**。

### <a name="step-3---run-the-powershell-script"></a>步驟 3 - 執行 PowerShell 指令碼

在 PowerShell 命令列殼層中，瀏覽至已下載的指令碼所在的資料夾。 變更參數 `AutomationAccountName`、`AAResourceGroupName`、`OMSResourceGroupName`、`HybridGroupName`、`SubscriptionID` 和 `WorkspaceName` 的值。 然後執行指令碼。

執行指令碼之後，系統會提示您向 Azure 進行驗證。 您必須以「訂用帳戶管理員」角色成員和訂用帳戶共同管理員的帳戶登入。

```powershell-interactive
.\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
-OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
-SubscriptionID <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
```

### <a name="step-4---install-nuget"></a>步驟 4 - 安裝 NuGet

系統會提示您同意安裝 NuGet，以及使用您的 Azure 認證進行驗證。 如果您沒有最新的 NuGet 版本，請從[可用的 NuGet 散發版本](https://www.nuget.org/downloads)取得最新版本。

### <a name="step-5---verify-the-deployment"></a>步驟 5 - 驗證部署

指令碼完成之後，[混合式背景工作角色群組] 頁面會顯示新的群組和成員數目。 如果這是現有群組，成員數目就會遞增。 您可以從 [混合式背景工作角色群組] 頁面上的清單中選取群組，然後選擇 [混合式背景工作角色] 圖格。 在 [混合式背景工作角色] 頁面上，您可以看到列出群組的每個成員。

## <a name="manual-deployment"></a>手動部署

在目標電腦上，針對您的自動化環境，執行一次開頭兩個步驟。 然後，針對每一部背景工作角色電腦，執行其餘步驟。

### <a name="step-1---create-a-log-analytics-workspace"></a>步驟 1 - 建立 Log Analytics 工作區

如果您還沒有 Log Analytics 工作區，則在建立工作區之前，請參閱 [Azure 監視器記錄設計指導](../azure-monitor/platform/design-logs-deployment.md)。

### <a name="step-2---add-an-azure-automation-feature-to-the-log-analytics-workspace"></a>步驟 2 - 將 Azure 自動化功能新增至 Log Analytics 工作區

自動化功能擴充「Azure 自動化」的功能性，包括支援「混合式 Runbook 背景工作角色」。 當您在 Log Analytics 工作區啟用 Azure 自動化功能時，背景工作角色元件會自動發送至代理程式電腦。

若要將 Azure 自動化功能 (例如更新管理) 新增至工作區，請執行下列 PowerShell Cmdlet：

```powershell-interactive
Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <LogAnalyticsWorkspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true -DefaultProfile <IAzureContextContainer>
```

### <a name="step-3---install-the-log-analytics-agent-for-windows"></a>步驟 3 - 安裝適用於 Windows 的 Log Analytics 代理程式

適用於 Windows 的 Log Analytics 代理程式會將電腦連線到 Azure 監視器 Log Analytics 工作區。 當您將代理程式安裝在電腦上，並連線到工作區時，將會自動下載「混合式 Runbook 背景工作角色」所需的元件。

若要將代理程式安裝在電腦上，請依照[將 Windows 電腦連線到 Azure 監視器記錄](../log-analytics/log-analytics-windows-agent.md)中的指示進行。 您可以對多部電腦重複此程序，將多個背景工作角色加入至您的環境。

幾分鐘後，當代理程式已成功連線到 Log Analytics 工作區時，您可以執行下列查詢，以驗證代理程式是否正在將活動訊號資料傳送至工作區。

```kusto
Heartbeat 
| where Category == "Direct Agent" 
| where TimeGenerated > ago(30m)
```

在搜尋結果中，您應該會看到電腦的活動訊號記錄，其中指出電腦已連線，且正在向服務報告。 根據預設，每個代理程式會將活動訊號記錄轉送至其獲指派的工作區。 使用下列步驟來完成代理程式安裝和設定。

1. 啟用功能以新增代理程式機器。 請參閱[在工作區中啟用機器](https://docs.microsoft.com/azure/automation/automation-onboard-solutions-from-automation-account#onboard-machines-in-the-workspace)。
2. 驗證代理程式是否已正確下載 Azure 自動化功能。 
3. 若要確認「混合式 Runbook 背景工作角色」的版本，請瀏覽至 **C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation**，並記下 **version** 子資料夾。

### <a name="step-4---install-the-runbook-environment-and-connect-to-azure-automation"></a>步驟 4 - 安裝 Runbook 環境並連線到 Azure 自動化

當您將代理程式設定為向 Log Analytics 工作區報告時，Azure 自動化功能會向下推送 `HybridRegistration` PowerShell 模組，其中包含 `Add-HybridRunbookWorker` Cmdlet。 使用此 Cmdlet 在電腦上安裝 Runbook 環境，並向 Azure 自動化註冊。

以系統管理員模式開啟 PowerShell 工作階段，並執行下列命令來匯入模組。

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module .\HybridRegistration.psd1
```

現在，使用下列語法執行 `Add-HybridRunbookWorker` Cmdlet。

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

您可以從 Azure 入口網站的 [管理金鑰] 頁面中，取得此 Cmdlet 所需的資訊。 在自動化帳戶中，從 [設定] 頁面上選取 [金鑰]，以開啟此頁面。

![[管理金鑰] 頁面](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* 針對 `GroupName` 參數，使用「混合式 Runbook 背景工作角色」群組的名稱。 如果「自動化」帳戶中已經有這個群組，系統就會將目前的電腦新增至其中。 如果此群組不存在，則會新增此群組。
* 針對 `EndPoint` 參數，使用 [管理金鑰] 頁面上的 [URL] 項目。
* 針對 `Token` 參數，使用 [管理金鑰] 頁面上的 [主要存取金鑰] 項目。
* 如有需要，請設定 `Verbose` 參數，以接收有關安裝的詳細資料。

### <a name="step-5----install-powershell-modules"></a>步驟 5 - 安裝 PowerShell 模組

Runbook 可以使用 Azure 自動化環境中安裝的模組中定義的任何活動和 Cmdlet。 因為這些模組不會自動部署到內部部署電腦，所以必須手動安裝。 但 Azure 模組例外。 此模組預設為安裝，可讓您存取 Cmdlet 以執行 Azure 自動化的所有 Azure 服務和活動。

因為「混合式 Runbook 背景工作角色」主要是管理本機資源，您很可能需要安裝支援這些資源的模組，特別是 `PowerShellGet` 模組。 如需安裝 Windows PowerShell 模組的相關資訊，請參閱 [Windows PowerShell](https://docs.microsoft.com/powershell/scripting/developer/windows-powershell)。

安裝的模組必須位於 `PSModulePath` 環境變數所參考的位置，才能由混合式背景工作角色自動匯入。 如需詳細資訊，請參閱[在 PSModulePath 中安裝模組](https://docs.microsoft.com/powershell/scripting/developer/module/installing-a-powershell-module?view=powershell-7)。

## <a name="remove-the-hybrid-runbook-worker-from-an-on-premises-windows-computer"></a><a name="remove-windows-hybrid-runbook-worker"></a>從內部部署 Windows 電腦中移除混合式 Runbook 背景工作角色

1. 在 Azure 入口網站中，前往您的自動化帳戶。
2. 在 [帳戶設定] 下，選取 [金鑰] 並記下 [URL] 和 [主要存取金鑰] 的值。

3. 以系統管理員模式開啟 PowerShell 工作階段，並使用您的 URL 和主要存取金鑰值執行下列命令。 使用 `Verbose` 參數，以取得移除流程的詳細記錄。 若要從混合式背景工作角色群組中移除過時的機器，請使用選擇性的 `machineName` 參數。

```powershell-interactive
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey> -machineName <ComputerName>
```

## <a name="remove-a-hybrid-worker-group"></a>移除混合式背景工作角色群組

若要移除混合式 Runbook 背景工作角色群組，您必須先從屬於該群組的每一部電腦中，移除混合式 Runbook 背景工作角色。 然後，使用下列步驟來移除群組：

1. 在 Azure 入口網站中，開啟自動化帳戶。
2. 在 [程序自動化] 下，選取 [混合式背景工作角色群組]。 選取您要刪除的群組。 該群組的屬性頁面隨即出現。

   ![屬性頁面](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

3. 在所選群組的屬性頁面上，選取 [刪除]。 這會顯示訊息要求您確認此動作。 如果您確定要繼續，請選取 [是]。

   ![確認訊息](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   此程序可能需要數秒鐘的時間完成。 您可以在功能表的 [通知] 底下追蹤其進度。

## <a name="next-steps"></a>後續步驟

* 若要了解如何設定您的 Runbook，將您在內部部署資料中心或其他雲端環境中的程序自動化，請參閱[在混合式 Runbook 背景工作角色上執行 Runbook](automation-hrw-run-runbooks.md)。
* 若要了解如何對混合式 Runbook 背景工作角色進行疑難排解，請參閱[對混合式 Runbook 背景工作角色問題進行疑難排解](troubleshoot/hybrid-runbook-worker.md#general)。