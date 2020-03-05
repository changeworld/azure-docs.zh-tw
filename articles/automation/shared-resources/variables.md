---
title: Azure 自動化中的變數資產
description: 變數資產是可用於 Azure 自動化中所有 Runbook 和 DSC 設定的值。  這篇文章說明變數的詳細資料，以及如何以文字式和圖形化編寫形式加以使用。
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 05/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 7495c6b114e232a9aad0075e173abebcb3c92cd0
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273606"
---
# <a name="variable-assets-in-azure-automation"></a>Azure 自動化中的變數資產

變數資產是可用於自動化帳戶中所有 runbook 和 DSC 設定的值。 您可以從 Azure 入口網站、從 PowerShell、在 runbook 內，或在 DSC 設定中管理它們。

自動化變數對下列案例很實用：

- 在多個 runbook 或 DSC 設定之間共用值。

- 從相同 runbook 或 DSC 設定中的多個工作之間共用值。

- 從入口網站或從 PowerShell 命令列管理 runbook 或 DSC 設定所使用的值。 例如，一組一般設定專案，例如特定的 VM 名稱清單、特定的資源群組、AD 功能變數名稱等。  

由於自動化變數會保存，因此即使 runbook 或 DSC 設定失敗，也可以使用它們。 此行為可讓一個 runbook 或 DSC 設定設定另一個 runbook 所使用的值，或下一次執行時的相同 runbook 或 DSC 設定。

建立變數時，您可以 Azure 自動化做為安全資產來指定其加密和儲存體。 其他安全的資產包括認證、憑證和連線。 Azure 自動化會將這些資產加密，並使用為每個自動化帳戶產生的唯一金鑰加以儲存。 金鑰會儲存在系統管理的 Key Vault 中。 儲存安全資產之前，Azure 自動化會從 Key Vault 載入金鑰，然後使用它來加密資產。 

Azure 自動化會安全地儲存每個加密的變數。 無法使用隨附在 Azure PowerShell 模組中的[AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0) Cmdlet 來抓取它的值。 取得加密值的唯一方法是在 runbook 或 DSC 設定中使用**get-automationvariable**活動。

>[!NOTE]
>如果您想要移除變數的加密，您必須刪除變數，並將它重新建立為未加密。

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 如需混合式 Runbook 背景工作角色上的 Az module 安裝指示，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 針對您的自動化帳戶，您可以使用[如何更新 Azure 自動化中的 Azure PowerShell 模組](../automation-update-azure-modules.md)，將模組更新為最新版本。

## <a name="variable-types"></a>變數型別

當您使用 Azure 入口網站建立變數時，您必須從下拉式清單中指定資料類型，讓入口網站可以顯示適當的控制項來輸入變數值。 以下是 Azure 自動化中可用的變數類型：

* String
* 整數
* Datetime
* Boolean
* Null

變數不限於指定的資料類型。 如果您想要指定不同類型的值，則必須使用 Windows PowerShell 來設定變數。 如果您指出**未定義**，變數的值會設定為**null**，而且您必須使用[AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0)指令或**get-automationvariable**活動設定值。

您無法使用入口網站來建立或變更複雜變數類型的值。 不過，您可以使用 Windows PowerShell 提供任何類型的值。 複雜類型會以[PSCustomObject](/dotnet/api/system.management.automation.pscustomobject)的形式抓取。

您可以藉由建立陣列或 hashtable，並將其儲存至變數，將多個值儲存至單一變數。

## <a name="powershell-cmdlets-that-create-and-manage-variable-assets"></a>建立和管理變數資產的 PowerShell Cmdlet

針對 Az 模組，下表中的 Cmdlet 是用來透過 Windows PowerShell 建立和管理自動化變數資產。 它們會隨附于[Az. automation 模組](/powershell/azure/overview)中，可在自動化 RUNBOOK 和 DSC 設定中使用。

| Cmdlet | 描述 |
|:---|:---|
|[AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0) | 擷取現有變數的值。|
|[新增-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationvariable?view=azps-3.5.0) | 建立新的變數並設定其值。|
|[移除-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationvariable?view=azps-3.5.0)| 移除現有的變數。|
|[設定-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0)| 設定現有的變數的值。|

## <a name="activities-to-access-variables"></a>存取變數的活動

下表中的活動是用來存取 runbook 和 DSC 設定中的變數。 **AzAutomationVariable**與**get-automationvariable**之間的差異會針對本文開頭的加密變數加以說明。

| 活動 | 描述 |
|:---|:---|
|**Get-automationvariable**|擷取現有變數的值。|
|**設定-Get-automationvariable**|設定現有的變數的值。|

> [!NOTE]
> 避免在 runbook 或 DSC 設定中**get-automationvariable**的*Name*參數中使用變數。 使用此參數可能會在設計階段將 runbook 或 DSC 設定與自動化變數之間的相依性探索複雜化。

下表中的函式可用來存取和取出 Python2 Runbook 中的變數。

|Python2 函式|描述|
|:---|:---|
|automationassets.get_automation_variable|擷取現有變數的值。 |
|automationassets.set_automation_variable|設定現有的變數的值。 |

> [!NOTE]
> 您必須在 Python runbook 的頂端匯入**automationassets**模組，才能存取資產功能。

## <a name="creating-a-new-automation-variable"></a>建立新自動化變數

### <a name="create-a-new-variable-using-the-azure-portal"></a>使用 Azure 入口網站建立新的變數

1. 從您的自動化帳戶，按一下 [資產] 圖格，然後在 [資產] 刀鋒視窗上選取 [變數]。
2. 在 [變數] 圖格上，選取 [新增變數]。
3. 完成 [**新增變數**] 分頁上的選項，然後按一下 [**建立**] 以儲存新的變數。

### <a name="create-a-new-variable-with-windows-powershell"></a>使用 Windows PowerShell 建立新的變數

此腳本會使用**AzAutomationVariable** Cmdlet 來建立新的變數，並設定其初始值。 然後，它可以使用**AzAutomationVariable**來抓取值。 如果值是簡單型別，則會抓取相同的型別。 如果它是複雜型別，則會抓取**PSCustomObject**型別。

下列範例顯示如何建立 String 類型的變數，然後傳回其值。

```powershell
New-AzAutomationVariable -ResourceGroupName "ResourceGroup01" 
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
–Encrypted $false –Value 'My String'
$string = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value
```

下列範例顯示如何建立具有複雜類型的變數，然後再抓取其屬性。 在此情況下，會使用來自[update-azvm](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM?view=azps-3.5.0)的虛擬機器物件。

```powershell
$vm = Get-AzVM -ResourceGroupName "ResourceGroup01" –Name "VM01"
New-AzAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm

$vmValue = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
$vmName = $vmValue.Name
$vmIpAddress = $vmValue.IpAddress
```

## <a name="using-a-variable-in-a-runbook-or-dsc-configuration"></a>在 Runbook 或 DSC 設定中使用變數

使用 **Set-AutomationVariable** 活動來設定 PowerShell Runbook 或 DSC 設定中自動化變數的值，並使用 **Get-AutomationVariable** 來取出該值。 您不應該在 runbook 或 DSC 設定中使用**AzAutomationVariable**和**AzAutomationVariable** Cmdlet 或其 AzureRM 模組對等專案，因為它們的效率比工作流程活動低。 

請注意，您無法使用**AzAutomationVariable**或其 AzureRM 模組對等的來取得安全變數的值。 

若要從 runbook 或 DSC 設定中建立新的變數，唯一的方法是使用**AzAutomationVariable** Cmdlet。

### <a name="textual-runbook-samples"></a>文字式 Runbook 範例

#### <a name="set-and-retrieve-a-simple-value-from-a-variable"></a>從變數設定和取出簡單的值

下列範例命令顯示如何設定及擷取文字式 Runbook 中的變數。 這個範例假設建立名為*NumberOfIterations*和*NumberOfRunnings*的整數變數，以及名為*samplemessage.txt*的字串變數。

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

#### <a name="set-and-retrieve-a-variable-in-a-python2-runbook"></a>在 Python2 runbook 中設定和取出變數

下列範例示範如何使用變數、設定變數，以及處理 Python2 runbook 中不存在之變數的例外狀況。

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

在圖形化 runbook 中，您可以新增**get-automationvariable**或**get-automationvariable**活動。 只要以滑鼠右鍵按一下圖形化編輯器 [程式庫] 窗格中的變數，然後選取您想要的活動即可。

![加入變數至畫布](../media/variables/runbook-variable-add-canvas.png)

#### <a name="set-values-in-a-variable"></a>設定變數中的值

下圖顯示的範例活動會在圖形化 Runbook 中使用簡單值更新變數。 在此範例中， **update-azvm**會抓取單一 Azure 虛擬機器，並將電腦名稱稱儲存至具有字串類型的現有自動化變數。 [連結是否為管線或序列，是因為程式](../automation-graphical-authoring-intro.md#links-and-workflow)代碼在輸出中只預期單一物件。

![設定簡單變數](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>後續步驟

- 若要深入瞭解如何在圖形化編寫中連接活動，請參閱[圖形化編寫中的連結](../automation-graphical-authoring-intro.md#links-and-workflow)。
- 若要開始使用圖形化 runbook，請參閱[我的第一個圖形化 runbook](../automation-first-runbook-graphical.md)。
