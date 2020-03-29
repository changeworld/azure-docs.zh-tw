---
title: Azure 自動化常見問題 |微軟文檔
description: 回答有關 Azure 自動化 的常見問題的解答。
services: automation
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/25/2020
ms.openlocfilehash: 129a5316c2e7be329b479c79706791e993d20b74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77925809"
---
# <a name="azure-automation-frequently-asked-questions"></a>Azure 自動化常見問題

此 Microsoft 常見問題解答是關於 Azure 自動化的常見問題清單。 如果您對其功能有任何疑問，請前往論壇併發布您的問題。 當問到常見問題時，我們會將其新增至此文章，以便其他人可以快速輕鬆地找到此問題。

## <a name="update-management-solution"></a>更新管理解決方案

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>我能否防止意外的作業系統級升級？

在某些 Linux 變體（如紅帽企業 Linux）上，作業系統級別的升級可能通過包進行。 這可能會導致「更新管理」在 OS 版本號碼變更時執行。 由於更新管理使用與管理員在 Linux 電腦上本地使用的套裝軟體相同的方法來更新包，因此此行為是有意的。

要避免通過更新管理部署更新作業系統版本，請使用**排除**功能。

在 Red Hat Enterprise Linux 中，要排除的套件名稱將是：redhat-release-server.x86_64。

### <a name="why-arent-criticalsecurity-updates-applied"></a>為什麼沒有應用關鍵/安全更新？

當您將更新部署至 Linux 機器時，可以選取更新分類。 此選項篩選應用於滿足指定條件的電腦的更新。 此篩選會在更新部署時套用至本機電腦上。

由於更新管理在雲中執行更新擴充，因此某些更新可以在更新管理中標記為具有安全影響，即使本地電腦沒有該資訊。 因此，如果將重大更新應用於 Linux 電腦，則可能會將更新標記為對該電腦具有安全影響，因此不會應用這些更新。 但是，更新管理可能仍報告該電腦不符合要求，因為它包含有關相關更新的其他資訊。

通過更新分類部署更新在 CentOS 的 RTM 版本上不起作用。 要正確部署 CentOS 的更新，請選擇所有分類以確保應用更新。 對於 SUSE，如果首先需要與 zypper（包管理器）或其依賴項相關的安全更新，則*僅*選擇**其他更新**作為分類可能會導致安裝某些安全更新。 此行為是 zypper 的限制。 在某些情況下，您可能需要重新運行更新部署。 若要驗證，請檢查更新記錄。

### <a name="can-i-deploy-updates-across-azure-tenants"></a>是否可以跨 Azure 租戶部署更新？

如果另一個 Azure 租戶中的電腦向更新管理報告需要修補，則必須使用以下解決方法來計畫它們。 您可以將[新 AzureRmAutomation計畫](/powershell/module/azurerm.automation/new-azurermautomationschedule)Cmdlet 與`-ForUpdate`交換器一起使用以創建計畫，並使用[New-Azure 伺服器軟體更新配置](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
)Cmdlet 並將其他租戶中的電腦傳遞給`-NonAzureComputer`參數。 下列範例示範如何執行：

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="next-steps"></a>後續步驟

如果您的問題未在此處回答，您可以參考以下論壇以獲取其他問題和答案。

- [Azure 自動化](https://social.msdn.microsoft.com/Forums/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)

有關更新管理解決方案的一般回饋，請訪問[回饋論壇](https://feedback.azure.com/forums/905242-update-management)。