---
title: 在 Azure 自動化中部署 Windows 混合式 Runbook 背景工作角色
description: 本文說明如何部署混合式 Runbook 背景工作角色，讓您可以在本機資料中心或雲端環境中，用來在 Windows 電腦上執行 runbook。
services: automation
ms.subservice: process-automation
ms.date: 11/24/2020
ms.topic: conceptual
ms.openlocfilehash: e77a90617d79dab8c71cdf0c7a6a4fb21e052fbd
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96182779"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>部署 Windows 混合式 Runbook 背景工作角色

您可以使用 Azure 自動化的使用者混合式 Runbook 背景工作角色功能直接在 Azure 或非 Azure 電腦上執行 runbook，包括已在 [啟用 Azure Arc 的伺服器](../azure-arc/servers/overview.md)上註冊的伺服器。 從裝載角色的電腦或伺服器，您可以直接對其執行 runbook，並針對環境中的資源執行，以管理這些本機資源。

Azure 自動化儲存和管理 runbook，然後將它們傳遞到一或多個指定的機器。 本文說明如何在 Windows 電腦上部署使用者混合式 Runbook 背景工作角色、如何移除背景工作角色，以及如何移除混合式 Runbook 背景工作角色群組。

在您成功部署 Runbook 背景工作角色後，請檢閱[在混合式 Runbook 背景工作角色上執行 Runbook](automation-hrw-run-runbooks.md)，以了解如何設定 Runbook 以將內部部署資料中心或其他雲端環境中的程序自動化。

## <a name="prerequisites"></a>Prerequisites

開始之前，請確定您具有下列各項。

### <a name="a-log-analytics-workspace"></a>Log Analytics 工作區

混合式 Runbook 背景工作角色相依于 Azure 監視器 Log Analytics 工作區，以安裝和設定角色。 您可以透過 [Azure Resource Manager](../azure-monitor/samples/resource-manager-workspace.md#create-a-log-analytics-workspace)、 [PowerShell](../azure-monitor/scripts/powershell-sample-create-workspace.md?toc=/powershell/module/toc.json)或 [Azure 入口網站](../azure-monitor/learn/quick-create-workspace.md)來建立它。

如果您沒有 Azure 監視器 Log Analytics 工作區，請在建立工作區之前，先參閱 [Azure 監視器記錄檔設計指引](../azure-monitor/platform/design-logs-deployment.md) 。

### <a name="log-analytics-agent"></a>Log Analytics 代理程式

混合式 Runbook 背景工作角色需要適用于支援的 Windows 作業系統的 [Log Analytics 代理程式](../azure-monitor/platform/log-analytics-agent.md) 。 針對裝載于 Azure 外部的伺服器或機器，您可以使用 [已啟用 Azure Arc 的伺服器](../azure-arc/servers/overview.md)來安裝 Log Analytics 代理程式。

### <a name="supported-windows-operating-system"></a>支援的 Windows 作業系統

混合式 Runbook 背景工作角色功能支援下列作業系統：

* Windows Server 2019
* Windows Server 2016 1709 版和 1803 版
* Windows Server 2012、2012 R2
* Windows Server 2008 SP2 (x64)、2008 R2
* Windows 10 企業版 (包括多工作階段) 和專業版
* Windows 8 企業版和專業版
* Windows 7 SP1

### <a name="minimum-requirements"></a>最低要求條件

Windows 系統和使用者混合式 Runbook 背景工作角色的最低需求如下：

* Windows PowerShell 5.1 ([下載 WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616)) 。 不支援 PowerShell Core。
* .NET Framework 4.6.2 或更新版本
* 雙核心
* 4 GB 的 RAM
* 連接埠 443 (輸出)

### <a name="network-configuration"></a>網路組態

如需混合式 Runbook 背景工作角色的網路需求，請參閱設定 [您的網路](automation-hybrid-runbook-worker.md#network-planning)。

### <a name="adding-a-machine-to-a-hybrid-runbook-worker-group"></a>將電腦新增至混合式 Runbook 背景工作角色群組

您可以將背景工作電腦新增至其中一個自動化帳戶中的混合式 Runbook 背景工作角色群組。 針對裝載受更新管理管理之系統混合式 Runbook 背景工作角色的電腦，您可以將它們新增至混合式 Runbook 背景工作角色群組。 但是，您必須針對更新管理和混合式 Runbook 背景工作角色群組成員資格使用相同的自動化帳戶。

>[!NOTE]
>Azure 自動化 [更新管理](./update-management/overview.md) 會自動在啟用更新管理的 azure 或非 azure 電腦上安裝系統混合式 Runbook 背景工作角色。 不過，此背景工作未向您的自動化帳戶中的任何混合式 Runbook 背景工作角色群組註冊。 若要在這些機器上執行您的 runbook，您需要將它們新增至混合式 Runbook 背景工作角色群組。 依照 [手動部署](#manual-deployment) 一節中的步驟6，將它新增至群組。

## <a name="enable-for-management-with-azure-automation-state-configuration"></a>使用 Azure 自動化狀態設定啟用管理

如需啟用電腦以使用 Azure 自動化狀態設定管理的相關資訊，請參閱 [Azure 自動化狀態設定啟用電腦以進行管理](automation-dsc-onboarding.md)。

> [!NOTE]
> 若要使用 Desired State Configuration (DSC) 來管理支援混合式 Runbook 背景工作角色的電腦設定，您必須將電腦新增為 DSC 節點。

## <a name="installation-options"></a>安裝選項

若要安裝和設定 Windows 使用者混合式 Runbook 背景工作角色，您可以使用下列其中一種方法。

* 使用提供的 PowerShell 腳本，將設定一或多部 Windows 電腦的程式完全 [自動化](#automated-deployment) 。 對於資料中心或其他雲端環境中的機器，建議採用此方法。
* 手動匯入自動化解決方案、安裝適用于 Windows 的 Log Analytics 代理程式，以及在電腦上設定背景工作角色。

## <a name="automated-deployment"></a>自動化部署

自動化部署方法會使用 PowerShell 腳本 **New-OnPremiseHybridWorker.ps1** 來自動化和設定 Windows 混合式 Runbook 背景工作角色。 它會執行下列動作：

* 安裝必要的模組
* 使用您的 Azure 帳戶登入
* 確認指定的資源群組和自動化帳戶是否存在
* 建立自動化帳戶屬性的參考
* 建立 Azure 監視器 Log Analytics 工作區（如果未指定）
* 在工作區中啟用 Azure 自動化解決方案
* 下載並安裝適用于 Windows 的 Log Analytics 代理程式
* 將機器註冊為混合式 Runbook 背景工作角色

請執行下列步驟，使用腳本在您的 Windows 電腦上安裝角色。

1. 從 [PowerShell 資源庫](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker)下載 **New-OnPremiseHybridWorker.ps1** 指令碼。 下載腳本之後，請在目的電腦上複製或執行腳本。 **New-OnPremiseHybridWorker.ps1** 腳本會在執行期間使用下列參數。

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

2. 開啟已提高權限的 64 位元 PowerShell 命令提示字元。

3. 從 PowerShell 命令提示字元，流覽至包含您所下載之腳本的資料夾。 變更參數 `AutomationAccountName`、`AAResourceGroupName`、`OMSResourceGroupName`、`HybridGroupName`、`SubscriptionID` 和 `WorkspaceName` 的值。 然後執行指令碼。

    執行指令碼之後，系統會提示您向 Azure 進行驗證。 您必須使用屬於訂用帳戶 **管理員** 角色成員和訂用帳戶共同管理員的帳戶登入。

    ```powershell-interactive
    $NewOnPremiseHybridWorkerParameters = @{
      AutomationAccountName = <nameOfAutomationAccount>
      AAResourceGroupName   = <nameOfResourceGroup>
      OMSResourceGroupName  = <nameOfResourceGroup>
      HybridGroupName       = <nameOfHRWGroup>
      SubscriptionID        = <subscriptionId>
      WorkspaceName         = <nameOfLogAnalyticsWorkspace>
    }
    .\New-OnPremiseHybridWorker.ps1 @NewOnPremiseHybridWorkerParameters
    ```

4. 系統會提示您同意安裝 NuGet，以及使用您的 Azure 認證進行驗證。 如果您沒有最新的 NuGet 版本，您可以從可用的 [Nuget 散發版本](https://www.nuget.org/downloads)下載它。

5. 在腳本完成之後驗證部署。 從自動化帳戶的 [ **混合式 runbook 背景工作角色群組** ] 頁面的 [ **使用者混合式 runbook 背景工作角色群組** ] 索引標籤下，它會顯示新的群組和成員數目。 如果這是現有群組，成員數目就會遞增。 您可以從頁面上的清單中選取群組，從左側功能表中選擇 [混合式背景 **工作角色** ]。 在 [ **混合式背景工作角色** ] 頁面上，您可以看到列出群組的每個成員。

## <a name="manual-deployment"></a>手動部署

若要安裝和設定 Windows 混合式 Runbook 背景工作角色，請執行下列步驟。

1. 在提高許可權的 PowerShell 命令提示字元中執行下列命令，或在 [Azure 入口網站](https://portal.azure.com)的 Cloud Shell 中執行下列命令，以在您的 Log Analytics 工作區中啟用 Azure 自動化的解決方案。

    ```powershell
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <resourceGroupName> -WorkspaceName <workspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true
    ```

2. 將 Log Analytics 代理程式部署到目的電腦。

    * 針對 Azure VM，使用[適用於 Windows 的虛擬機器擴充功能](../virtual-machines/extensions/oms-windows.md)，安裝適用於 Windows 的 Log Analytics 代理程式。 擴充功能會在 Azure 虛擬機器上安裝 Log Analytics 代理程式，並且在現有的 Log Analytics 工作區中註冊虛擬機器。 您可以使用 Azure Resource Manager 範本、PowerShell 或 Azure 原則來指派 [適用于 *Linux* 或 *Windows* vm 內建原則的「部署 Log Analytics 代理程式](../governance/policy/samples/built-in-policies.md#monitoring) 」。 安裝代理程式之後，您可以將電腦新增至自動化帳戶中的混合式 Runbook 背景工作角色群組。
    
    * 針對非 Azure 機器，您可以使用 [已啟用 Azure Arc 的伺服器](../azure-arc/servers/overview.md)來安裝 Log Analytics 代理程式。 啟用 Arc 的伺服器支援使用下列方法來部署 Log Analytics 代理程式：
    
        - 使用 VM 延伸模組架構。
        
            Azure Arc 啟用的伺服器中的這項功能可讓您將 Log Analytics 代理程式 VM 擴充功能部署至非 Azure Windows 和/或 Linux 伺服器。 您可以在混合式電腦上使用下列方法來管理 VM 擴充功能，或在已啟用 Arc 的伺服器上管理伺服器：
        
            - [Azure 入口網站](../azure-arc/servers/manage-vm-extensions-portal.md)
            - [Azure CLI](../azure-arc/servers/manage-vm-extensions-cli.md)
            - [Azure PowerShell](../azure-arc/servers/manage-vm-extensions-powershell.md)
            - Azure [Resource Manager 範本](../azure-arc/servers/manage-vm-extensions-template.md)
        
        - 使用 Azure 原則。
        
            使用這種方法時，您可以使用 Azure 原則將 [Log Analytics 代理程式部署到 Linux 或 Windows Azure Arc 機器](../governance/policy/samples/built-in-policies.md#monitoring) 內建原則，以在啟用 Arc 的伺服器是否已安裝 log analytics 代理程式時進行審核。 如果未安裝代理程式，則會使用補救工作自動進行部署。 或者，如果您打算使用適用於 VM 的 Azure 監視器監視電腦，請改用 [ [啟用適用於 VM 的 Azure 監視器](../governance/policy/samples/built-in-initiatives.md#monitoring) ] 方案來安裝和設定 Log Analytics 代理程式。

    建議您使用 Azure 原則安裝適用于 Windows 或 Linux 的 Log Analytics 代理程式。

3. 確認代理程式已向工作區報告

    適用于 Windows 的 Log Analytics 代理程式會將機器連接到 Azure 監視器的 Log Analytics 工作區。 當您在電腦上安裝代理程式並將它連線到您的工作區時，它會自動下載混合式 Runbook 背景工作角色所需的元件。

    幾分鐘後，當代理程式已成功連線到 Log Analytics 工作區時，您可以執行下列查詢，以驗證代理程式是否正在將活動訊號資料傳送至工作區。

    ```kusto
    Heartbeat 
    | where Category == "Direct Agent"
    | where TimeGenerated > ago(30m)
    ```

    在搜尋結果中，您應該會看到機器的心跳記錄，表示它已連線並向服務回報。 根據預設，每個代理程式會將活動訊號記錄轉送至其獲指派的工作區。 使用下列步驟來完成代理程式安裝和設定。

4. 確認主控 Log Analytics 代理程式之電腦上的混合式 Runbook 背景工作角色版本，流覽 `C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\` 並記下 **版本** 子資料夾。 在工作區中啟用解決方案之後，此資料夾會在電腦上顯示數分鐘。

5. 安裝 runbook 環境並連接至 Azure 自動化。 當您將代理程式設定為向 Log Analytics 工作區報告並匯入 **自動化** 解決方案時，解決方案會向下推送 `HybridRegistration` PowerShell 模組。 此模組包含 `Add-HybridRunbookWorker` Cmdlet。 使用此 Cmdlet 在電腦上安裝 runbook 環境，並向 Azure 自動化註冊。

    以系統管理員模式開啟 PowerShell 工作階段，並執行下列命令來匯入模組。

    ```powershell-interactive
    cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
    Import-Module .\HybridRegistration.psd1
    ```

6. 執行 `Add-HybridRunbookWorker` Cmdlet，指定參數、和的值 `Url` `Key` `GroupName` 。

    ```powershell-interactive
    Add-HybridRunbookWorker –GroupName <String> -Url <Url> -Key <String>
    ```

    您可以 `Url` `Key` 從自動化帳戶中的 [ **金鑰** ] 頁面取得參數所需的資訊。 從頁面左側的 [**帳戶設定**] 區段下，選取 [**金鑰**]。

    ![[管理金鑰] 頁面](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

    * 針對 `Url` 參數，複製 **URL** 的值。

    * 針對 `Key` 參數，複製 **主要存取金鑰** 的值。

    * 針對 `GroupName` 參數，使用「混合式 Runbook 背景工作角色」群組的名稱。 如果此群組已存在於自動化帳戶中，則會將目前的電腦新增至該群組。 如果此群組不存在，則會新增此群組。

    * 如有需要，請設定 `Verbose` 參數，以接收有關安裝的詳細資料。

7. 請在命令完成之後驗證部署。 從自動化帳戶的 [ **混合式 runbook 背景工作角色群組** ] 頁面的 [ **使用者混合式 runbook 背景工作角色群組** ] 索引標籤下，它會顯示新的或現有的群組和成員數目。 如果這是現有群組，成員數目就會遞增。 您可以從頁面上的清單中選取群組，從左側功能表中選擇 [混合式背景 **工作角色**]。 在 [ **混合式背景工作角色** ] 頁面上，您可以看到列出群組的每個成員。

## <a name="install-powershell-modules"></a>安裝 PowerShell 模組

Runbook 可以使用 Azure 自動化環境中安裝的模組中定義的任何活動和 Cmdlet。 因為這些模組不會自動部署至內部部署機器，所以您必須手動安裝它們。 但 Azure 模組例外。 此模組預設為安裝，可讓您存取 Cmdlet 以執行 Azure 自動化的所有 Azure 服務和活動。

因為「混合式 Runbook 背景工作角色」主要是管理本機資源，您很可能需要安裝支援這些資源的模組，特別是 `PowerShellGet` 模組。 如需安裝 Windows PowerShell 模組的相關資訊，請參閱 [Windows PowerShell](/powershell/scripting/developer/windows-powershell)。

安裝的模組必須位於 `PSModulePath` 環境變數所參考的位置，才能由混合式背景工作角色自動匯入。 如需詳細資訊，請參閱[在 PSModulePath 中安裝模組](/powershell/scripting/developer/module/installing-a-powershell-module)。

## <a name="remove-the-hybrid-runbook-worker"></a><a name="remove-windows-hybrid-runbook-worker"></a>移除混合式 Runbook 背景工作角色

1. 在 Azure 入口網站中，前往您的自動化帳戶。

2. 在 [帳戶設定] 下，選取 [金鑰] 並記下 [URL] 和 [主要存取金鑰] 的值。

3. 以系統管理員模式開啟 PowerShell 工作階段，並使用您的 URL 和主要存取金鑰值執行下列命令。 使用 `Verbose` 參數，以取得移除流程的詳細記錄。 若要從混合式背景工作角色群組中移除過時的機器，請使用選擇性的 `machineName` 參數。

```powershell-interactive
Remove-HybridRunbookWorker -Url <URL> -Key <primaryAccessKey> -MachineName <computerName>
```

## <a name="remove-a-hybrid-worker-group"></a>移除混合式背景工作角色群組

若要移除混合式 Runbook 背景工作角色群組，您必須先從屬於群組成員的每一部電腦移除混合式 Runbook 背景工作角色。 然後，使用下列步驟來移除群組：

1. 在 Azure 入口網站中，開啟自動化帳戶。

2. 在 [程序自動化] 下，選取 [混合式背景工作角色群組]。 選取您要刪除的群組。 該群組的屬性頁面隨即出現。

   ![屬性頁面](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

3. 在所選群組的屬性頁面上，選取 [刪除]。 這會顯示訊息要求您確認此動作。 如果您確定要繼續，請選取 [是]。

   ![確認訊息](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   此程序可能需要數秒鐘的時間完成。 您可以在功能表的 [通知] 底下追蹤其進度。

## <a name="next-steps"></a>後續步驟

* 若要了解如何設定您的 Runbook，將您在內部部署資料中心或其他雲端環境中的程序自動化，請參閱[在混合式 Runbook 背景工作角色上執行 Runbook](automation-hrw-run-runbooks.md)。

* 若要了解如何對混合式 Runbook 背景工作角色進行疑難排解，請參閱[對混合式 Runbook 背景工作角色問題進行疑難排解](troubleshoot/hybrid-runbook-worker.md#general)。