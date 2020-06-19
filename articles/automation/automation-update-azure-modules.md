---
title: 更新 Azure 自動化中的 Azure PowerShell 模組
description: 本文說明如何更新 Azure 自動化中預設提供的常用 Azure PowerShell 模組。
services: automation
ms.subservice: process-automation
ms.date: 06/14/2019
ms.topic: conceptual
ms.openlocfilehash: 892197c79285495f49a870bbe79eb75229af2940
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/25/2020
ms.locfileid: "83831019"
---
# <a name="update-azure-powershell-modules"></a>更新 Azure PowerShell 模組

每個自動化帳戶中預設會提供最常用的 Azure PowerShell 模組。 請參閱[預設模組](shared-resources/modules.md#default-modules)。 由於 Azure 小組會定期更新 Azure 模組，內含的 Cmdlet 可能有所有變動。 這些變更 (例如，重新命名參數或完全取代 Cmdlet) 可能對您的 Runbook 造成負面影響。 

> [!NOTE]
> 您無法刪除全域模組，因為這些是自動化預設提供的模組。

## <a name="set-up-an-automation-account"></a>設定自動化帳戶

為了避免影響 Runbook 及其所自動化的流程，有所更新時，請務必測試和驗證。 如果您沒有適用於此用途的專用自動化帳戶，請考慮建立一個，讓您可以在開發 Runbook 期間測試許多不同的案例。 此測試應該包括反覆變更，例如更新 PowerShell 模組。

請確定自動化帳戶已建立 [Azure 執行身分帳戶認證](manage-runas-account.md)。

如果您在本機開發指令碼，則在測試時，本機和自動化帳戶中最好有相同的模組版本，以確保獲得相同的結果。 結果已經過驗證且您已套用所需的任何變更之後，您就能將變更移到生產環境。

> [!NOTE]
> 新的自動化帳戶可能不會包含最新的模組。

## <a name="obtain-a-runbook-to-use-for-updates"></a>取得要用於更新的 Runbook

若要更新自動化帳戶中的 Azure 模組，您必須使用 [Update-AutomationAzureModulesForAccount](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) Runbook (以開放原始碼提供)。 若要開始使用此 Runbook 來更新 Azure 模組，請從 GitHub 存放庫下載此 Runbook。 然後，您可以將此 Runbook 匯入自動化帳戶中，或當作指令碼來執行。 若要了解如何將 Runbook 匯入自動化帳戶中，請參閱[匯入 Runbook](manage-runbooks.md#import-a-runbook)。

根據預設，**Update-AutomationAzureModulesForAccount** Runbook 支援更新 Azure、AzureRM 和 Az 模組。 如需使用此 Runbook 來更新 Az.Automation 模組的詳細資訊，請參閱[更新 Azure 模組 Runbook 讀我檔案](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md)。 在自動化帳戶中使用 Az 模組時，還有其他重要的因素需要考量。 若要深入了解，請參閱[在 Azure 自動化中管理模組](shared-resources/modules.md)。

## <a name="use-update-runbook-code-as-a-regular-powershell-script"></a>將更新 Runbook 程式碼當作一般 PowerShell 指令碼來使用

您可以將 Runbook 程式碼當作一般 PowerShell 指令碼來使用，而不是當作 Runbook。 若要這麼做，請先使用 [onnect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.7.0) Cmdlet 登入 Azure，再將 `-Login $false` 傳給指令碼。

## <a name="use-the-update-runbook-on-sovereign-clouds"></a>在主權雲端使用更新 Runbook

若要在主權雲端使用此 Runbook，請使用 `AzEnvironment` 參數將正確的環境傳給 Runbook。 允許的值為 AzureCloud (Azure 公用雲端)、AzureChinaCloud、AzureGermanCloud 和 AzureUSGovernment。 您可以使用 `Get-AzEnvironment | select Name` 來取出這些值。 如果您未將任何值傳給此 Cmdlet，則 Runbook 會以 AzureCloud 為預設值。

## <a name="use-the-update-runbook-to-update-a-specific-module-version"></a>使用更新 Runbook 來更新特定模組版本

如果您想要使用特定的 Azure PowerShell 模組版本，而不是 PowerShell 資源庫上提供的最新模組版本，請將這些版本傳給 **Update-AutomationAzureModulesForAccount** Runbook 的選擇性 `ModuleVersionOverrides` 參數。 如需範例，請參閱 [Update-AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1) \(英文\) Runbook。 `ModuleVersionOverrides` 參數中未提及的 Azure PowerShell 模組會以 PowerShell 資源庫上的最新模組版本來更新。 如果您未傳遞任何項目給 `ModuleVersionOverrides` 參數，則所有模組都會以 PowerShell 資源庫上的最新模組版本來更新。 此行為同於 Azure 入口網站的 [更新 Azure 模組] 按鈕。

## <a name="next-steps"></a>後續步驟

* 如需使用模組的詳細資訊，請參閱[在 Azure 自動化中管理模組](shared-resources/modules.md)。
* 如需更新 Runbook 的相關資訊，請參閱[更新 Azure 模組 Runbook](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update)。
