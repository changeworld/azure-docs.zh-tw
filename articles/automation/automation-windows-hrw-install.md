---
title: 在 Azure 自動化中部署 Windows 混合式 Runbook 背景工作角色
description: 本文說明如何部署混合式 Runbook 背景工作角色，以用來在本機資料中心或雲端環境中的 Windows 電腦上執行 runbook。
services: automation
ms.subservice: process-automation
ms.date: 06/24/2020
ms.topic: conceptual
ms.openlocfilehash: e1262aedda95f3feb7cf5604644d938bf4d00a53
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85854909"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>部署 Windows 混合式 Runbook 背景工作角色

您可以使用 Azure 自動化的「混合式 Runbook 背景工作角色」功能，直接在裝載角色的電腦上執行 runbook，並針對環境中的資源來管理這些本機資源。 Azure 自動化會儲存並管理 runbook，然後將其傳遞至一或多個指定的機器。 本文說明如何在 Windows 電腦上部署混合式 Runbook 背景工作角色、如何移除背景工作，以及如何移除混合式 Runbook 背景工作角色群組。

在您成功部署 Runbook 背景工作角色後，請檢閱[在混合式 Runbook 背景工作角色上執行 Runbook](automation-hrw-run-runbooks.md)，以了解如何設定 Runbook 以將內部部署資料中心或其他雲端環境中的程序自動化。

## <a name="prerequisites"></a>必要條件

開始之前，請確定您具有下列各項。

### <a name="a-log-analytics-workspace"></a>Log Analytics 工作區

「混合式 Runbook 背景工作角色」取決於 Azure 監視器 Log Analytics 工作區，以安裝和設定角色。 您可以透過[Azure Resource Manager](../azure-monitor/samples/resource-manager-workspace.md#create-a-log-analytics-workspace)、透過[PowerShell](../azure-monitor/scripts/powershell-sample-create-workspace.md?toc=/powershell/module/toc.json)或在[Azure 入口網站](../azure-monitor/learn/quick-create-workspace.md)中建立它。

如果您沒有 Azure 監視器 Log Analytics 工作區，請在建立工作區之前，先參閱[Azure 監視器記錄設計指引](../azure-monitor/platform/design-logs-deployment.md)。

如果您有工作區，但它並未連結至您的自動化帳戶，則啟用自動化功能可為 Azure 自動化新增功能，包括混合式 Runbook 背景工作角色的支援。 當您在 Log Analytics 工作區中啟用其中一項 Azure 自動化功能時，特別是[更新管理](automation-update-management.md)或[變更追蹤和清查](change-tracking.md)，背景工作元件會自動推送至代理程式電腦。

   若要將更新管理功能新增至您的工作區，請執行下列 PowerShell Cmdlet：

```powershell-interactive
   Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <logAnalyticsWorkspaceName> -IntelligencePackName "Updates" -Enabled $true
```

   若要將變更追蹤和清查功能新增至您的工作區，請執行下列 PowerShell Cmdlet：

```powershell-interactive
   Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <logAnalyticsWorkspaceName> -IntelligencePackName "ChangeTracking" -Enabled $true
```

### <a name="log-analytics-agent"></a>Log Analytics 代理程式

混合式 Runbook 背景工作角色需要[Log Analytics 代理程式](../azure-monitor/platform/log-analytics-agent.md)，才能進行支援的 Windows 作業系統。

### <a name="supported-windows-operating-system"></a>支援的 Windows 作業系統

Windows 混合式 Runbook 背景工作角色已正式支援下列版本的 Windows 作業系統：

* Windows Server 2019
* Windows Server 2016 1709 版和 1803 版
* Windows Server 2012、2012 R2
* Windows Server 2008 SP2 (x64)、2008 R2
* Windows 10 企業版 (包括多工作階段) 和專業版
* Windows 8 企業版和專業版
* Windows 7 SP1

### <a name="minimum-requirements"></a>最低要求條件

「Windows 混合式 Runbook 背景工作角色」的最低需求如下：

* Windows PowerShell 5.1 或更新版本 ([下載 WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616))
* .NET Framework 4.6.2 或更新版本
* 雙核心
* 4 GB 的 RAM
* 連接埠 443 (輸出)

### <a name="network-configuration"></a>網路組態

若要了解「混合式 Runbook 背景工作角色」的其他網路需求，請參閱[設定網路](automation-hybrid-runbook-worker.md#network-planning)。

### <a name="adding-a-machine-to-a-hybrid-runbook-worker-group"></a>將機器新增至混合式 Runbook 背景工作角色群組

您可以將背景工作電腦新增至自動化帳戶中的「混合式 Runbook 背景工作角色」群組。 請注意，只要 Azure 自動化功能和「混合式 Runbook 背景工作角色」群組成員資格都使用相同的帳戶，您就必須支援自動化 Runbook。 此功能已新增至 Hybrid Runbook Worker 7.2.12024.0 版。

>[!NOTE]
>啟用 Azure 自動化[更新管理](automation-update-management.md)會自動將任何連線到您 Log Analytics 工作區的 Windows 電腦設定為混合式 Runbook 背景工作角色，以支援管理其作業系統更新。 不過，此背景工作角色不會向自動化帳戶中已定義的任何「混合式 Runbook 背景工作角色」群組註冊。

## <a name="enabling-machines-for-management-with-azure-automation-state-configuration"></a>使用 Azure 自動化狀態設定來啟用電腦管理

如需有關啟用電腦以 Azure 自動化狀態設定進行管理的詳細資訊，請參閱[Azure 自動化狀態設定啟用電腦以進行管理](automation-dsc-onboarding.md)。

> [!NOTE]
> 若要使用 Desired State Configuration （DSC）來管理支援混合式 Runbook 背景工作角色的電腦設定，您必須將電腦新增為 DSC 節點。

## <a name="windows-hybrid-runbook-worker-installation-options"></a>Windows 混合式 Runbook 背景工作安裝選項

若要安裝及設定 Windows 混合式 Runbook 背景工作角色，您有下列兩種方法可用。

* 針對 Azure VM，使用[適用於 Windows 的虛擬機器擴充功能](../virtual-machines/extensions/oms-windows.md)，安裝適用於 Windows 的 Log Analytics 代理程式。 此擴充會在 Azure 虛擬機器上安裝 Log Analytics 代理程式，並使用 Azure Resource Manager 範本或 PowerShell，將虛擬機器註冊到現有的 Log Analytics 工作區。 安裝代理程式之後，您就可以將 VM 新增至自動化帳戶中的混合式 Runbook 背景工作角色群組。

* 針對非 Azure Vm，請使用將[windows 電腦連線至 Azure 監視器](../azure-monitor/platform/agent-windows.md)一文中所述的部署選項，安裝適用于 Windows 的 Log Analytics 代理程式。 您可以為多部電腦重複此程式，以將多個背景工作角色新增至您的環境。 安裝代理程式之後，您可以將 Vm 新增至您的自動化帳戶中的混合式 Runbook 背景工作角色群組。

* 使用提供的 PowerShell 腳本，將設定一或多部 Windows 電腦的程式完全[自動化](#automated-deployment)。 對於資料中心或其他雲端環境中的機器，建議採用此方法。

## <a name="automated-deployment"></a>自動化部署

在目的電腦上，執行下列步驟，使用 PowerShell 腳本**New-OnPremiseHybridWorker.ps1**自動安裝和設定 Windows 混合式背景工作角色。 指令碼會執行下列步驟：

* 安裝必要的模組
* 使用您的 Azure 帳戶登入
* 驗證指定的資源群組和自動化帳戶是否存在
* 建立自動化帳戶屬性的參考
* 建立 Azure 監視器 Log Analytics 工作區（如果未指定）
* 在工作區中啟用 Azure 自動化解決方案
* 下載並安裝適用于 Windows 的 Log Analytics 代理程式
* 將電腦註冊為混合式 Runbook 背景工作角色

### <a name="step-1---download-the-powershell-script"></a>步驟 1 - 下載 PowerShell 指令碼

從 [PowerShell 資源庫](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker)下載 **New-OnPremiseHybridWorker.ps1** 指令碼。 下載腳本之後，請在目的電腦上複製或執行。 **New-OnPremiseHybridWorker.ps1** 指令碼在執行期間會使用如下所述的參數。

| 參數 | 狀態 | 描述 |
| --------- | ------ | ----------- |
| `AAResourceGroupName` | 強制性 | 與您的自動化帳戶相關聯的資源群組名稱。 |
| `AutomationAccountName` | 強制性 | 自動化帳戶的名稱。
| `Credential` | 選擇性 | 登入 Azure 環境時所要使用的認證。 |
| `HybridGroupName` | 強制性 | 您針對支援此案例的 Runbook，指定作為目標的「混合式 Runbook 背景工作角色」群組名稱。 |
| `OMSResourceGroupName` | 選擇性 | Log Analytics 工作區的資源群組名稱。 如果未指定此資源群組，則會使用 `AAResourceGroupName` 的值。 |
| `SubscriptionID` | 強制性 | 識別碼，代表與自動化帳戶相關聯的 Azure 訂用帳戶。 |
| `TenantID` | 選擇性 | 識別碼，代表與自動化帳戶相關聯的租用戶組織。 |
| `WorkspaceName` | 選用 | Log Analytics 工作區名稱。 如果您沒有 Log Analytics 工作區，此指令碼就會建立並設定一個 Log Analytics 工作區。 |

> [!NOTE]
> 啟用功能時，Azure 自動化只支援特定區域才能連結 Log Analytics 工作區和自動化帳戶。 如需支援的對應配對清單，請參閱[自動化帳戶和 Log Analytics 工作區的區域對應](how-to/region-mappings.md)。

### <a name="step-2---open-windows-powershell-command-line-shell"></a>步驟 2 - 開啟 Windows PowerShell 命令列殼層

從 [**開始] 功能表**按一下 [**開始**]，輸入**PowerShell**，在 [ **Windows PowerShell**] 上按一下滑鼠右鍵，然後按一下 [以**系統管理員身分執行**]。

### <a name="step-3---run-the-powershell-script"></a>步驟 3 - 執行 PowerShell 指令碼

在 PowerShell 命令列殼層中，瀏覽至已下載的指令碼所在的資料夾。 變更參數 `AutomationAccountName`、`AAResourceGroupName`、`OMSResourceGroupName`、`HybridGroupName`、`SubscriptionID` 和 `WorkspaceName` 的值。 然後執行指令碼。

執行指令碼之後，系統會提示您向 Azure 進行驗證。 您必須以「訂用帳戶管理員」角色成員和訂用帳戶共同管理員的帳戶登入。

```powershell-interactive
.\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <nameOfAutomationAccount> -AAResourceGroupName <nameOfResourceGroup>`
-OMSResourceGroupName <nameOfOResourceGroup> -HybridGroupName <nameOfHRWGroup> `
-SubscriptionID <subscriptionId> -WorkspaceName <nameOfLogAnalyticsWorkspace>
```

### <a name="step-4---install-nuget"></a>步驟 4 - 安裝 NuGet

系統會提示您同意安裝 NuGet，以及使用您的 Azure 認證進行驗證。 如果您沒有最新的 NuGet 版本，您可以從可用的[Nuget 散發版本](https://www.nuget.org/downloads)下載。

### <a name="step-5---verify-the-deployment"></a>步驟 5 - 驗證部署

腳本完成之後，您自動化帳戶中的 [混合式背景工作角色群組] 頁面會顯示新的群組和成員數目。 如果這是現有群組，成員數目就會遞增。 您可以從 [混合式背景工作角色群組] 頁面上的清單中選取群組，然後選擇 [混合式背景工作角色] 圖格。 在 [混合式背景工作角色] 頁面上，您可以看到列出群組的每個成員。

## <a name="manual-deployment"></a>手動部署

若要安裝及設定 Windows 混合式 Runbook 背景工作角色，請執行下列步驟。

### <a name="step-1---verify-agent-is-reporting-to-workspace"></a>步驟 1-確認代理程式已向工作區報告

適用于 Windows 的 Log Analytics 代理程式會將機器連接到 Azure 監視器 Log Analytics 工作區。 當您將代理程式安裝在電腦上，並將它連線到您的工作區時，它會自動下載混合式 Runbook 背景工作角色所需的元件。

幾分鐘後，當代理程式已成功連線到 Log Analytics 工作區時，您可以執行下列查詢，以驗證代理程式是否正在將活動訊號資料傳送至工作區。

```kusto
Heartbeat 
| where Category == "Direct Agent"
| where TimeGenerated > ago(30m)
```

在搜尋結果中，您應該會看到機器的心跳記錄，指出它已連接並向服務報告。 根據預設，每個代理程式會將活動訊號記錄轉送至其獲指派的工作區。 使用下列步驟來完成代理程式安裝和設定。

1. 啟用功能以新增代理程式機器。 如更新管理和 Azure Vm，請參閱[啟用 Azure vm](automation-onboard-solutions-from-automation-account.md#enable-azure-vms)，針對非 Azure vm，請參閱在[工作區中啟用電腦](automation-onboard-solutions-from-automation-account.md#enable-machines-in-the-workspace)。 如變更追蹤和 Azure Vm，請參閱[啟用 Azure vm](automation-enable-changes-from-auto-acct.md#enable-azure-vms)，針對非 Azure vm，請參閱在[工作區中啟用電腦](automation-enable-changes-from-auto-acct.md#enable-machines-in-the-workspace)。

2. 若要確認混合式 Runbook 背景工作角色的版本，請流覽 `C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\` 並記下**version**子資料夾。

### <a name="step-3---install-the-runbook-environment-and-connect-to-azure-automation"></a>步驟 3-安裝 runbook 環境並連接到 Azure 自動化

當您將代理程式設定為向 Log Analytics 工作區報告時，Azure 自動化功能會向下推送 `HybridRegistration` PowerShell 模組，其中包含 `Add-HybridRunbookWorker` Cmdlet。 使用此 Cmdlet 在電腦上安裝 runbook 環境，並向 Azure 自動化註冊。

以系統管理員模式開啟 PowerShell 工作階段，並執行下列命令來匯入模組。

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module .\HybridRegistration.psd1
```

現在，使用下列語法執行 `Add-HybridRunbookWorker` Cmdlet。

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -Url <Url> -Key <String>
```

您可以 `Url` `Key` 從自動化帳戶的 [**金鑰**] 頁面取得參數所需的資訊。 從頁面左側的 [**帳戶設定**] 區段下選取 [**金鑰**]。

![[管理金鑰] 頁面](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* 針對 `Url` 參數，複製 [ **URL**] 的值。

* 針對 `Key` 參數，複製 [**主要存取金鑰**] 的值。

* 針對 `GroupName` 參數，使用「混合式 Runbook 背景工作角色」群組的名稱。 如果此群組已存在於自動化帳戶中，則會新增目前的電腦。 如果此群組不存在，則會新增此群組。

* 如有需要，請設定 `Verbose` 參數，以接收有關安裝的詳細資料。

### <a name="step-4----install-powershell-modules"></a>步驟 4-安裝 PowerShell 模組

Runbook 可以使用 Azure 自動化環境中安裝的模組中定義的任何活動和 Cmdlet。 因為這些模組不會自動部署至內部部署機器，所以您必須手動安裝它們。 但 Azure 模組例外。 此模組預設為安裝，可讓您存取 Cmdlet 以執行 Azure 自動化的所有 Azure 服務和活動。

因為「混合式 Runbook 背景工作角色」主要是管理本機資源，您很可能需要安裝支援這些資源的模組，特別是 `PowerShellGet` 模組。 如需安裝 Windows PowerShell 模組的相關資訊，請參閱 [Windows PowerShell](https://docs.microsoft.com/powershell/scripting/developer/windows-powershell)。

安裝的模組必須位於 `PSModulePath` 環境變數所參考的位置，才能由混合式背景工作角色自動匯入。 如需詳細資訊，請參閱[在 PSModulePath 中安裝模組](https://docs.microsoft.com/powershell/scripting/developer/module/installing-a-powershell-module?view=powershell-7)。

## <a name="remove-the-hybrid-runbook-worker-from-an-on-premises-windows-machine"></a><a name="remove-windows-hybrid-runbook-worker"></a>從內部部署 Windows 機器移除混合式 Runbook 背景工作角色

1. 在 Azure 入口網站中，前往您的自動化帳戶。

2. 在 [帳戶設定] 下，選取 [金鑰] 並記下 [URL] 和 [主要存取金鑰] 的值。

3. 以系統管理員模式開啟 PowerShell 工作階段，並使用您的 URL 和主要存取金鑰值執行下列命令。 使用 `Verbose` 參數，以取得移除流程的詳細記錄。 若要從混合式背景工作角色群組中移除過時的機器，請使用選擇性的 `machineName` 參數。

```powershell-interactive
Remove-HybridRunbookWorker -Url <URL> -Key <primaryAccessKey> -MachineName <computerName>
```

## <a name="remove-a-hybrid-worker-group"></a>移除混合式背景工作角色群組

若要移除混合式 Runbook 背景工作角色群組，您必須先從屬於該群組成員的每一部電腦中移除混合式 Runbook 背景工作角色。 然後，使用下列步驟來移除群組：

1. 在 Azure 入口網站中，開啟自動化帳戶。

2. 在 [程序自動化] 下，選取 [混合式背景工作角色群組]。 選取您要刪除的群組。 該群組的屬性頁面隨即出現。

   ![屬性頁面](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

3. 在所選群組的屬性頁面上，選取 [刪除]。 這會顯示訊息要求您確認此動作。 如果您確定要繼續，請選取 [是]。

   ![確認訊息](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   此程序可能需要數秒鐘的時間完成。 您可以在功能表的 [通知] 底下追蹤其進度。

## <a name="next-steps"></a>後續步驟

* 若要了解如何設定您的 Runbook，將您在內部部署資料中心或其他雲端環境中的程序自動化，請參閱[在混合式 Runbook 背景工作角色上執行 Runbook](automation-hrw-run-runbooks.md)。

* 若要了解如何對混合式 Runbook 背景工作角色進行疑難排解，請參閱[對混合式 Runbook 背景工作角色問題進行疑難排解](troubleshoot/hybrid-runbook-worker.md#general)。
