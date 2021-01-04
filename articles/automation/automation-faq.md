---
title: Azure 自動化常見問題集 | Microsoft Docs
description: 本文提供 Azure 自動化常見問題的解答。
services: automation
ms.subservice: ''
ms.topic: conceptual
ms.date: 12/17/2020
ms.openlocfilehash: 2b40cc3d4cea4476ffde8bee8cec694975eb5083
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/22/2020
ms.locfileid: "97724267"
---
# <a name="azure-automation-frequently-asked-questions"></a>Azure 自動化常見問題集

此 Microsoft 常見問題集是關於 Azure 自動化的常見問題清單。 如果您有任何其他有關其功能的問題，請前往討論論壇並張貼您的問題。 當問題發生時，我們會將它新增至本文，以便快速且輕鬆地找到。

## <a name="update-management"></a>更新管理

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>我可以防止非預期的 OS 層級升級嗎？

在某些 Linux 版本上 (例如 Red Hat Enterprise Linux)，安裝特定套件可能會造成 OS 層級升級。 這可能會導致「更新管理」在 OS 版本號碼變更時執行。 因為「更新管理」使用與系統管理員在本機 Linux 電腦上使用的相同方式來更新套件，所以這是刻意的行為。

若要避免透過「更新管理」實作來更新 OS 版本，請使用 [排除] 功能。

在 Red Hat Enterprise Linux 中，要排除的套件名稱為 `redhat-release-server.x86_64`。

### <a name="why-arent-criticalsecurity-updates-applied"></a>為何不套用重大/安全性更新？

當您將更新部署至 Linux 機器時，可以選取更新分類。 此選項會篩選出符合指定準則的更新。 此篩選會在更新部署時套用至本機電腦上。

由於「更新管理」會在雲端中執行更新擴充，因您可在「更新管理」將有些更新標示為有安全性影響，即使本機電腦並沒有該資訊。 如果您將重大更新套用至 Linux 電腦，某些未標示為在該電腦上有安全性影響的更新，因此不會套用。 不過，「更新管理」仍可能將該電腦回報為不符合規範的電腦，因為其對於該相關更新還有其他資訊。

RTM 版本的 CentOS 不支援依更新分類部署更新。 為了適當部署 CentOS 更新，請選取所有分類以確保套用所有更新。 針對 SUSE，如果分類與 zypper (套件管理員) 或必須先有相依性的相依性，則只會選取 **其他更新** 。 此行為是 zypper 的限制。 在某些情況下，您可能需要重新執行更新部署，然後透過更新記錄驗證佈署。

### <a name="can-i-deploy-updates-across-azure-tenants"></a>我可以跨 Azure 租用戶部署更新嗎？

如果您在另一個向「更新管理」回報的 Azure 租用戶中有需要修補的電腦，則必須使用下列因應措施進行排程。 您可以使用 [New-AzAutomationSchedule](/powershell/module/Az.Automation/New-AzAutomationSchedule) Cmdlet 搭配指定的 `ForUpdateConfiguration` 參數來建立排程。 您可以使用 [New-AzAutomationSoftwareUpdateConfiguration](/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration) Cmdlet，並將其他租用戶中的電腦傳遞至 `NonAzureComputer` 參數。 下列範例示範如何執行。

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="process-automation---python-runbooks"></a>進程自動化-Python runbook

### <a name="which-python-3-version-is-supported-in-azure-automation"></a>Azure 自動化支援哪個 Python 3 版本？

針對雲端作業，支援 Python 3.8。 如果程式碼在不同版本之間相容，則任何3.x 版本的腳本和封裝都可能適用。

針對 Windows 混合式 Runbook 背景工作角色上的混合式作業，您可以選擇安裝您想要使用的任何3.x 版。 針對 Linux 混合式 Runbook 背景工作角色上的混合式作業，我們相依于電腦上安裝的 Python 3 版本，以執行 DSC OMSConfig 和 Linux 混合式背景工作角色。 建議您安裝3.6 版;不過，如果方法簽章或 Python 3 版本之間的合約沒有任何重大變更，則不同的版本也應可運作。

### <a name="can-python-2-and-python-3-runbooks-run-in-same-automation-account"></a>Python 2 和 Python 3 runbook 是否可以在相同的自動化帳戶中執行？

是，在相同的自動化帳戶中，不會有使用 Python 2 和 Python 3 runbook 的限制。  

### <a name="what-is-the-plan-for-migrating-existing-python-2-runbooks-and-packages-to-python-3"></a>將現有的 Python 2 runbook 和套件遷移至 Python 3 的方案是什麼？

Azure 自動化不打算將 Python 2 runbook 和套件遷移至 Python 3。 您將必須自行執行這種遷移。 現有和新的 Python 2 runbook 和套件將繼續運作。

### <a name="what-are-the-packages-supported-by-default-in-python-3-environment"></a>Python 3 環境中預設支援哪些套件？

Python 3 自動化環境預設會安裝 Azure 套件4.0.0。 您可以手動匯入較高版本的 Azure 套件以覆寫預設版本。

### <a name="what-if-i-run-a-python-3-runbook-that-references-a-python-2-package-or-vice-versa"></a>如果我執行的 Python 3 runbook 參考 Python 2 封裝或反之亦然？

Python 2 和 Python 3 有不同的執行環境。 當 Python 2 runbook 正在執行時，只可匯入 Python 2 套件，並與 Python 3 類似。

### <a name="how-do-i-differentiate-between-python-2-and-python-3-runbooks-and-packages"></a>如何? 區分 Python 2 和 Python 3 runbook 和套件？

Python 3 是新的 runbook 定義，可區別 Python 2 和 Python 3 runbook。 同樣地，針對 Python 3 套件引進另一個套件種類。

## <a name="next-steps"></a>後續步驟

如果這裡沒有解答您的問題，您可以參考下列來源以取得更多問題和解答。

- [Azure 自動化](/answers/topics/azure-automation.html)
- [意見反應論壇](https://feedback.azure.com/forums/905242-update-management)
