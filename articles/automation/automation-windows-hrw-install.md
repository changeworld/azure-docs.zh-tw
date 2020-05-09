---
title: Azure 自動化 Windows 混合式 Runbook 背景工作
description: 本文提供有關安裝「Azure 自動化混合式 Runbook 背景工作角色」的資訊，您可以使用它在本機資料中心或雲端環境中的 Windows 型電腦上執行 Runbook。
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: 163650a05bf47e6cb8a8832bb85477740d88b0cd
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/05/2020
ms.locfileid: "82787357"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>部署 Windows 混合式 Runbook 背景工作角色

您可以使用 Azure 自動化的混合式 Runbook 背景工作角色功能，直接在裝載角色的電腦上以及針對環境中的資源執行 Runbook，從而管理這些本機資源。 Azure 自動化會儲存並管理 runbook，然後將其傳遞至一或多部指定的電腦。 本文說明如何在 Windows 電腦上部署混合式 Runbook 背景工作角色。

在您成功部署 Runbook 背景工作角色後，請檢閱[在混合式 Runbook 背景工作角色上執行 Runbook](automation-hrw-run-runbooks.md)，以了解如何設定 Runbook 以將內部部署資料中心或其他雲端環境中的程序自動化。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 如需有關混合式 Runbook 背景工作角色的 Az 模組安裝指示，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 針對您的自動化帳戶，您可以使用[如何更新 Azure 自動化中的 Azure PowerShell 模組](automation-update-azure-modules.md)，將模組更新為最新版本。

## <a name="windows-hybrid-runbook-worker-installation-and-configuration"></a>Windows 混合式 Runbook 背景工作角色安裝和設定

若要安裝及設定 Windows 混合式 Runbook 背景工作角色，您可以使用下列其中一種方法。

* 針對 Azure Vm，請使用[適用于 windows 的虛擬機器擴充](../virtual-machines/extensions/oms-windows.md)功能來安裝適用于 Windows 的 Log Analytics 代理程式。 此擴充功能會在 Azure 虛擬機器上安裝 Log Analytics 代理程式，並使用 Azure Resource Manager 範本或 PowerShell，將虛擬機器註冊到現有的 Log Analytics 工作區中。 安裝代理程式之後，您可以將 VM 新增至您的自動化帳戶中的混合式 Runbook 背景工作角色群組。 請參閱[手動部署](#manual-deployment)一節中的步驟3和4。

* 使用自動化 runbook，將設定 Windows 電腦的程式完全自動化。 這是您的資料中心或其他雲端環境中的機器建議使用的方法。

* 遵循逐步程式，在您的非 Azure VM 上手動安裝和設定混合式 Runbook 背景工作角色。

> [!NOTE]
> 若要使用 Desired State Configuration （DSC）來管理支援混合式 Runbook 背景工作角色的伺服器設定，您必須將伺服器新增為 DSC 節點。

### <a name="minimum-requirements-for-windows-hybrid-runbook-worker"></a>Windows 混合式 Runbook 背景工作角色的最低需求

「Windows 混合式 Runbook 背景工作角色」的最低需求如下：

* Windows Server 2012 或更新版本
* Windows PowerShell 5.1 或更新版本（[下載 WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616)）
* .NET Framework 4.6.2 或更新版本
* 雙核心
* 4 GB 的 RAM
* 連接埠 443 (輸出)

### <a name="network-configuration"></a>網路組態

若要了解「混合式 Runbook 背景工作角色」的其他網路需求，請參閱[設定網路](automation-hybrid-runbook-worker.md#network-planning)。

### <a name="server-onboarding-for-management-with-state-configuration-dsc"></a>使用狀態設定（DSC）進行管理的伺服器上架

如需將伺服器上線以使用狀態設定（DSC）進行管理的相關資訊，請參閱將[機器上架以供狀態設定（dsc）管理](automation-dsc-onboarding.md)。

啟用[更新管理](automation-update-management.md)會自動將任何連線到您 Log Analytics 工作區的 Windows 電腦設定為混合式 Runbook 背景工作角色，以支援 Runbook 更新。 不過，此工作者不會向您的自動化帳戶中已定義的任何混合式 Runbook 背景工作角色群組註冊。

### <a name="addition-of-the-computer-to-a-hybrid-runbook-worker-group"></a>將電腦新增至混合式 Runbook 背景工作角色群組

您可以將背景工作電腦新增至自動化帳戶中的「混合式 Runbook 背景工作角色」群組。 請注意，您必須支援自動化 runbook，前提是 Azure 自動化功能和混合式 Runbook 背景工作角色群組成員資格都使用相同的帳戶。 此功能已新增至 Hybrid Runbook Worker 7.2.12024.0 版。

## <a name="automated-deployment"></a>自動化部署

在目的電腦上，執行下列步驟來自動化 Windows 混合式背景工作角色的安裝和設定。

### <a name="step-1---download-the-powershell-script"></a>步驟 1-下載 PowerShell 腳本

從[PowerShell 資源庫](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker)下載**new-onpremisehybridworker.ps1**腳本。 此下載應直接從執行「混合式 Runbook 背景工作角色」的電腦，或從您環境中的另一部電腦。 當您下載腳本之後，請將它複製到您的背景工作角色。 **New-onpremisehybridworker.ps1**腳本會在執行期間使用下面所述的參數。

| 參數 | 狀態 | 描述 |
| --------- | ------ | ----------- |
| `AAResourceGroupName` | 強制性 | 與您的自動化帳戶相關聯的資源群組名稱。 |
| `AutomationAccountName` | 強制性 | 自動化帳戶的名稱。
| `Credential` | 選用 | 登入 Azure 環境時所要使用的認證。 |
| `HybridGroupName` | 強制性 | 您針對支援此案例的 Runbook，指定作為目標的「混合式 Runbook 背景工作角色」群組名稱。 |
| `OMSResourceGroupName` | 選用 | Log Analytics 工作區的資源群組名稱。 如果未指定此資源群組，則`AAResourceGroupName`會使用的值。 |
| `SubscriptionID` | 強制性 | 與您的自動化帳戶相關聯之 Azure 訂用帳戶的識別碼。 |
| `TenantID` | 選用 | 與您的自動化帳戶相關聯的租使用者組織識別碼。 |
| `WorkspaceName` | 選用 | Log Analytics 工作區名稱。 如果您沒有 Log Analytics 工作區，此指令碼就會建立並設定一個 Log Analytics 工作區。 |

> [!NOTE]
> 啟用功能時，Azure 自動化只支援特定區域來連結 Log Analytics 工作區和自動化帳戶。 如需支援的對應配對清單，請參閱[自動化帳戶和 Log Analytics 工作區的區域對應](how-to/region-mappings.md)。

### <a name="step-2---open-windows-powershell-command-line-shell"></a>步驟 2-開啟 Windows PowerShell 命令列 shell

在系統管理員模式下，從 [**開始**] 畫面開啟**Windows PowerShell** 。

### <a name="step-3---run-the-powershell-script"></a>步驟 3-執行 PowerShell 腳本

在 PowerShell 命令列 shell 中，流覽至包含您已下載之腳本的資料夾。 變更`AutomationAccountName`參數、 `AAResourceGroupName`、 `OMSResourceGroupName`、 `HybridGroupName` `SubscriptionID`、和`WorkspaceName`的值。 然後執行指令碼。

執行指令碼之後，系統會提示您向 Azure 進行驗證。 您「必須」以「訂用帳戶管理員」角色成員和訂用帳戶共同管理員的帳戶登入。

```powershell-interactive
.\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
-OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
-SubscriptionID <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
```

### <a name="step-4---install-nuget"></a>步驟 4-安裝 NuGet

系統會提示您同意安裝 NuGet，並使用您的 Azure 認證進行驗證。 如果您沒有最新的 NuGet 版本，您可以從可用的[Nuget 散發版本](https://www.nuget.org/downloads)取得。

### <a name="step-5---verify-the-deployment"></a>步驟 5-驗證部署

指令碼執行完成之後，[混合式背景工作角色群組] 頁面會顯示新的群組和成員數目。 如果這是現有群組，成員數目就會遞增。 您可以從 [混合式背景工作角色群組] 頁面上的清單中選取群組，然後選擇 [混合式背景**工作角色**] 磚。 在 [混合式背景工作角色] 頁面上，您可以看到列出群組的每個成員。

## <a name="manual-deployment"></a>手動部署

在目的電腦上，針對您的自動化環境執行一次前兩個步驟。 然後執行每部背景工作電腦的其餘步驟。

### <a name="step-1---create-a-log-analytics-workspace"></a>步驟 1-建立 Log Analytics 工作區

如果您還沒有 Log Analytics 工作區，請在建立工作區之前，先參閱[Azure 監視器記錄設計指引](../azure-monitor/platform/design-logs-deployment.md)。

### <a name="step-2---add-an-azure-automation-feature-to-the-log-analytics-workspace"></a>步驟 2-將 Azure 自動化功能新增至 Log Analytics 工作區

自動化功能會為 Azure 自動化新增功能，包括混合式 Runbook 背景工作角色的支援。 當您將解決方案新增至 Log Analytics 工作區時，它會自動將您所安裝的背景工作元件，推送至代理程式電腦，如下一個步驟所述。

若要將自動化解決方案新增至您的工作區，請執行下列 PowerShell Cmdlet。

```powershell-interactive
Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <LogAnalyticsWorkspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true -DefaultProfile <IAzureContextContainer>
```

### <a name="step-3---install-the-log-analytics-agent-for-windows"></a>步驟 3-安裝適用于 Windows 的 Log Analytics 代理程式

適用于 Windows 的 Log Analytics 代理程式會將電腦連接到 Azure 監視器 Log Analytics 工作區。 當您將代理程式安裝在電腦上，並將它連線到您的工作區時，它會自動下載混合式 Runbook 背景工作角色所需的元件。

若要在電腦上安裝代理程式，請依照[將 Windows 電腦連線至 Azure 監視器記錄](../log-analytics/log-analytics-windows-agent.md)檔中的指示進行。 您可以對多部電腦重複此程序，將多個背景工作角色加入至您的環境。

當代理程式在幾分鐘後成功連線到您的 Log Analytics 工作區時，您可以執行下列查詢，以確認它是否正在傳送心跳資料至工作區。

```kusto
Heartbeat 
| where Category == "Direct Agent" 
| where TimeGenerated > ago(30m)
```

在搜尋結果中，您應該會看到電腦的心跳記錄，指出它已連接並向服務報告。 根據預設，每個代理程式都會將一個信號記錄轉送至其指派的工作區。 

請使用下列步驟來完成代理程式安裝和設定。

1. 啟用解決方案以將代理程式電腦上架。 請參閱在[工作區中將電腦上架](https://docs.microsoft.com/azure/automation/automation-onboard-solutions-from-automation-account#onboard-machines-in-the-workspace)。
2. 確認代理程式已正確下載自動化解決方案。 
3. 若要確認混合式 Runbook 背景工作角色的版本，請流覽至**C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation** ，並記下**version**子資料夾。

### <a name="step-4---install-the-runbook-environment-and-connect-to-azure-automation"></a>步驟 4-安裝 runbook 環境並連接到 Azure 自動化

當您將代理程式設定為向 Log Analytics 工作區報告時，自動化解決方案會向`HybridRegistration`下推送包含`Add-HybridRunbookWorker` Cmdlet 的 PowerShell 模組。 使用此 Cmdlet 在電腦上安裝 runbook 環境，並向 Azure 自動化註冊。

以系統管理員模式開啟 PowerShell 工作階段，並執行下列命令來匯入模組。

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module .\HybridRegistration.psd1
```

現在，使用`Add-HybridRunbookWorker`下列語法執行 Cmdlet。

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

您可以從 Azure 入口網站的 [管理金鑰] 頁面取得這個 Cmdlet 所需的資訊。 在您的自動化帳戶的 [設定] 頁面上選取 [**金鑰**]，以開啟此頁面。

![[管理金鑰] 頁面](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* 針對`GroupName`參數，請使用混合式 Runbook 背景工作角色群組的名稱。 如果「自動化」帳戶中已經有這個群組，系統就會將目前的電腦新增至其中。 如果此群組不存在，則會新增此群組。
* 針對`EndPoint`參數，使用 [管理金鑰] 頁面上的 [ **URL** ] 專案。
* 針對`Token`參數，請使用 [管理金鑰] 頁面上的 [**主要存取金鑰**] 專案。
* 如有需要，請`Verbose`設定參數以接收安裝的詳細資料。

### <a name="step-5----install-powershell-modules"></a>步驟 5-安裝 PowerShell 模組

Runbook 可以使用 Azure 自動化環境中安裝的模組中定義的任何活動和 Cmdlet。 因為這些模組不會自動部署到內部部署電腦，所以您必須手動安裝它們。 例外狀況是 Azure 模組。 此模組預設會安裝，並可讓您存取所有 Azure 服務和活動的 Cmdlet，以進行 Azure 自動化。

因為混合式 Runbook 背景工作角色的主要目的是要管理本機資源，您很可能需要安裝支援這些資源的模組，特別是`PowerShellGet`模組。 如需安裝 Windows PowerShell 模組的相關資訊，請參閱[Windows powershell](https://docs.microsoft.com/powershell/scripting/developer/windows-powershell)。

已安裝的模組必須位於`PSModulePath`環境變數所參考的位置，才能讓混合式背景工作角色自動將其匯入。 如需詳細資訊，請參閱[在 PSModulePath 中安裝模組](https://docs.microsoft.com/powershell/scripting/developer/module/installing-a-powershell-module?view=powershell-7)。

## <a name="remove-the-hybrid-runbook-worker-from-an-on-premises-windows-computer"></a><a name="remove-windows-hybrid-runbook-worker"></a>從內部部署 Windows 電腦中移除混合式 Runbook 背景工作角色

1. 在 Azure 入口網站中，前往您的自動化帳戶。
2. 在 [帳戶設定]**** 下，選取 [金鑰]**** 並記下 [URL]**** 和 [主要存取金鑰]**** 的值。

3. 在系統管理員模式中開啟 PowerShell 會話，並使用您的 URL 和主要存取金鑰值來執行下列命令。 請使用`Verbose`參數來取得移除程式的詳細記錄。 若要從混合式背景工作角色群組中移除過時的機器，請使用選擇性的 `machineName` 參數。

```powershell-interactive
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey> -machineName <ComputerName>
```

## <a name="remove-a-hybrid-worker-group"></a>移除混合式背景工作角色群組

若要移除混合式 Runbook 背景工作角色群組，您必須先從屬於該群組成員的每一部電腦中移除混合式 Runbook 背景工作角色。 然後使用下列步驟來移除群組：

1. 在 Azure 入口網站中，開啟自動化帳戶。
2. 選取 [程式**自動化**] 下的 [混合式背景**工作角色群組**] 選取您要刪除的群組。 該群組的屬性頁面隨即出現。

   ![屬性頁面](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

3. 在所選群組的屬性頁面上，選取 [刪除]****。 這會顯示訊息要求您確認此動作。 如果您確定要繼續，請選取 [是]****。

   ![確認訊息](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   此程序可能需要數秒鐘的時間完成。 您可以在功能表的 [通知]**** 底下追蹤其進度。

## <a name="next-steps"></a>後續步驟

* 若要了解如何設定您的 Runbook，將您在內部部署資料中心或其他雲端環境中的程序自動化，請參閱[在混合式 Runbook 背景工作角色上執行 Runbook](automation-hrw-run-runbooks.md)。
* 若要瞭解如何針對混合式 Runbook 背景工作角色進行疑難排解，請參閱[疑難排解 Windows 混合式 runbook 背景工作角色](troubleshoot/hybrid-runbook-worker.md#windows)。

