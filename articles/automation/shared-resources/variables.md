---
title: 在 Azure 自動化中管理變數
description: 變數資產是可用於 Azure 自動化中所有 Runbook 和 DSC 設定的值。  這篇文章說明變數的詳細資料，以及如何以文字式和圖形化編寫形式加以使用。
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 05/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4778e9b2c0d3b442b214966ab69810d2f42b70b8
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732753"
---
# <a name="manage-variables-in-azure-automation"></a>在 Azure 自動化中管理變數

可變資產是可用於自動化帳戶中的所有 Runbook 和 DSC 配置的值。 可以從 Azure 門戶、PowerShell、Runbook 或 DSC 配置中管理它們。

自動化變數對下列案例很實用：

- 在多個 Runbook 或 DSC 配置之間共用值。

- 在同一 Runbook 或 DSC 配置中的多個作業之間共用值。

- 從門戶或 PowerShell 命令列管理 Runbook 或 DSC 配置使用的值。 例如一組常見的配置項,例如 VM 名稱的特定清單、特定資源組、AD 功能變數名稱等。  

Azure 自動化會保留變數,即使 Runbook 或 DSC 配置失敗,它們也能可用。 此行為允許一個 Runbook 或 DSC 設定設置一個值,然後由另一個 Runbook 或下次執行時由同一 Runbook 或 DSC 配置使用。

Azure 自動化安全地存儲每個加密變數。 創建變數時,可以將 Azure 自動化的加密和存儲指定為安全資產。 其他安全資產包括憑據、證書和連接。 Azure 自動化對這些資產進行加密,並使用為每個自動化帳戶生成的唯一密鑰存儲它們。 金鑰存儲在系統管理的關鍵保管庫中。 在存儲安全資產之前,Azure自動化會從密鑰保管庫載入密鑰,然後使用它對資產進行加密。 

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有關混合 Runbook 輔助角色上的 Az 模組安裝說明,請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 對於自動化帳戶,可以使用[「如何更新 Azure 自動化 中的 Azure PowerShell」模組](../automation-update-azure-modules.md)將模組更新到最新版本。

## <a name="variable-types"></a>變數型別

使用 Azure 門戶建立變數時,必須從下拉清單中指定數據類型,以便門戶可以顯示用於輸入變數值的適當控制項。 以下是 Azure 自動化中可用的變數類型:

* String
* 整數
* Datetime
* Boolean
* Null

該變數不局限於指定的數據類型。 如果要指定不同類型的值,則必須使用 Windows PowerShell 設置變數。 如果指示`Not defined`,變數的值將設置為 Null。 您必須使用[Set-AzAutomation Variable](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0) cmdlet 或`Set-AutomationVariable`活動設定值。

不能使用 Azure 門戶創建或更改複雜變數類型的值。 但是,您可以使用 Windows PowerShell 提供任何類型的值。 複雜類型作為[PSCustomObject](/dotnet/api/system.management.automation.pscustomobject)檢索。

您可以藉由建立陣列或雜湊表，並將它儲存到變數中，來儲存多個值到單一變數。

>[!NOTE]
>VM 名稱變數最多可以為 80 個字元。 資源組變數最多可以為 90 個字元。 請參考[Azure 資源的命名規則和限制](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-name-rules)。

## <a name="powershell-cmdlets-that-create-and-manage-variable-assets"></a>建立及管理可變資產的 PowerShell cmdlet

對於 Az 模組,下表中的 cmdlet 用於使用 Windows PowerShell 創建和管理自動化變數資產。 它們作為[Az.自動化模組](/powershell/azure/overview)的一部分發貨,可用於自動化手冊和 DSC 配置。

| Cmdlet | 描述 |
|:---|:---|
|[取得-阿茲自動化變數](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0) | 擷取現有變數的值。 不能使用此 cmdlet 檢索加密變數的值。 執行此操作的唯一方法是在 Runbook`Get-AutomationVariable`或 DSC 配置中使用活動。 |
|[新-阿茲自動化變數](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationvariable?view=azps-3.5.0) | 建立新的變數並設定其值。|
|[刪除-阿茲自動化變數](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationvariable?view=azps-3.5.0)| 移除現有的變數。|
|[設定-Az自動化變數](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0)| 設定現有的變數的值。 |

## <a name="activities-to-access-variables-in-runbooks-and-dsc-configurations"></a>存取 Runbook 與 DSC 設定中的變數

下表中的活動用於存取 Runbook 和 DSC 設定中的變數。 這些活動的cmdlet隨全域模組`Orchestrator.AssetManagement.Cmdlets`一起提供。

| 活動 | 描述 |
|:---|:---|
|`Get-AutomationVariable`|擷取現有變數的值。|
|`Set-AutomationVariable`|設定現有的變數的值。|

> [!NOTE]
> 避免在`Name`runbook`Get-AutomationVariable`或 DSC 配置中使用 參數中的變數。 使用此參數會使在設計時 Runbook 或 DSC 配置和自動化變數之間的依賴項發現複雜化。

請注意,`Get-AutomationVariable`在 PowerShell 中不起作用,但僅在 Runbook 或 DSC 配置中工作。 例如,要檢視加密變數的值,您可以建立 Runbook 來取得該變數,然後將其寫入輸出流:
 
```powershell
$mytestencryptvar = Get-AutomationVariable -Name TestVariable
Write-output "The encrypted value of the variable is: $mytestencryptvar"
```

## <a name="functions-to-access-variables-in-python-2-runbooks"></a>存取 Python 2 Runbook 中的變數的函數

下表中的函數用於造訪 Python 2 Runbook 中的變數。

|Python 2 功能|描述|
|:---|:---|
|`automationassets.get_automation_variable`|擷取現有變數的值。 |
|`automationassets.set_automation_variable`|設定現有的變數的值。 |

> [!NOTE]
> 您必須在`automationassets`Python runbook 頂部導入模組才能存取資產函數。

## <a name="working-with-automation-variables"></a>使用自動化變數

>[!NOTE]
>如果要刪除變數的加密,則必須刪除該變數並將其重新創建為未加密的變數。

### <a name="create-a-new-variable-using-the-azure-portal"></a>使用 Azure 門戶建立新變數

1. 從自動化帳戶中,按一下 **「資產**」磁貼,然後單擊 **「資產**」邊欄選項卡,然後選擇 **「變數**」。
2. 在 [變數]**** 圖格上，選取 [新增變數]****。
3. 完成 **"新建變數"** 邊欄選項卡上的選項,然後按下「**建立」** 以儲存新變數。

> [!NOTE]
> 保存加密變數后,無法在門戶中查看該變數。 它只能更新。

### <a name="create-and-use-a-variable-in-windows-powershell"></a>在 Windows PowerShell 建立及使用變數

PowerShell 文稿`New-AzAutomationVariable`使用 cmdlet 或其 AzureRM 模組等效項創建新變數並設置其初始值。 如果變數已加密,則呼叫應使用 參數`Encrypted`。

該腳本使用[Get-AzAutomationvariable](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0)檢索變數的值。 如果值是一個簡單的類型,cmdlet 將檢索同一類型。 如果是複雜類型,`PSCustomObject`則檢索類型。

>[!NOTE]
>PowerShell 腳本無法檢索加密的值。 執行此操作的唯一`Get-AutomationVariable`方法是在 Runbook 或 DSC 配置中使用活動。

下面的範例展示如何創建String類型的變數,然後返回其值。

```powershell
New-AzAutomationVariable -ResourceGroupName "ResourceGroup01" 
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
–Encrypted $false –Value 'My String'
$string = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value
```

下面的範例展示如何建立具有複雜類型的變數,然後檢索其屬性。 在這種情況下,將使用[Get-AzVM 中的](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM?view=azps-3.5.0)虛擬機器物件。

```powershell
$vm = Get-AzVM -ResourceGroupName "ResourceGroup01" –Name "VM01"
New-AzAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm

$vmValue = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
$vmName = $vmValue.Name
$vmIpAddress = $vmValue.IpAddress
```

### <a name="create-and-use-a-variable-in-a-runbook-or-dsc-configuration"></a>在 Runbook 或 DSC 設定建立與使用變數

在 Runbook 或 DSC 配置中創建新變數的唯一`New-AzAutomationVariable`方法是使用 cmdlet 或其 AzureRM 模組等效項。 文稿使用此 cmdlet 設定變數的初始值。 然後,文稿可以使用 檢索`Get-AzAutomationVariable`值 。 如果值是一個簡單的類型,則檢索相同的類型。 如果是複雜類型,則檢索`PSCustomObject`類型。

>[!NOTE]
>檢索加密值的唯一方法是使用 Runbook`Get-AutomationVariable`或 DSC 配置中的活動。 

### <a name="textual-runbook-samples"></a>文字式 Runbook 範例

#### <a name="set-and-retrieve-a-simple-value-from-a-variable"></a>從變數設定與檢索簡單值

下列範例命令顯示如何設定及擷取文字式 Runbook 中的變數。 此範例假定建立名為`NumberOfIterations`和的整數變數和`NumberOfRunnings`名為的`SampleMessage`字串變數。

```powershell
$NumberOfIterations = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfIterations'
$NumberOfRunnings = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfRunnings'
$SampleMessage = Get-AutomationVariable -Name 'SampleMessage'

Write-Output "Runbook has been run $NumberOfRunnings times."

for ($i = 1; $i -le $NumberOfIterations; $i++) {
    Write-Output "$i`: $SampleMessage"
}
Set-AzAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" –Name NumberOfRunnings –Value ($NumberOfRunnings += 1)
```

#### <a name="set-and-retrieve-a-variable-in-a-python-2-runbook"></a>在 Python 2 執行簿中設定與檢索變數

下面的範例展示如何使用變數、設置變數和處理 Python 2 Runbook 中不存在的變數的異常。

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a variable
value = automationassets.get_automation_variable("test-variable")
print value

# set a variable (value can be int/bool/string)
automationassets.set_automation_variable("test-variable", True)
automationassets.set_automation_variable("test-variable", 4)
automationassets.set_automation_variable("test-variable", "test-string")

# handle a non-existent variable exception
try:
    value = automationassets.get_automation_variable("nonexisting variable")
except AutomationAssetNotFound:
    print "variable not found"
```

### <a name="graphical-runbook-samples"></a>圖形化 Runbook 範例

在圖形運行簿中,可以添加`Get-AutomationVariable``Set-AutomationVariable`或活動。 只需按一下圖形編輯器的「庫」窗格中的變數,然後選擇所需的活動。

![加入變數至畫布](../media/variables/runbook-variable-add-canvas.png)

#### <a name="set-values-in-a-variable"></a>變數中設定值

下圖顯示的範例活動會在圖形化 Runbook 中使用簡單值更新變數。 在此範例中,`Get-AzVM`檢索單個 Azure 虛擬機器並將電腦名稱儲存到現有的自動化字串變數。 [連結是管道還是序列](../automation-graphical-authoring-intro.md#links-and-workflow)並不重要,因為代碼只期望輸出中的單個物件。

![設定簡單變數](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>後續步驟

- 要瞭解有關在圖形創作中連接活動有關,請參閱[圖形創作中的連結](../automation-graphical-authoring-intro.md#links-and-workflow)。
- 要開始使用圖形執行簿,請參閱[我的第一個圖形執行簿](../automation-first-runbook-graphical.md)。
