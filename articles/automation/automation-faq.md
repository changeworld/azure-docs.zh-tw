---
title: Azure 自動化常見問題集 | Microsoft Docs
description: 本文提供 Azure 自動化常見問題的解答。
services: automation
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/25/2020
ms.openlocfilehash: 1cd20e28d1b36167154059adf728a9cfdf8102bc
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836612"
---
# <a name="azure-automation-frequently-asked-questions"></a>Azure 自動化常見問題集

此 Microsoft 常見問題集是關於 Azure 自動化的常見問題清單。 如有任何關於其功能的其他問題，請前往討論論壇並張貼您的問題。 當問到常見問題時，我們會將其新增至此文章，以便其他人可以快速輕鬆地找到此問題。

## <a name="update-management"></a>更新管理

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>我可以防止非預期的 OS 層級升級嗎？

在某些 Linux 版本上 (例如 Red Hat Enterprise Linux)，安裝特定套件可能會造成 OS 層級升級。 這可能會導致「更新管理」在 OS 版本號碼變更時執行。 因為「更新管理」使用與系統管理員在本機 Linux 電腦上使用的相同方式來更新套件，所以這是刻意的行為。

若要避免透過「更新管理」實作來更新 OS 版本，請使用 [排除] 功能。

在 Red Hat Enterprise Linux 中，要排除的套件名稱為 `redhat-release-server.x86_64`。

### <a name="why-arent-criticalsecurity-updates-applied"></a>為何不套用重大/安全性更新？

當您將更新部署至 Linux 機器時，可以選取更新分類。 此選項會篩選出符合指定準則的更新。 此篩選會在更新部署時套用至本機電腦上。

由於「更新管理」會在雲端中執行更新擴充，因您可在「更新管理」將有些更新標示為有安全性影響，即使本機電腦並沒有該資訊。 如果您將重大更新套用至 Linux 電腦，某些未標示為在該電腦上有安全性影響的更新，因此不會套用。 不過，「更新管理」仍可能將該電腦回報為不符合規範的電腦，因為其對於該相關更新還有其他資訊。

RTM 版本的 CentOS 不支援依更新分類部署更新。 為了適當部署 CentOS 更新，請選取所有分類以確保套用所有更新。 針對 SUSE，只選取 [其他更新] 作為分類時，如果首先需要與 zypper (套件管理員) 相關的安全性更新或其相依性，則可能導致安裝其他一些安全性更新。 此行為是 zypper 的限制。 在某些情況下，您可能需要重新執行更新部署，然後透過更新記錄驗證佈署。

### <a name="can-i-deploy-updates-across-azure-tenants"></a>我可以跨 Azure 租用戶部署更新嗎？

如果您在另一個向「更新管理」回報的 Azure 租用戶中有需要修補的電腦，則必須使用下列因應措施進行排程。 您可以使用 [New-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) Cmdlet 搭配指定的 `ForUpdateConfiguration` 參數來建立排程。 您可以使用 [New-AzAutomationSoftwareUpdateConfiguration](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration?view=azps-3.7.0) Cmdlet，並將其他租用戶中的電腦傳遞至 `NonAzureComputer` 參數。 下列範例示範如何執行。

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="next-steps"></a>後續步驟

如果您的問題並未在此獲得解答，您可以參考下列來源，以取得其他問題和答案。

- [Azure 自動化](https://docs.microsoft.com/answers/topics/azure-automation.html)
- [意見反應論壇](https://feedback.azure.com/forums/905242-update-management)
