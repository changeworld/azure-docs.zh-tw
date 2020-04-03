---
title: Azure 自動化中的 Az 模組支援
description: 本文提供有關在 Azure 自動化中使用 Az 模組的資訊。
services: automation
ms.subservice: shared-capabilities
ms.date: 02/08/2019
ms.topic: conceptual
ms.openlocfilehash: 21fa1c4faa4a080b9b495e1481fdadcd7e8bea10
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619484"
---
# <a name="az-module-support-in-azure-automation"></a>Azure 自動化中的 Az 模組支援

Azure 自動化支援在 Runbook 中使用[Azure PowerShell Az 模組](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)。 匯總 Az 模組不會在任何新的或現有的自動化帳戶中自動導入。 

## <a name="considerations"></a>考量

在 Azure 自動化中使用匯總 Az 模組時,需要考慮許多事項。 自動化帳戶中的更高級別的解決方案可以使用 Runbook 和模組。 編輯 Runbook 或升級模組可能會導致 Runbook 的問題。 在導入新的 Az 模組之前,應在單獨的自動化帳戶中仔細測試所有 Runbook 和解決方案。 對模組的任何修改都會對[開始/停止](automation-solution-vm-management.md)解決方案產生負面影響。 我們不建議在包含任何解決方案的自動化帳戶中更改模組和 Runbook。 此行為不是 Az 模組的特定項目。 在向自動化帳戶引入任何更改時,應考慮這一點。

在自動化帳戶中導入 Az 模組不會自動在 Runbook 使用的 PowerShell 工作階段中導入該模組。 模組會在下列情況下匯入到 PowerShell 工作階段：

* 當 Runbook 從模組呼叫 cmdlet 時
* 當 Runbook`Import-Module`使用 cmdlet 顯式匯入模組時
* 當 Runbook 匯入另一個模組時,具體取決於模組

> [!IMPORTANT]
> 確保自動化帳戶中的 Runbook 將 Az 模組或[AzureRM](https://www.powershellgallery.com/packages/AzureRM/6.13.1)模組(但不是兩者)導入 PowerShell 作業階段。 如果 Runbook 在 AzureRM 模組之前導入 Az 模組,則 Runbook 將完成。 但是,引用[Get_SerializationSettings](troubleshoot/runbooks.md#get-serializationsettings) cmdlet 的錯誤顯示在作業流中,cmdlet 可能無法正確執行。 如果 Runbook 在 Az 模組之前導入 AzureRM 模組,則 Runbook 也會完成。 但是,在這種情況下,您在作業流中收到一個錯誤,指出無法在同一會話中導入 Az 和 AzureRM 或在同一 Runbook 中使用。

## <a name="migrating-to-az-modules"></a>遷移至 Az 模組

建議您在測試自動化帳戶中測試遷移到 Az 模組。 創建此帳戶后,可以使用本節中的說明處理模組。

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-modules"></a>停止並取消計畫使用 AzureRM 模組的所有 Runbook

為了確保不運行任何使用 AzureRM 模組的現有 Runbook,停止並取消計畫所有受影響的 Runbook。 以執行類似於此範例的代碼,您可以看到存在哪些計畫以及要刪除的計劃:

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

請務必分別查看每個計劃,以確保如有必要,可以在未來為 Runbook 重新計劃。

### <a name="import-the-az-modules"></a>匯入 Az 模組

>[!NOTE]
>僅導入 Runbook 需要的 Az 模組。 不要導入匯總 Az 模組,因為它包含所有 Az 模組。 本指南對於所有模組都相同。

[Az.帳戶](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0)模組是其他 Az 模組的依賴項。 因此,Runbook 必須在導入任何其他模組之前將此模組導入自動化帳戶。

要導入 Azure 門戶中的模組,我們:

1. 從您的自動化帳戶中,選擇**分享資源**下的**模組**。 
2. 按一下 [瀏覽資源庫]**** 來開啟 [瀏覽資源庫] 頁面。  
3. 在搜尋列中,輸入模組名稱,例如`Az.Accounts`。 
4. 在 PowerShell 模組頁面上,按一下「**導入**」以將模組導入自動化帳戶。

![從自動化帳戶匯入模組](media/az-modules/import-module.png)

此導入過程也可以通過[PowerShell 庫](https://www.powershellgallery.com)透過搜尋模組進行導入來完成。 找到模組後,選擇它,選擇 Azure**自動化**選項卡,然後單擊「**部署到 Azure 自動化**」。

![直接從資源庫匯入模組](media/az-modules/import-gallery.png)

## <a name="testing-your-runbooks"></a>測試 Runbook

將 Az 模組導入自動化帳戶後,可以開始編輯 Runbook 以使用這些模組。 大多數 cmdlet 的名稱與 AzureRM 模組的名稱相同,但 AzureRM(或 AzureRm) 前綴已更改為 Az。 有關不遵循此命名約定的模組清單,請參閱[異常清單](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters)。

測試 Runbook 的變更以使用新 cmdlet 的方法是在 Runbook`Enable-AzureRmAlias -Scope Process`的開頭使用 。 通過將此命令添加到 runbook,文本可以運行而不進行更改。

## <a name="after-migration-details"></a>移轉後詳細資訊

遷移完成後,不要嘗試使用自動化帳戶上的 AzureRM 模組開始運行簿。 還建議不要導入或更新帳戶上的 AzureRM 模組。 請考慮遷移到 Az 的帳戶,並且僅使用 Az 模組操作。 

創建新的自動化帳戶時,仍將安裝現有的 AzureRM 模組。 您仍可以使用 AzureRM cmdlet 更新教程執行簿。 但是,不應運行這些 Runbook。

## <a name="next-steps"></a>後續步驟

若要深入了解使用 Az 模組，請參閱[開始使用 Az 模組](/powershell/azure/get-started-azureps?view=azps-1.1.0)。
