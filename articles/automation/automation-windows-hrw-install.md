---
title: Azure 自動化 Windows 混合式 Runbook 背景工作
description: 本文提供有關安裝「Azure 自動化混合式 Runbook 背景工作角色」的資訊，您可以使用它在本機資料中心或雲端環境中的 Windows 型電腦上執行 Runbook。
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: 9f3e06f66996be4a2b43b64e6100c62a2fa41381
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77649954"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>部署 Windows 混合式 Runbook 背景工作角色

您可以使用 Azure 自動化的混合式 Runbook 背景工作角色功能，直接在裝載角色的電腦上以及針對環境中的資源執行 Runbook，從而管理這些本機資源。 Runbook 會儲存並在 Azure 自動化中管理，接著傳遞至一或多個指定的電腦。 本文說明如何在 Windows 電腦上部署混合式 Runbook 背景工作角色。

在您成功部署 Runbook 背景工作角色後，請檢閱[在混合式 Runbook 背景工作角色上執行 Runbook](automation-hrw-run-runbooks.md)，以了解如何設定 Runbook 以將內部部署資料中心或其他雲端環境中的程序自動化。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 如需混合式 Runbook 背景工作角色上的 Az module 安裝指示，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 針對您的自動化帳戶，您可以使用[如何更新 Azure 自動化中的 Azure PowerShell 模組](automation-update-azure-modules.md)，將模組更新為最新版本。

## <a name="windows-hybrid-runbook-worker-installation-and-configuration"></a>Windows 混合式 Runbook 背景工作角色安裝和設定

若要安裝及設定 Windows 混合式 Runbook 背景工作角色，您可以使用下列其中一種方法。

* 針對 Azure Vm，您可以使用[適用于 windows 的虛擬機器擴充](../virtual-machines/extensions/oms-windows.md)功能來安裝適用于 Windows 的 Log Analytics 代理程式。 此擴充功能會在 Azure 虛擬機器上安裝 Log Analytics 代理程式，並使用 Azure Resource Manager 範本或使用 PowerShell，將虛擬機器註冊到現有的 Log Analytics 工作區中。 安裝代理程式之後，即可將 VM 新增至自動化帳戶中的「混合式 Runbook 背景工作角色」群組，請遵循下面的「[手動部署](#manual-deployment)」一節底下的**步驟 4** 。

* 使用自動化 runbook，將設定 Windows 電腦的程式完全自動化。 針對您資料中心或其他雲端環境中的機器，這是建議的方法。

* 遵循逐步程式，在您的非 Azure VM 上手動安裝和設定混合式 Runbook 背景工作角色。

> [!NOTE]
> 若要使用 Desired State Configuration (DSC) 來管理支援混合式 Runbook 背景工作角色的伺服器設定，您必須將它們新增為 DSC 節點。

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

### <a name="server-onboarding-for-management-with-automation-dsc"></a>使用 Automation DSC 進行管理的伺服器上架

如需將伺服器上線以使用 DSC 進行管理的相關資訊，請參閱將[機器上架以供 AZURE 自動化 dsc 管理](automation-dsc-onboarding.md)。

如果您啟用[更新管理解決方案](../operations-management-suite/oms-solution-update-management.md)，則任何連線到 Log Analytics 工作區的 Windows 電腦都會自動設定為混合式 Runbook 背景工作角色，以支援此解決方案中包含的 runbook。 不過，它不會向已在您「自動化」帳戶中定義的任何「混合式背景工作角色」群組註冊。

### <a name="adding-the-computer-to-a-hybrid-runbook-worker-group"></a>將電腦新增至混合式 Runbook 背景工作角色群組

您可以將電腦新增至您自動化帳戶中的混合式 Runbook 背景工作角色群組來支援自動化 Runbook，只要解決方案和混合式 Runbook 背景工作角色群組成員資格兩者使用相同的帳戶即可。 此功能已新增至 Hybrid Runbook Worker 7.2.12024.0 版。

## <a name="automated-deployment"></a>自動化部署

在目的電腦上，執行下列步驟來自動化 Windows 混合式背景工作角色的安裝和設定。

### <a name="1-download-the-powershell-script"></a>1. 下載 PowerShell 腳本

直接從執行「混合式 Runbook 背景工作角色」的電腦，或從您環境中的另一部電腦，從[PowerShell 資源庫](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker)下載 New-OnPremiseHybridWorker.ps1 指令碼。 將指令碼複製到背景工作角色。 New-OnPremiseHybridWorker.ps1 指令碼在執行期間需要下列參數：

* *AAResourceGroupName* (必要)：與您的自動化帳戶相關聯的資源群組名稱。
* *OMSResourceGroupName* (選擇性)：Log Analytics 工作區的資源群組名稱。 若未指定此資源群組，將會使用 *AAResourceGroupName*。
* *SubscriptionID* （必要）：您的自動化帳戶所在的 Azure 訂用帳戶識別碼。
* *TenantID* （選用）：與您的自動化帳戶相關聯的租使用者組織識別碼。
* *WorkspaceName* (選擇性)：Log Analytics 工作區名稱。 如果您沒有 Log Analytics 工作區，此指令碼就會建立並設定一個 Log Analytics 工作區。
* *AutomationAccountName* （必要）：您的自動化帳戶的名稱。
* *HybridGroupName* (必要)：您針對支援此案例的 Runbook，指定作為目標的「混合式 Runbook 背景工作角色」群組名稱。
* *認證*（選擇性）：登入 Azure 環境時所要使用的認證。
  
> [!NOTE]
> 啟用解決方案時，只有特定區域支援連結 Log Analytics 工作區和自動化帳戶。 如需支援的對應配對清單，請參閱[自動化帳戶和 Log Analytics 工作區的區域對應](how-to/region-mappings.md)。

### <a name="2-open-windows-powershell-command-line-shell"></a>2. 開啟 Windows PowerShell 命令列 shell

在系統管理員模式下，從 [**開始**] 畫面開啟**Windows PowerShell** 。

### <a name="3-run-the-powershell-script"></a>3. 執行 PowerShell 腳本

從 PowerShell 命令列 shell，流覽至包含您所下載腳本的資料夾。 變更參數 *-AutomationAccountName*、 *-AAResourceGroupName*、 *-OMSResourceGroupName*、 *-HybridGroupName*、 *-SubscriptionId* 及 *-WorkspaceName* 的值。 然後執行指令碼。

執行指令碼之後，系統會提示您向 Azure 進行驗證。 您必須使用屬於訂用帳戶管理員角色成員和訂用帳戶共同管理員的帳戶登入。

```powershell-interactive
.\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
-OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
-SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
```

### <a name="4-install-nuget"></a>4. 安裝 NuGet

系統會提示您同意安裝 NuGet，並使用您的 Azure 認證進行驗證。 如果您沒有最新的 NuGet 版本，您可以從可用的[Nuget 散發版本](https://www.nuget.org/downloads)取得。

### <a name="5-verify-the-deployment"></a>5. 驗證部署

指令碼執行完成之後，[混合式背景工作角色群組] 頁面會顯示新的群組和成員數目。 如果這是現有群組，成員數目就會遞增。 您可以從 [混合式背景**工作角色群組**] 頁面上的清單中選取群組，然後選取 [混合式背景**工作**角色] 磚。 在 [Hybrid Worker] 頁面上，您會看到列出群組的每個成員。

## <a name="manual-deployment"></a>手動部署

在目的電腦上，針對您的自動化環境執行一次前兩個步驟。 然後執行每部背景工作電腦的其餘步驟。

### <a name="1-create-a-log-analytics-workspace"></a>1. 建立 Log Analytics 工作區

如果您還沒有 Log Analytics 工作區，請先參閱[Azure 監視器記錄設計指導](../azure-monitor/platform/design-logs-deployment.md)方針，再建立工作區。

### <a name="2-add-the-automation-solution-to-the-log-analytics-workspace"></a>2. 將自動化解決方案新增至 Log Analytics 工作區

「自動化」解決方案會增加「Azure 自動化」的功能，包括對「混合式 Runbook 背景工作角色」的支援。 當您將解決方案新增至 Log Analytics 工作區時，它會自動將背景工作元件推送至您將在下一個步驟中安裝的代理程式電腦。

若要將**自動化**解決方案新增至您的工作區，請執行下列 PowerShell Cmdlet。

```powershell-interactive
Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <LogAnalyticsWorkspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true -DefaultProfile <IAzureContextContainer>
```

### <a name="3-install-the-log-analytics-agent-for-windows"></a>3. 安裝適用于 Windows 的 Log Analytics 代理程式

適用于 Windows 的 Log Analytics 代理程式會將電腦連接到 Azure 監視器 Log Analytics 工作區。 當您將代理程式安裝在電腦上，並將它連線到您的工作區時，它會自動下載混合式 Runbook 背景工作角色所需的元件。

若要在電腦上安裝代理程式，請依照[將 Windows 電腦連線至 Azure 監視器記錄](../log-analytics/log-analytics-windows-agent.md)檔中的指示進行。 您可以對多部電腦重複此程序，將多個背景工作角色加入至您的環境。

當代理程式在幾分鐘後成功連線到您的 Log Analytics 工作區時，您可以執行下列查詢，以確認它是否正在傳送心跳資料到工作區：

```kusto
Heartbeat 
| where Category == "Direct Agent" 
| where TimeGenerated > ago(30m)
```

在搜尋結果中，您應該會看到電腦的心跳記錄，指出它已連接並向服務報告。 根據預設，每個代理程式都會將一個信號記錄轉送至其指派的工作區。 當 C:\Program Files\Microsoft Monitoring Agent\agent 中具有名為 Agent\agent 出現 azureautomationfiles 的資料夾時，您可以確認代理程式已正確下載自動化解決方案。 若要確認混合式 Runbook 背景工作角色的版本，請流覽至 C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\，並記下 \\*版本* 子資料夾。

### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4. 安裝 runbook 環境並連接到 Azure 自動化

當您將代理程式設定為向 Log Analytics 工作區報告時，自動化解決方案會向下推送 HybridRegistration PowerShell 模組，其中包含**add-hybridrunbookworker** Cmdlet。 使用此 Cmdlet 在電腦上安裝 runbook 環境，並向 Azure 自動化註冊。

以系統管理員模式開啟 PowerShell 工作階段，並執行下列命令來匯入模組。

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module .\HybridRegistration.psd1
```

然後使用下列語法執行**add-hybridrunbookworker** Cmdlet。

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

您可以從 Azure 入口網站中的 [管理金鑰] 頁面取得此 Cmdlet 所需的資訊。 從您「自動化」帳戶的 [設定] 頁面中選取 [金鑰]，以開啟此頁面。

![[管理金鑰] 頁面](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* 針對 [群組群組 *] 參數，* 使用混合式 Runbook 背景工作角色群組的名稱。 如果「自動化」帳戶中已經有這個群組，系統就會將目前的電腦新增至其中。 如果此群組不存在，則會新增此群組。
* 針對 [*端點*] 參數，使用 [管理金鑰] 頁面上的 [ **URL** ] 專案。
* 針對*權杖*參數，使用 [管理金鑰] 頁面上的 [**主要存取金鑰**] 專案。

若要收到有關安裝的詳細資訊，請搭配 *Add-HybridRunbookWorker* 使用 **-Verbose** 參數。

### <a name="5-install-powershell-modules"></a>5. 安裝 PowerShell 模組

Runbook 可以使用「Azure 自動化」環境內所安裝模組中定義的任何活動和 Cmdlet。 這些模組不會自動部署到內部部署電腦，因此您必須手動安裝它們。 例外狀況是 Azure 模組。 此模組預設會安裝，並可讓您存取所有 Azure 服務和活動的 Cmdlet，以進行 Azure 自動化。

由於「混合式 Runbook 背景工作角色」功能的主要目的是要管理本機資源，因此您很有可能需要安裝支援這些資源的模組，特別是 PowerShellGet 模組。 如需安裝 Windows PowerShell 模組的相關資訊，請參閱[Windows powershell](https://docs.microsoft.com/powershell/scripting/developer/windows-powershell)。

已安裝的模組必須位於 PSModulePath 環境變數所參考的位置，才能讓混合式背景工作角色自動匯入它們。 如需詳細資訊，請參閱[在 PSModulePath 中安裝模組](https://docs.microsoft.com/powershell/scripting/developer/module/installing-a-powershell-module?view=powershell-7)。

## <a name="next-steps"></a>後續步驟

* 若要了解如何設定您的 Runbook，將您在內部部署資料中心或其他雲端環境中的程序自動化，請參閱[在混合式 Runbook 背景工作角色上執行 Runbook](automation-hrw-run-runbooks.md)。
* 如需移除混合式 Runbook 背景工作角色的指示，請參閱[移除 Azure 自動化混合式 Runbook 背景工作角色](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker)。
* 若要了解如何針對「混合式 Runbook 背景工作角色」進行疑難排解，請參閱[針對 Windows 混合式 Runbook 背景工作角色進行疑難排解](troubleshoot/hybrid-runbook-worker.md#windows)
* 如需如何針對更新管理的問題進行疑難排解的其他步驟，請參閱[更新管理：疑難排解](troubleshoot/update-management.md)。
