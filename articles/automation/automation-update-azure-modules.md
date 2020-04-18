---
title: 更新 Azure 自動化中的 Azure 模組
description: 本文說明現在要如何更新 Azure 自動化中預設提供的通用 Azure PowerShell 模組。
services: automation
ms.subservice: process-automation
ms.date: 06/14/2019
ms.topic: conceptual
ms.openlocfilehash: ba926ccbb069c8620259514e0a64c56957529a0f
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617475"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>如何更新 Azure 自動化中的 Azure PowerShell 模組

要更新自動化帳戶中的 Azure 模組,需要使用可作為開源的[「更新 Azure 模組 runbook」。。](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) 若要開始使用 **Update-AutomationAzureModulesForAccount** Runbook 來更新 Azure 模組，請從 GitHub 上的[更新 Azure 模組 Runbook 存放庫](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) \(英文\) 下載它。 然後,您可以將它導入自動化帳戶,或將其作為腳本運行。 要瞭解如何在自動化帳戶中匯入 Runbook,請參閱導入[Runbook](manage-runbooks.md#importing-a-runbook)。

默認情況下,每個自動化帳戶中都會提供最常見的 PowerShell 模組。 Azure 小組會定期更新 Azure 模組。 因此,要使自動化帳戶中的模組保持最新,應使用[更新自動化 AzureModuleFor帳戶](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update)運行簿。

由於產品小組會定期更新模組，因此，內含的 Cmdlet 可能會發生變更。 例如,這些更改重命名參數或完全棄用 cmdlet 可能會對 Runbook 產生負面影響。

為了避免影響 Runbook 以及它們所自動化的流程，請先測試和驗證，然後再繼續。 如果您沒有適用於此用途的專用自動化帳戶，請考慮建立一個，讓您可以在開發 Runbook 期間測試許多不同的案例。 此測試應包括反覆運算更改,例如更新 PowerShell 模組。

如果在本地開發腳本,建議在測試時在本地具有與自動化帳戶相同的模組版本,以確保收到相同的結果。 結果已經過驗證且您已套用所需的任何變更之後，您就能將變更移到生產環境。

> [!NOTE]
> 新的自動化帳戶可能不會包含最新的模組。

> [!NOTE]
> 您將無法刪除全域模組,這些模組是自動化提供的開箱即用的模組。

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有關混合 Runbook 輔助角色上的 Az 模組安裝說明,請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 

## <a name="considerations"></a>考量

以下是使用本文更新 Azure 模組時需要考慮的一些注意事項:

* 本文中描述的 Runbook 預設支援更新 Azure、AzureRM 和 Az 模組。 有關使用此 Runbook 更新 Az.自動化模組的詳細資訊,請查看[更新 Azure 模組 RUNbook README。](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md) 在使用自動化帳戶中的 Az 模組時,需要考慮其他重要因素。 要瞭解更多資訊,請參閱[在自動化帳戶中使用 Az 模組](az-modules.md)。

* 在啟動此 Runbook 之前，請確定您的自動化帳戶已建立 [Azure 執行身分帳戶認證](manage-runas-account.md)。

* 您可以將此代碼用作一般 PowerShell 文稿,而不是 Runbook:只需先使用[Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.7.0) cmdlet 登入`-Login $false`到 Azure,然後傳遞到 腳本。

* 若要在主權雲端上使用此 Runbook，請使用 `AzEnvironment` 參數來將正確的環境傳遞給 Runbook。  可接受的值包括 AzureCloud(Azure 公共雲)、Azure 中國雲、Azure 德語雲和 AzureUS 政府。 可以使用 檢索這些`Get-AzEnvironment | select Name`值 。 如果不將值傳遞給此 cmdlet,則 Runbook 默認為 AzureCloud。

* 如果要使用特定的 Azure PowerShell 模組版本,而不是 PowerShell 庫中提供的最新模組,請`ModuleVersionOverrides`將這些版本傳遞給**更新-自動化Azure ModuleFor帳戶**手冊的可選參數。 如需範例，請參閱 [Update-AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
) \(英文\) Runbook。 `ModuleVersionOverrides` 參數中未提及的 Azure PowerShell 模組會以 PowerShell 資源庫上的最新模組版本來更新。 如果您未傳遞任何項目給 `ModuleVersionOverrides` 參數，則所有模組都會以 PowerShell 資源庫上的最新模組版本來更新。 此行為與 [更新 Azure 模組]**** 按鈕相同。

## <a name="next-steps"></a>後續步驟

造訪開放原始碼的[更新 Azure 模組 Runbook](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) \(英文\) 來深入了解它。
