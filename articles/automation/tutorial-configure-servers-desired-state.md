---
title: 使用 Azure 自動化將伺服器設定為所需狀態並管理漂移
description: 教學課程 - 使用 Azure Automation State Configuration 管理伺服器組態
services: automation
ms.subservice: dsc
ms.topic: conceptual
ms.date: 08/08/2018
ms.openlocfilehash: a02c664ddf0802ad5ac306f98de14b7c0d5d7271
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81678699"
---
# <a name="configure-servers-to-a-desired-state-and-manage-drift"></a>將伺服器設定為預期狀態並管理漂移

Azure Automation State Configuration 可讓您指定伺服器的組態，並且確定這些伺服器處於指定狀態一段時間。

> [!div class="checklist"]
> - 上架 VM 讓 Azure 自動化 DSC 管理
> - 將設定上傳至 Azure 自動化
> - 將設定編譯成節點設定
> - 將節點設定指派給受控節點
> - 檢查受控節點的合規性狀態

在本教學課程中，我們會使用簡單的[DSC](/powershell/scripting/dsc/configurations/configurations)設定，以確保 IIS 已安裝在 VM 上。

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 如需有關混合式 Runbook 背景工作角色的 Az 模組安裝指示，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 針對您的自動化帳戶，您可以使用[如何更新 Azure 自動化中的 Azure PowerShell 模組](automation-update-azure-modules.md)，將模組更新為最新版本。

## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程，您需要：

- Azure 自動化帳戶。 如需建立 Azure 自動化執行身分帳戶的指示，請參閱 [Azure 執行身分帳戶](automation-sec-configure-azure-runas-account.md)。
- 執行 Windows Server 2008 R2 或更新版本的 Azure Resource Manager VM （非傳統）。 如需建立 VM 的指示，請參閱[在 Azure 入口網站中建立您的第一個 Windows 虛擬機器](../virtual-machines/virtual-machines-windows-hero-tutorial.md)。
- Azure PowerShell 模組 3.6 版或更新版本。 執行 `Get-Module -ListAvailable Az` 以尋找版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/azurerm/install-azurerm-ps)。
- 熟悉 Desired State Configuration (DSC)。 如需 DSC 的詳細資訊，請參閱[Windows PowerShell Desired State Configuration 總覽](/powershell/scripting/dsc/overview/overview)。

## <a name="support-for-partial-configurations"></a>部分設定的支援

Azure 自動化狀態設定支援使用[部分](/powershell/scripting/dsc/pull-server/partialconfigs)設定。 在此案例中，DSC 會設定為獨立管理多個設定，並從 Azure 自動化抓取每個設定。 不過，每個自動化帳戶只能指派一個設定給一個節點。 這表示如果您在節點上使用兩個設定，您將需要兩個自動化帳戶。

如需如何從提取服務註冊部分設定的詳細資訊，請參閱[部分](https://docs.microsoft.com/powershell/scripting/dsc/pull-server/partialconfigs#partial-configurations-in-pull-mode)設定的檔。

如需有關小組如何搭配使用設定即程式碼，共同管理伺服器的詳細資訊，請參閱[瞭解 DSC 在 CI/CD 管線中的角色](/powershell/scripting/dsc/overview/authoringadvanced)。

## <a name="log-in-to-azure"></a>登入 Azure

使用[disconnect-azaccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0) Cmdlet 登入您的 Azure 訂用帳戶，並遵循畫面上的指示進行。

```powershell
Connect-AzAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>建立設定並將其上傳至 Azure 自動化


在文字編輯器中，輸入下列內容，並將它儲存在本機做為**TestConfig**。

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
> 在需要匯入多個模組以提供 DSC 資源的更先進案例中，請確定每個模組在您`Import-DscResource`的設定中都有唯一的行。

呼叫[AzAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/Az.Automation/Import-AzAutomationDscConfiguration?view=azps-3.7.0) Cmdlet，將設定上傳至您的自動化帳戶。

```powershell
 Import-AzAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>將設定編譯成節點設定

DSC 設定必須編譯成節點設定，才可以指派至節點。 請參閱 [DSC 設定](/powershell/scripting/dsc/configurations/configurations)。

呼叫[AzAutomationDscCompilationJob](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationDscCompilationJob?view=azps-3.7.0) Cmdlet，將設定編譯`TestConfig`成您的自動化帳戶中名為`TestConfig.WebServer`的節點設定。

```powershell
Start-AzAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

## <a name="register-a-vm-to-be-managed-by-state-configuration"></a>註冊 VM 以供 State Configuration 管理

您可以使用 Azure Automation State Configuration 來管理 Azure VM (傳統和 Resource Manager)、內部部署 VM、Linux 機器、AWS VM 和內部部署實體機器。 在本主題中，我們將討論如何只註冊 Azure Resource Manager VM。 如需將其他類型的機器註冊的詳細資訊，請參閱 [將機器上架以供 Azure Automation State Configuration 管理](automation-dsc-onboarding.md)。

呼叫[AzAutomationDscNode](https://docs.microsoft.com/powershell/module/Az.Automation/Register-AzAutomationDscNode?view=azps-3.7.0) Cmdlet，將您的 VM 註冊為受管理節點的 Azure 自動化狀態設定。 

```powershell
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm'
```

### <a name="specify-configuration-mode-settings"></a>指定設定模式設定

使用[AzAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode) CMDLET 將 VM 註冊為受管理的節點，並指定設定屬性。 例如，您可以將電腦的狀態指定為`ApplyOnly` `ConfigurationMode`屬性的值，只套用一次。 狀態設定不會在初始檢查之後嘗試套用設定。

```powershell
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationMode 'ApplyOnly'
```

您也可以使用`ConfigurationModeFrequencyMins`屬性，指定 DSC 檢查設定狀態的頻率。 如需有關 DSC 設定的詳細資訊，請參閱[設定本機設定管理員](/powershell/scripting/dsc/managing-nodes/metaConfig)。

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

這會將名`TestConfig.WebServer`為的節點設定指派給已`DscVm`註冊的 DSC 節點。 根據預設，DSC 節點會每隔 30 分鐘檢查節點設定的合規性。 如需如何變更合規性檢查間隔的詳細資訊，請參閱[設定本機組態管理員](/powershell/scripting/dsc/managing-nodes/metaConfig)。

## <a name="check-the-compliance-status-of-a-managed-node"></a>檢查受控節點的合規性狀態

您可以使用[AzAutomationDscNodeReport](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationDscNodeReport?view=azps-3.7.0) Cmdlet 來取得受管理節點的相容性狀態報表。

```powershell
# Get the ID of the DSC node
$node = Get-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeId $node.Id

# Display the most recent report
$reports[0]
```

## <a name="remove-nodes-from-service"></a>從服務移除節點

當您將節點新增至 Azure 自動化狀態設定時，本機 Configuration Manager 中的設定會設為 [向服務註冊] 和 [提取設定] 和 [所需的模組] 來設定電腦。
如果您選擇從服務中移除節點，您可以使用 Azure 入口網站或 Az Cmdlet 來執行此動作。

> [!NOTE]
> 從服務取消註冊節點時，只會設定本機 Configuration Manager 設定，讓節點不再連接至服務。
> 這不會影響目前套用至節點的設定。
> 若要移除目前的設定，請使用[PowerShell](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/remove-dscconfigurationdocument?view=powershell-5.1)或刪除本機設定檔案（這是 Linux 節點的唯一選項）。

### <a name="azure-portal"></a>Azure 入口網站

從 Azure 自動化按一下目錄中的 [**狀態設定（DSC）** ]。
接下來按一下 [**節點**]，以查看已向服務註冊的節點清單。
按一下您要移除之節點的名稱。
在開啟的節點視圖中，按一下 [**取消註冊**]。

### <a name="powershell"></a>PowerShell

若要使用 PowerShell 從 Azure 自動化狀態設定服務取消註冊節點，請遵循 Cmdlet [AzAutomationDscNode](https://docs.microsoft.com/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-2.0.0)的檔。

## <a name="next-steps"></a>後續步驟

- 若要開始使用，請參閱[Azure 自動化狀態設定](automation-dsc-getting-started.md)使用者入門。
- 若要瞭解如何將節點上架，請參閱將[機器上架以進行管理，方法是 Azure 自動化狀態設定](automation-dsc-onboarding.md)。
- 若要瞭解如何編譯 DSC 設定，讓您可以將它們指派給目標節點，請參閱[在 Azure 自動化狀態設定中編譯](automation-dsc-compile.md)設定。
- 如需 PowerShell Cmdlet 參考，請參閱[Azure 自動化狀態設定 Cmdlet](/powershell/module/azurerm.automation/#automation)。
- 如需定價資訊，請參閱[Azure 自動化狀態設定定價](https://azure.microsoft.com/pricing/details/automation/)。
- 若要查看在持續部署管線中使用 Azure 自動化狀態設定的範例，請參閱[使用 Azure 自動化狀態設定和 Chocolatey 的持續部署](automation-dsc-cd-chocolatey.md)
