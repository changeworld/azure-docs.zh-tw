---
title: 更新 Azure 自動化中的 Azure PowerShell 模組
description: 本文說明現在要如何更新 Azure 自動化中預設提供的通用 Azure PowerShell 模組。
services: automation
ms.subservice: process-automation
ms.date: 06/14/2019
ms.topic: conceptual
ms.openlocfilehash: 3f2ceda6e87ce16e910f3d215c1fabe81d522f32
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2020
ms.locfileid: "82855554"
---
# <a name="update-azure-powershell-modules-in-azure-automation"></a>更新 Azure 自動化中的 Azure PowerShell 模組

若要更新您的自動化帳戶中的 Azure 模組，您需要使用「[更新 Azure 模組」 runbook](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update)，其以開放原始碼的形式提供。 若要開始使用 **Update-AutomationAzureModulesForAccount** Runbook 來更新 Azure 模組，請從 GitHub 上的[更新 Azure 模組 Runbook 存放庫](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) \(英文\) 下載它。 然後，您可以將它匯入到您的自動化帳戶，或以腳本的形式執行。 若要瞭解如何在您的自動化帳戶中匯入 runbook，請參閱匯[入 runbook](manage-runbooks.md#import-a-runbook)。

預設會在每個自動化帳戶中提供最常見的 PowerShell 模組。 Azure 小組會定期更新 Azure 模組。 因此，若要讓自動化帳戶中的模組保持在最新狀態，您應該使用[update-automationazuremodulesforaccount](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) runbook。

由於產品小組會定期更新模組，因此，內含的 Cmdlet 可能會發生變更。 這些變更（例如，重新具名引數或完全淘汰 Cmdlet）可能會對您的 runbook 造成負面影響。

為了避免影響 Runbook 以及它們所自動化的流程，請先測試和驗證，然後再繼續。 如果您沒有適用於此用途的專用自動化帳戶，請考慮建立一個，讓您可以在開發 Runbook 期間測試許多不同的案例。 這類測試應包括反復的變更，例如更新 PowerShell 模組。

如果您在本機開發您的腳本，建議您在測試時于自動化帳戶中擁有相同的模組版本，以確保您會收到相同的結果。 結果已經過驗證且您已套用所需的任何變更之後，您就能將變更移到生產環境。

> [!NOTE]
> 新的自動化帳戶可能不會包含最新的模組。

> [!NOTE]
> 您將無法刪除全域模組，也就是自動化提供現成可用的模組。

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 如需有關混合式 Runbook 背景工作角色的 Az 模組安裝指示，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 

## <a name="considerations"></a>考量

以下是使用本文來更新 Azure 模組時要考慮的一些考慮：

* 本文中所述的 runbook 預設支援更新 Azure、AzureRM 和 Az 模組。 如需使用此 runbook 更新 Az. Automation 模組的詳細資訊，請參閱[更新 Azure 模組 RUNBOOK 讀我檔案](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md)。 在您的自動化帳戶中使用 Az 模組時，您必須考慮其他重要因素。 若要深入瞭解，請參閱[管理 Azure 自動化中的模組](shared-resources/modules.md)。

* 在啟動此 Runbook 之前，請確定您的自動化帳戶已建立 [Azure 執行身分帳戶認證](manage-runas-account.md)。

* 您可以使用此程式碼做為一般的 PowerShell 腳本，而不是 runbook：只要使用 Disconnect-azaccount 指令[程式](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.7.0)登入 Azure，然後傳遞`-Login $false`至腳本即可。

* 若要在主權雲端上使用此 Runbook，請使用 `AzEnvironment` 參數來將正確的環境傳遞給 Runbook。  可接受的值為 AzureCloud （Azure 公用雲端）、AzureChinaCloud、AzureGermanCloud 和 AzureUSGovernment。 您可以使用`Get-AzEnvironment | select Name`來抓取這些值。 如果您未將值傳遞給此 Cmdlet，runbook 會預設為 AzureCloud。

* 如果您想要使用特定 Azure PowerShell 模組版本，而不是 PowerShell 資源庫上可用的最新模組，請將這些版本傳遞`ModuleVersionOverrides`至**update-automationazuremodulesforaccount** runbook 的選擇性參數。 如需範例，請參閱 [Update-AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
) \(英文\) Runbook。 `ModuleVersionOverrides` 參數中未提及的 Azure PowerShell 模組會以 PowerShell 資源庫上的最新模組版本來更新。 如果您未傳遞任何項目給 `ModuleVersionOverrides` 參數，則所有模組都會以 PowerShell 資源庫上的最新模組版本來更新。 此行為與 [更新 Azure 模組]**** 按鈕相同。

## <a name="next-steps"></a>後續步驟

造訪開放原始碼的[更新 Azure 模組 Runbook](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) \(英文\) 來深入了解它。
