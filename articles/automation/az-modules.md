---
title: Azure 自動化中的 Az 模組支援
description: 本文提供有關在 Azure 自動化中使用 Az 模組的資訊。
services: automation
ms.subservice: shared-capabilities
ms.date: 02/08/2019
ms.topic: conceptual
ms.openlocfilehash: 776834937d81a3ba84e3c1db56496a7d951d7982
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548351"
---
# <a name="az-module-support-in-azure-automation"></a>Azure 自動化中的 Az 模組支援

Azure 自動化支援在 Runbook 中使用[Azure PowerShell Az 模組](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)。 任何新或現有自動化帳戶中不會自動導入 Az 模組。 

## <a name="considerations"></a>考量

在 Azure 自動化中使用 Az 模組時有許多要考量的事項。 自動化帳戶中的更高級別的解決方案可以使用 Runbook 和模組。 編輯 Runbook 或升級模組可能會導致 Runbook 的問題。 在導入新`Az`模組之前,應在單獨的自動化帳戶中仔細測試所有 Runbook 和解決方案。 對模組的任何修改都會對[開始/停止](automation-solution-vm-management.md)解決方案產生負面影響。 我們不建議在包含任何解決方案的自動化帳戶中更改模組和 Runbook。 此行為不是 Az 模組的特定項目。 在向自動化帳戶引入任何更改時,應考慮此行為。

在自動化`Az`帳戶中導入模組不會自動導入 Runbook 使用的 PowerShell 工作階段中的模組。 模組會在下列情況下匯入到 PowerShell 工作階段：

* 當模組中的 Cmdlet 是從 Runbook 叫用時
* 當 Runbook 明確地使用 `Import-Module` Cmdlet 來匯入它時
* 當相依於模組的其他模組匯入到 PowerShell 工作階段時

> [!IMPORTANT]
> 請務必確保自動化帳戶中的 Runbook 僅導入`Az``AzureRM`或 模組到 Runbook 使用的 PowerShell 作業階段中,而不是同時導入兩者。 如果在`Az`Runbook`AzureRM`中之前導入,則 Runbook 會完成,但引用[Get_SerializationSettings](troubleshoot/runbooks.md#get-serializationsettings) cmdlet 的錯誤顯示在作業流中,並且 cmdlet 可能無法正確執行。 如果導入`AzureRM``Az`然後 ,Runbook 仍然完成,但在作業流中收到錯誤,`Az``AzureRM`指出兩者 不能在同一會話中導入,也不能在同一 Runbook 中使用。

## <a name="migrating-to-az-modules"></a>遷移至 Az 模組

建議您在測試自動化帳戶中測試遷移到 Az 模組。 創建自動化帳戶后,可以使用本節中的說明處理模組。

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-cmdlets"></a>停止並取消計畫使用 AzureRM cmdlet 的所有 Runbook

若要確保不執行使用 `AzureRM` Cmdlet 的任何現有 Runbook，您應該停止並取消排程使用 `AzureRM` 模組的所有 Runbook。 以執行類似於此範例的代碼,您可以看到存在哪些計畫以及必須刪除哪些計畫。

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

請務必分別查看每個計劃,以確保在必要時可以重新計劃運行簿。

### <a name="import-the-az-modules"></a>匯入 Az 模組

只會入您的 Runbook 需要的 Az 模組。 不要導入匯總`Az`模塊,因為它`Az.*`包含所有 模組。 本指南對於所有模組都相同。

[Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) 模組是其他 `Az.*` 模組的相依項目。 因此,在導入任何其他模組之前,需要將此模組導入自動化帳戶。

從您的自動化帳戶中,選擇**分享資源**下的**模組**。 按一下 [瀏覽資源庫]**** 來開啟 [瀏覽資源庫]**** 頁面。  在搜尋列中，輸入模組名稱 (例如`Az.Accounts`)。 在 PowerShell 模組頁面上,按一下「**導入**」以將模組導入自動化帳戶。

![從自動化帳戶匯入模組](media/az-modules/import-module.png)

此導入過程也可以通過[PowerShell 庫](https://www.powershellgallery.com)透過搜尋模組進行導入來完成。 一旦您找到模組，請選取它，並且在 [Azure 自動化]**** 索引標籤下按一下 [部署至 Azure 自動化]****。

![直接從資源庫匯入模組](media/az-modules/import-gallery.png)

## <a name="testing-your-runbooks"></a>測試 Runbook

將`Az`模組導入自動化帳戶後,可以開始編輯 Runbook 以使用 Az 模組。 大多數 cmdlet 具有相同的`AzureRM`名稱, 但`Az`已更改為的除外。 有關不遵循此命名約定的模組清單,請參閱[異常清單](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters)。

測試 Runbook 的變更以使用新 cmdlet 的方法是在 Runbook`Enable-AzureRMAlias -Scope Process`的開頭使用 。 通過將此命令添加到 runbook,文本可以運行而不進行更改。

## <a name="after-migration-details"></a>移轉後詳細資訊

遷移完成後,不要嘗試使用自動化帳戶上的`AzureRM`模組開始運行簿。 還建議不要導入或更新`AzureRM`帳戶上的模組。 請考慮遷移到`Az`的帳戶,並且僅使用`Az`模組操作。 

創建新的自動化帳戶時,仍安裝現有`AzureRM`模組。 您仍可以使用`AzureRM`cmdlet 更新教程執行簿。 不應運行這些 Runbook。

## <a name="next-steps"></a>後續步驟

若要深入了解使用 Az 模組，請參閱[開始使用 Az 模組](/powershell/azure/get-started-azureps?view=azps-1.1.0)。
