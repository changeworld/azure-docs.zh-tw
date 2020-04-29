---
title: Azure 自動化常見問題 |Microsoft Docs
description: 有關 Azure 自動化常見問題的解答。
services: automation
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/25/2020
ms.openlocfilehash: 3fa29f3df5f0434c4c61e8d12adbb3f55156a29f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81405969"
---
# <a name="azure-automation-frequently-asked-questions"></a>Azure 自動化常見問題

此 Microsoft 常見問題是關於 Azure 自動化的常見問題清單。 如果您有任何關於其功能的其他問題，請前往討論論壇並張貼您的問題。 當問到常見問題時，我們會將其新增至此文章，以便其他人可以快速輕鬆地找到此問題。

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 如需有關混合式 Runbook 背景工作角色的 Az 模組安裝指示，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 針對您的自動化帳戶，您可以使用[如何更新 Azure 自動化中的 Azure PowerShell 模組](automation-update-azure-modules.md)，將模組更新為最新版本。

## <a name="update-management-solution"></a>更新管理解決方案

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>我可以防止非預期的 OS 層級升級嗎？

在某些 Linux 變體（例如 Red Hat Enterprise Linux）上，可能會透過套件進行 OS 層級的升級。 這可能會導致作業系統版本號碼變更的更新管理執行。 因為更新管理會使用相同的方法來更新系統管理員在 Linux 電腦本機上使用的套件，所以這個行為是刻意的。

若要避免透過更新管理部署來更新 OS 版本，請使用**排除**功能。

在 Red Hat Enterprise Linux 中，要排除的封裝名稱`redhat-release-server.x86_64`是。

### <a name="why-arent-criticalsecurity-updates-applied"></a>為什麼不套用重大/安全性更新？

當您將更新部署至 Linux 機器時，可以選取更新分類。 此選項會篩選符合指定準則的更新。 此篩選會在更新部署時套用至本機電腦上。

由於更新管理會在雲端中執行更新擴充，因此您可以將更新管理中的某些更新標示為有安全性影響，即使本機電腦沒有該資訊也一樣。 如果您將重大更新套用至 Linux 機器，可能會有未在該機器上標示為有安全性影響的更新，因此不會套用。 不過，更新管理可能還是會將該機器回報為不相容，因為它有相關更新的其他資訊。

藉由更新分類來部署更新，不會在 CentOS 的 RTM 版本上生效。 若要正確部署 CentOS 的更新，請選取 [所有分類] 以確定已套用更新。 若是 SUSE，只選取**其他更新**做為分類時，如果必須先 zypper （套件管理員）或其相依性相關的安全性更新，就會導致安裝一些額外的安全性更新。 此行為是 zypper 的限制。 在某些情況下，您可能需要重新執行更新部署，然後透過更新記錄檔來驗證部署。

### <a name="can-i-deploy-updates-across-azure-tenants"></a>我可以跨 Azure 租使用者部署更新嗎？

如果您的電腦需要在另一個 Azure 租使用者報告中進行修補，以更新管理，您必須使用下列因應措施來進行排程。 您可以使用[AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) Cmdlet 搭配指定的`ForUpdateConfiguration`參數來建立排程。 您可以使用[AzAutomationSoftwareUpdateConfiguration](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration?view=azps-3.7.0) Cmdlet，並將另一個租使用者中的機器傳遞給`NonAzureComputer`參數。 下列範例示範如何執行。

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="next-steps"></a>後續步驟

如果這裡沒有解答您的問題，您可以參考下列來源，以取得其他問題和答案。

- [Azure 自動化](https://social.msdn.microsoft.com/Forums/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)
- [意見反應論壇](https://feedback.azure.com/forums/905242-update-management)
