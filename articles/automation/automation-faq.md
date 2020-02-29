---
title: Azure 自動化常見問題 |Microsoft Docs
description: 有關 Azure 自動化常見問題的解答。
services: automation
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/25/2020
ms.openlocfilehash: 129a5316c2e7be329b479c79706791e993d20b74
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2020
ms.locfileid: "77925809"
---
# <a name="azure-automation-frequently-asked-questions"></a>Azure 自動化常見問題

此 Microsoft 常見問題是關於 Azure 自動化的常見問題清單。 如果您有任何關於其功能的其他問題，請前往討論論壇並張貼您的問題。 當問到常見問題時，我們會將其新增至此文章，以便其他人可以快速輕鬆地找到此問題。

## <a name="update-management-solution"></a>更新管理解決方案

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>我可以防止非預期的 OS 層級升級嗎？

在某些 Linux 變體（例如 Red Hat Enterprise Linux）上，可能會透過套件進行 OS 層級的升級。 這可能會導致「更新管理」在 OS 版本號碼變更時執行。 因為更新管理會使用相同的方法來更新系統管理員在 Linux 電腦本機上使用的套件，所以這個行為是刻意的。

若要避免透過更新管理部署來更新 OS 版本，請使用**排除**功能。

在 Red Hat Enterprise Linux 中，要排除的套件名稱將是：redhat-release-server.x86_64。

### <a name="why-arent-criticalsecurity-updates-applied"></a>為什麼不套用重大/安全性更新？

當您將更新部署至 Linux 機器時，可以選取更新分類。 此選項會篩選套用至符合指定準則之電腦的更新。 此篩選會在更新部署時套用至本機電腦上。

由於更新管理會在雲端中執行更新擴充，因此有些更新可能會在更新管理中標示為有安全性影響，即使本機電腦沒有該資訊也一樣。 因此，如果您將重大更新套用至 Linux 電腦，可能會有未標示為對該電腦造成安全性影響的更新，因此不會套用更新。 不過，更新管理可能仍會將該機器回報為不符合規範，因為它有相關更新的其他資訊。

藉由更新分類來部署更新，不會在 CentOS 的 RTM 版本上生效。 若要正確部署 CentOS 的更新，請選取 [所有分類] 以確定已套用更新。 若是 SUSE，*只*選取**其他更新**做為分類時，如果必須先 zypper （套件管理員）或其相依性相關的安全性更新，也可能會造成某些安全性更新。 此行為是 zypper 的限制。 在某些情況下，您可能需要重新執行更新部署。 若要驗證，請檢查更新記錄。

### <a name="can-i-deploy-updates-across-azure-tenants"></a>我可以跨 Azure 租使用者部署更新嗎？

如果您有另一個 Azure 租使用者中的電腦向您需要修補的更新管理報告，則必須使用下列因應措施來進行排程。 您可以使用[new-azurermautomationschedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) Cmdlet 搭配 `-ForUpdate` 參數來建立排程，並使用[new-azurermautomationsoftwareupdateconfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) Cmdlet，並將另一個租使用者中的機器傳遞至 `-NonAzureComputer` 參數。 下列範例示範如何執行：

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="next-steps"></a>後續步驟

如果這裡沒有解答您的問題，您可以參考下列論壇以取得其他問題和答案。

- [Azure 自動化](https://social.msdn.microsoft.com/Forums/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)

如需有關更新管理解決方案的一般意見反應，請流覽[意見反應論壇](https://feedback.azure.com/forums/905242-update-management)。