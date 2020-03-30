---
title: 在 Azure 自動化中使用 Az 模組
description: 本文提供在 Azure 自動化中使用 Az 模組的資訊
services: automation
ms.subservice: shared-capabilities
ms.date: 02/08/2019
ms.topic: conceptual
ms.openlocfilehash: dfbf54c19aef00cbda886a4531797cda7ef3a191
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76986099"
---
# <a name="az-module-support-in-azure-automation"></a>Azure 自動化中的 Az 模組支援

Azure 自動化支援在您的 Runbook 中使用 [Azure Powershell Az 模組](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)。 Az 模組不會在任何新的或現有的自動化帳戶中自動匯入。 本文討論如何使用 Az 模組與 Azure 自動化。

## <a name="considerations"></a>考量

在 Azure 自動化中使用 Az 模組時有許多要考量的事項。 Runbook 和模組可以由您的自動化帳戶中較高層級的解決方案使用。 編輯 Runbook 或升級模組可能會導致 Runbook 的問題。 您應該在匯入新的 `Az` 模組之前，在個別自動化帳戶中小心地測試所有 Runbook 和解決方案。 對模組的任何修改都會對[開始/停止](automation-solution-vm-management.md)解決方案產生負面影響。 我們不建議在包含任何解決方案的自動化帳戶中更改模組和運行簿。 此行為不是 Az 模組的特定項目。 將任何變更導入到您的自動化帳戶時，應該將此行為列入考量。

在您的自動化帳戶中匯入 `Az` 模組，不會自動在 Runbook 使用的 PowerShell 工作階段中匯入模組。 模組會在下列情況下匯入到 PowerShell 工作階段：

* 當模組中的 Cmdlet 是從 Runbook 叫用時
* 當 Runbook 明確地使用 `Import-Module` Cmdlet 來匯入它時
* 當相依於模組的其他模組匯入到 PowerShell 工作階段時

> [!IMPORTANT]
> 請務必確定自動化帳戶中的 Runbook 僅將 `Az` 或 `AzureRM` 模組匯入到 Runbook 使用的 PowerShell 工作階段，而不是兩者皆匯入。 如果在 Runbook 中 `Az` 於 `AzureRM` 前面匯入，Runbook 就會完成，但是[參考 get_SerializationSettings 方法時發生錯誤](troubleshoot/runbooks.md#get-serializationsettings)會顯示在作業串流中，而且 Cmdlet 可能無法適當地執行。 如果您匯入 `AzureRM` 然後匯入 `Az`，您的 Runbook 仍然會完成，但是您會在作業串流中看到錯誤，指出 `Az` 和 `AzureRM` 兩者無法在相同工作階段中匯入或者在相同 Runbook 中使用。

## <a name="migrating-to-az-modules"></a>遷移至 Az 模組

建議您測試移轉為使用 Az 模組，而不是測試自動化帳戶中的 AzureRM 模組。 一旦建立該自動化帳戶，您可以使用下列步驟來確保移轉順利：

### <a name="stop-and-unschedule-all-runbook-that-uses-azurerm-modules"></a>停止並取消排程使用 AzureRM 模組的所有 Runbook

若要確保不執行使用 `AzureRM` Cmdlet 的任何現有 Runbook，您應該停止並取消排程使用 `AzureRM` 模組的所有 Runbook。 您可以看到有哪些排程存在，以及哪些排程必須執行下列範例來移除：

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

請務必個別檢閱每個排程，以確保您未來可以視需要針對您的 Runbook 重新排程。

### <a name="import-the-az-modules"></a>匯入 Az 模組

只會入您的 Runbook 需要的 Az 模組。 不要匯入彙總 `Az` 模組，因為它包含要匯入的所有 `Az.*` 模組。 本指南對於所有模組都相同。

[Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) 模組是其他 `Az.*` 模組的相依項目。 基於這個原因，此模組必須在您匯入其他任何模組之前，先匯入到您的自動化帳戶。

從您的自動化帳戶中，選擇**共用資源**下的**模組**。 按一下 [瀏覽資源庫]**** 來開啟 [瀏覽資源庫]**** 頁面。  在搜尋列中，輸入模組名稱 (例如`Az.Accounts`)。 在 [PowerShell 模組] 頁面上，按一下 [匯入]**** 將模組匯入到您的自動化帳戶。

![從自動化帳戶匯入模組](media/az-modules/import-module.png)

此匯入程序也可以透過在 [PowerShell 資源庫](https://www.powershellgallery.com)搜尋模組來完成。 一旦您找到模組，請選取它，並且在 [Azure 自動化]**** 索引標籤下按一下 [部署至 Azure 自動化]****。

![直接從資源庫匯入模組](media/az-modules/import-gallery.png)

## <a name="test-your-runbooks"></a>測試您的 Runbook

一旦 `Az` 模組匯入到您的自動化帳戶，您可以開始編輯您的 Runbook 以改為使用 Az 模組。 大部分 Cmdlet 具有相同的名稱，除了 `AzureRM` 已變更為 `Az`。 如需未遵循此程序的模組清單，請參閱[例外狀況清單](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters)。

在修改您的 Runbook 使用新 Cmdlet 之前測試 Runbook 的其中一個方式，是在 Runbook 開頭使用 `Enable-AzureRMAlias -Scope Process`。 藉由將它新增至您的 Runbook，您的 Runbook 就可以執行而不需要變更。

## <a name="after-migration-details"></a>移轉後詳細資料

完成移轉之後，不要再使用帳戶上的 `AzureRM` 模組來啟動 Runbook。 也建議您不要匯入或更新此帳戶上的 `AzureRM` 模組。 從此刻開始，請將此帳戶視為已遷移至 `Az`，並且僅使用 `Az` 模組操作。 建立新的自動化帳戶時，現有 `AzureRM` 模組仍然會安裝，且撰寫的教學課程 Runbook 仍然會包含 `AzureRM` Cmdlet。 不應運行這些 Runbook。

## <a name="next-steps"></a>後續步驟

若要深入了解使用 Az 模組，請參閱[開始使用 Az 模組](/powershell/azure/get-started-azureps?view=azps-1.1.0)。
