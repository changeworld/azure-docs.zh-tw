---
title: 使用 Azure 自動化將伺服器設定為所需狀態並管理漂移
description: 教學課程 - 使用 Azure Automation State Configuration 管理伺服器組態
services: automation
ms.subservice: dsc
ms.topic: conceptual
ms.date: 08/08/2018
ms.openlocfilehash: a02c664ddf0802ad5ac306f98de14b7c0d5d7271
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
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

在本教程中,我們使用一個簡單的[DSC 配置](/powershell/scripting/dsc/configurations/configurations),以確保在 VM 上安裝 IIS。

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有關混合 Runbook 輔助角色上的 Az 模組安裝說明,請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 對於自動化帳戶,可以使用[「如何更新 Azure 自動化 中的 Azure PowerShell」模組](automation-update-azure-modules.md)將模組更新到最新版本。

## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程，您需要：

- Azure 自動化帳戶。 如需建立 Azure 自動化執行身分帳戶的指示，請參閱 [Azure 執行身分帳戶](automation-sec-configure-azure-runas-account.md)。
- 運行 Windows Server 2008 R2 或更高版本的 Azure 資源管理器 VM(不是經典)。 有關建立 VM 的說明,請參閱[在 Azure 門戶中建立第一個 Windows 虛擬機器](../virtual-machines/virtual-machines-windows-hero-tutorial.md)。
- Azure PowerShell 模組 3.6 版或更新版本。 執行 `Get-Module -ListAvailable Az` 以尋找版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/azurerm/install-azurerm-ps)。
- 熟悉 Desired State Configuration (DSC)。 有關 DSC 的資訊,請參閱[Windows 電源外殼所需的狀態配置概述](/powershell/scripting/dsc/overview/overview)。

## <a name="support-for-partial-configurations"></a>支援部分設定

Azure 自動化狀態設定支援使用[部分設定](/powershell/scripting/dsc/pull-server/partialconfigs)。 在這種情況下,DSC 配置為獨立管理多個配置,並從 Azure 自動化檢索每個配置。 但是,每個自動化帳戶只能向節點分配一個配置。 這意味著,如果您對一個節點使用兩個配置,則需要兩個自動化帳戶。

有關如何從拉取服務註冊部分設定的詳細資訊,請參閱[部分設定的文件](https://docs.microsoft.com/powershell/scripting/dsc/pull-server/partialconfigs#partial-configurations-in-pull-mode)。

有關團隊如何協同協作以使用設定作為代碼協作管理伺服器的詳細資訊,請參閱[瞭解 DSC 在 CI/CD 管道中的角色](/powershell/scripting/dsc/overview/authoringadvanced)。

## <a name="log-in-to-azure"></a>登入 Azure

使用[Connect-AzAccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0) cmdlet 登入到 Azure 訂閱,並按照螢幕上的說明進行操作。

```powershell
Connect-AzAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>建立設定並將其上傳至 Azure 自動化


在文字編輯器中,鍵入以下內容並將本地端儲存為**TestConfig.ps1**。

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
> 在需要導入多個提供 DSC 資源的模組的更高級方案中,請確保每個模組在配置中`Import-DscResource`具有唯一的行。

調用[導入-阿茲自動化Dsc配置](https://docs.microsoft.com/powershell/module/Az.Automation/Import-AzAutomationDscConfiguration?view=azps-3.7.0)cmdlet 將配置上載到自動化帳戶。

```powershell
 Import-AzAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>將設定編譯成節點設定

DSC 設定必須編譯成節點設定，才可以指派至節點。 請參考[DSC 設定](/powershell/scripting/dsc/configurations/configurations)。

調用[啟動-AzAutomationD編譯作業](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationDscCompilationJob?view=azps-3.7.0)cmdlet,將`TestConfig`配置編譯為自動化帳戶中`TestConfig.WebServer`指定的 節點配置。

```powershell
Start-AzAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

## <a name="register-a-vm-to-be-managed-by-state-configuration"></a>註冊 VM 以供 State Configuration 管理

您可以使用 Azure Automation State Configuration 來管理 Azure VM (傳統和 Resource Manager)、內部部署 VM、Linux 機器、AWS VM 和內部部署實體機器。 在本主題中，我們將討論如何只註冊 Azure Resource Manager VM。 如需將其他類型的機器註冊的詳細資訊，請參閱 [將機器上架以供 Azure Automation State Configuration 管理](automation-dsc-onboarding.md)。

調用[註冊-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/Az.Automation/Register-AzAutomationDscNode?view=azps-3.7.0) cmdlet 將 VM 註冊到 Azure 自動化狀態配置作為託管節點。 

```powershell
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm'
```

### <a name="specify-configuration-mode-settings"></a>指定設定模式設定

使用[註冊-AzAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode) cmdlet 將 VM 註冊為託管節點並指定配置屬性。 例如,可以通過`ApplyOnly`指定`ConfigurationMode`為 屬性的值來指定計算機的狀態僅應用一次。 狀態配置不會嘗試在初始檢查后應用配置。

```powershell
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationMode 'ApplyOnly'
```

還可以指定 DSC`ConfigurationModeFrequencyMins`使用 屬性 檢查配置狀態的頻率。 如需有關 DSC 設定的詳細資訊，請參閱[設定本機設定管理員](/powershell/scripting/dsc/managing-nodes/metaConfig)。

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

這會指定節點`TestConfig.WebServer`設定到註冊的 DSC 節點`DscVm`。 根據預設，DSC 節點會每隔 30 分鐘檢查節點設定的合規性。 如需如何變更合規性檢查間隔的詳細資訊，請參閱[設定本機組態管理員](/powershell/scripting/dsc/managing-nodes/metaConfig)。

## <a name="check-the-compliance-status-of-a-managed-node"></a>檢查受控節點的合規性狀態

您可以使用[Get-AzAutomationDscNode 報告](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationDscNodeReport?view=azps-3.7.0)cmdlet 獲取有關託管節點的合規性狀態的報告。

```powershell
# Get the ID of the DSC node
$node = Get-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeId $node.Id

# Display the most recent report
$reports[0]
```

## <a name="remove-nodes-from-service"></a>從服務中移除節點

將節點添加到 Azure 自動化狀態設定時,本地配置管理員中的設置設定為向服務註冊,並提取配置以及設定電腦所需的模組。
如果選擇從服務中刪除節點,則可以使用 Azure 門戶或 Az cmdlet 執行此操作。

> [!NOTE]
> 從服務中取消註冊節點只會設置本地配置管理員設置,以便節點不再連接到服務。
> 這不會影響當前應用於節點的配置。
> 要刪除當前設定,請使用[PowerShell](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/remove-dscconfigurationdocument?view=powershell-5.1)或刪除本地設定檔(這是 Linux 節點的唯一選項)。

### <a name="azure-portal"></a>Azure 入口網站

從 Azure 自動化中,單擊目錄中**的狀態配置 (DSC)。**
接下來按下 **「節點」** 以查看向服務註冊的節點清單。
按下要刪除的節點的名稱。
在打開的「節點」檢視中,按一下 **「取消註冊**」 。。

### <a name="powershell"></a>PowerShell

要使用 PowerShell 從 Azure 自動化狀態配置服務中取消註冊節點,請按照 cmdlet[取消註冊-AzAutomationDnode 的文件](https://docs.microsoft.com/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-2.0.0)進行操作。

## <a name="next-steps"></a>後續步驟

- 要開始,請參閱[開始使用 Azure 自動化狀態設定](automation-dsc-getting-started.md)。
- 要瞭解如何載入節點,請參閱[載入電腦來進行 Azure 自動化狀態設定進行管理](automation-dsc-onboarding.md)。
- 要瞭解如何編譯 DSC 設定以便將它們分配給目標節點,請參閱[在 Azure 自動化狀態設定中編譯設定](automation-dsc-compile.md)。
- 有關 PowerShell cmdlet 引用,請參閱[Azure 自動化狀態設定 cmdlet](/powershell/module/azurerm.automation/#automation)。
- 有關定價資訊,請參閱[Azure 自動化狀態設定定價](https://azure.microsoft.com/pricing/details/automation/)。
- 若要查看在持續部署管線中使用 Azure 自動化狀態設定的範例，請參閱[使用 Azure 自動化狀態設定和 Chocolatey 的持續部署](automation-dsc-cd-chocolatey.md)
