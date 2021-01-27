---
title: 在 Azure 自動化中將電腦設定為預期狀態
description: 本文說明如何使用 Azure 自動化狀態設定，將電腦設定為預期狀態。
services: automation
ms.subservice: dsc
ms.topic: conceptual
ms.date: 08/08/2018
ms.openlocfilehash: 2e09607dde118ce25e5d2e5311e7614f2f18a590
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98890725"
---
# <a name="configure-machines-to-a-desired-state"></a>將機器設定為預期狀態

Azure Automation State Configuration 可讓您指定伺服器的組態，並且確定這些伺服器處於指定狀態一段時間。

> [!div class="checklist"]
> - 上架 VM 讓 Azure 自動化 DSC 管理
> - 將設定上傳至 Azure 自動化
> - 將設定編譯成節點設定
> - 將節點設定指派給受控節點
> - 檢查受控節點的合規性狀態

針對此教學課程，我們會使用簡單的 [DSC 設定](/powershell/scripting/dsc/configurations/configurations)，以確保在 VM 上安裝 IIS。

## <a name="prerequisites"></a>Prerequisites

- Azure 自動化帳戶。 如需建立 Azure 自動化執行身分帳戶的指示，請參閱 [Azure 執行身分帳戶](./manage-runas-account.md)。
- 執行 Windows Server 2008 R2 或更新版本的 Azure Resource Manager VM (非傳統)。 如需建立 VM 的指示，請參閱[在 Azure 入口網站中建立第一個 Windows 虛擬機器](../virtual-machines/windows/quick-create-portal.md)。
- Azure PowerShell 模組 3.6 版或更新版本。 執行 `Get-Module -ListAvailable Az` 以尋找版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/azurerm/install-azurerm-ps)。
- 熟悉 Desired State Configuration (DSC)。 如需 DSC 的資訊，請參閱 [Windows PowerShell 預期狀態設定概觀](/powershell/scripting/dsc/overview/overview)。

## <a name="support-for-partial-configurations"></a>部分設定的支援

Azure 自動化狀態設定支援使用[部分設定](/powershell/scripting/dsc/pull-server/partialconfigs)。 在此案例中，DSC 會設定為獨立管理多項設定，並從 Azure 自動化擷取各項設定。 不過，每個自動化帳戶只能指派一項設定給一個節點。 這表示如果在節點上使用兩項設定，您將需要兩個自動化帳戶。

如需如何從提取服務註冊部分設定的詳細資料，請參閱[部分設定](/powershell/scripting/dsc/pull-server/partialconfigs#partial-configurations-in-pull-mode)的文件。

如需小組如何將設定作為程式碼使用，以共同管理伺服器的詳細資訊，請參閱[了解 DSC 在 CI/CD 管線中的角色](/powershell/scripting/dsc/overview/authoringadvanced)。

## <a name="log-in-to-azure"></a>登入 Azure

使用 [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) Cmdlet 登入 Azure 訂用帳戶，並遵循畫面上的指示操作。

```powershell
Connect-AzAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>建立設定並將其上傳至 Azure 自動化


在文字編輯器中輸入下列項目，並將其於本機儲存為 **TestConfig.ps1**。

```powershell
configuration TestConfig {
   Node WebServer {
      WindowsFeature IIS {
         Ensure               = 'Present'
         Name                 = 'Web-Server'
         IncludeAllSubFeature = $true
      }
   }
}
```

> [!NOTE]
> 在需要匯入多個模組以提供 DSC 資源的更進階案例中，請確定每個模組在設定中都有唯一的 `Import-DscResource` 行。

呼叫 [Import-AzAutomationDscConfiguration](/powershell/module/Az.Automation/Import-AzAutomationDscConfiguration) Cmdlet，以將設定上傳至自動化帳戶中。

```powershell
 Import-AzAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>將設定編譯成節點設定

DSC 設定必須編譯成節點設定，才可以指派至節點。 請參閱 [DSC 設定](/powershell/scripting/dsc/configurations/configurations)。

呼叫 [Start-AzAutomationDscCompilationJob](/powershell/module/Az.Automation/Start-AzAutomationDscCompilationJob) Cmdlet，以將 `TestConfig` 設定編譯成自動化帳戶中名為 `TestConfig.WebServer` 的節點設定。

```powershell
Start-AzAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

## <a name="register-a-vm-to-be-managed-by-state-configuration"></a>註冊 VM 以供 State Configuration 管理

您可以使用 Azure Automation State Configuration 來管理 Azure VM (傳統和 Resource Manager)、內部部署 VM、Linux 機器、AWS VM 和內部部署實體機器。 在本主題中，我們將討論如何只註冊 Azure Resource Manager VM。 如需將其他類型的機器註冊的詳細資訊，請參閱 [將機器上架以供 Azure Automation State Configuration 管理](automation-dsc-onboarding.md)。

呼叫 [Register-AzAutomationDscNode](/powershell/module/Az.Automation/Register-AzAutomationDscNode) Cmdlet，以使用 Azure 自動化狀態設定作為受控節點來註冊 VM。 

```powershell
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm'
```

### <a name="specify-configuration-mode-settings"></a>指定設定模式設定

使用 [Register-AzAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode) Cmdlet，以將 VM 註冊為受控節點，並指定設定屬性。 例如，您可藉由指定 `ApplyOnly` 作為 `ConfigurationMode` 屬性的值，以指定讓電腦的狀態只套用一次。 狀態設定不會在初始檢查之後嘗試套用設定。

```powershell
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationMode 'ApplyOnly'
```

您也可以藉由使用 `ConfigurationModeFrequencyMins` 屬性，以指定 DSC 檢查設定狀態的頻率。 如需有關 DSC 設定的詳細資訊，請參閱[設定本機設定管理員](/powershell/scripting/dsc/managing-nodes/metaConfig)。

```powershell
# Run a DSC check every 60 minutes
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationModeFrequencyMins 60
```

## <a name="assign-a-node-configuration-to-a-managed-node"></a>將節點設定指派給受控節點

現在我們可以將已編譯的節點設定指派給我們想要設定的 VM。

```powershell
# Get the ID of the DSC node
$node = Get-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -NodeId $node.Id
```

此動作會將名為 `TestConfig.WebServer` 的節點設定，指派至已註冊的 DSC 節點 `DscVm`。 根據預設，DSC 節點會每隔 30 分鐘檢查節點設定的合規性。 如需如何變更合規性檢查間隔的詳細資訊，請參閱[設定本機組態管理員](/powershell/scripting/dsc/managing-nodes/metaConfig)。

## <a name="check-the-compliance-status-of-a-managed-node"></a>檢查受控節點的合規性狀態

您可使用 [Get-AzAutomationDscNodeReport](/powershell/module/Az.Automation/Get-AzAutomationDscNodeReport) Cmdlet，以取得受控節點合規性狀態的報告。

```powershell
# Get the ID of the DSC node
$node = Get-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeId $node.Id

# Display the most recent report
$reports[0]
```

## <a name="remove-nodes-from-service"></a>從服務中移除節點

當將節點新增至 Azure 自動化狀態設定時，本機設定管理員中的設定會設為向服務註冊、提取設定以及所需的模組來設定電腦。
如果想要從服務中移除節點，您可使用 Azure 入口網站或 Az Cmdlet 來執行此動作。

> [!NOTE]
> 從服務取消註冊節點時，只會設定本機設定管理員設定，讓節點不再連線至服務。
> 這不會影響目前套用至節點的設定。
> 若要移除目前的設定，請使用 [PowerShell](/powershell/module/psdesiredstateconfiguration/remove-dscconfigurationdocument) 或刪除本機設定檔 (此為 Linux 節點的唯一選項)。

### <a name="azure-portal"></a>Azure 入口網站

從 Azure 自動化中，按一下目錄中的 [狀態設定 (DSC)]。
接下來，按一下 [節點] 以檢視向服務註冊的節點清單。
按一下想要移除的節點名稱。
在開啟的節點檢視中，按一下 [取消註冊]。

### <a name="powershell"></a>PowerShell

若要使用 PowerShell 從 Azure 自動化狀態設定服務取消註冊節點，請參閱下列文件中的 [Unregister-AzAutomationDscNode](/powershell/module/az.automation/unregister-azautomationdscnode) Cmdlet。

## <a name="next-steps"></a>後續步驟

- 若要開始使用，請參閱[開始使用 Azure 自動化狀態設定](automation-dsc-getting-started.md)。
- 若要了解如何啟用節點，請參閱[啟用 Azure 自動化狀態設定](automation-dsc-onboarding.md)。
- 若要了解如何編譯 DSC 設定，以便可將其指派給目標節點，請參閱[編譯 Azure 自動化狀態設定中的 DSC 設定](automation-dsc-compile.md)。
- 如需在持續部署管道中使用 Azure 自動化狀態設定的範例，請參閱[使用 Chocolatey 設定持續部署](automation-dsc-cd-chocolatey.md)。
- 如需定價資訊，請參閱 [Azure 自動化狀態設定定價](https://azure.microsoft.com/pricing/details/automation/)。
- 如需 PowerShell Cmdlet 參考，請參閱 [Az.Automation](/powershell/module/az.automation)。
