---
title: Azure 自動化常見問題 |微軟文件
description: 回答有關 Azure 自動化的常見問題的解答。
services: automation
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/25/2020
ms.openlocfilehash: 3fa29f3df5f0434c4c61e8d12adbb3f55156a29f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81405969"
---
# <a name="azure-automation-frequently-asked-questions"></a>Azure 自動化常見問題

此 Microsoft 常見問題解答是關於 Azure 自動化的常見問題清單。 如果您對其功能有任何疑問,請前往論壇併發佈您的問題。 當問到常見問題時，我們會將其新增至此文章，以便其他人可以快速輕鬆地找到此問題。

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有關混合 Runbook 輔助角色上的 Az 模組安裝說明,請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 對於自動化帳戶,可以使用[「如何更新 Azure 自動化 中的 Azure PowerShell」模組](automation-update-azure-modules.md)將模組更新到最新版本。

## <a name="update-management-solution"></a>更新管理解決方案

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>我能否防止意外的操作系統級升級?

在某些 Linux 變體(如紅帽企業 Linux)上,操作系統級別的升級可能通過包進行。 這可能會導致運行更新管理,其中操作系統版本號會更改。 由於更新管理使用與管理員在 Linux 電腦上本地使用的軟體包相同的方法來更新包,因此此行為是有意的。

要避免通過更新管理部署更新操作系統版本,請使用**排除**功能。

在紅帽企業 Linux 中,要`redhat-release-server.x86_64`排除的 包名稱為 。

### <a name="why-arent-criticalsecurity-updates-applied"></a>為什麼沒有應用關鍵/安全更新?

當您將更新部署至 Linux 機器時，可以選取更新分類。 此選項篩選滿足指定條件的更新。 此篩選會在更新部署時套用至本機電腦上。

由於更新管理在雲中執行更新擴充,因此您可以在更新管理中標記某些更新具有安全影響,即使本地電腦沒有該資訊也是如此。 如果將關鍵更新應用於 Linux 計算機,則可能有一些更新未標記為對該電腦具有安全影響,因此不會應用這些更新。 但是,更新管理可能仍報告該計算機不符合要求,因為它包含有關相關更新的其他資訊。

通過更新分類部署更新在 CentOS 的 RTM 版本上不起作用。 要正確部署 CentOS 的更新,請選擇所有分類以確保應用更新。 對於 SUSE,如果首先需要與 zypper(包管理器)或其依賴項相關的安全更新,則選擇"僅**選擇其他更新**作為分類"可能會導致安裝一些其他安全更新。 此行為是 zypper 的限制。 在某些情況下,您可能需要重新運行更新部署,然後通過更新日誌驗證部署。

### <a name="can-i-deploy-updates-across-azure-tenants"></a>是否可以跨 Azure 租戶部署更新?

如果電腦需要在另一個 Azure 租戶中進行修補,報告給更新管理,則必須使用以下解決方法來計劃它們。 您可以使用["新建-AzAutomation計劃](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0)"cmdlet`ForUpdateConfiguration`與指定的 參數一起創建計劃。 您可以使用[New-AzAutomationSoftwareUpdate 設定](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration?view=azps-3.7.0)cmdlet 並將另一個租戶中的`NonAzureComputer`電腦傳遞參數 。 下列範例示範如何執行。

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="next-steps"></a>後續步驟

如果您的問題未在此處回答,您可以參考以下來源以獲取其他問題和答案。

- [Azure 自動化](https://social.msdn.microsoft.com/Forums/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)
- [意見反應論壇](https://feedback.azure.com/forums/905242-update-management)
