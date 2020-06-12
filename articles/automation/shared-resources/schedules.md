---
title: 管理 Azure 自動化中的排程
description: 本文說明如何在 Azure 自動化中建立和使用排程。
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 557dc3ad48f8f21d8898e2beb5d940d66058e90c
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744959"
---
# <a name="manage-schedules-in-azure-automation"></a>管理 Azure 自動化中的排程

若要排程在指定時間啟動 Azure 自動化中的 Runbook，您會將它連結到一個或多個排程。 您可以在 Azure 入口網站中，將 Runbook 排程設定成執行一次，或是每小時或每天重複發生。 您也可以將其排定為在每週、每月、特定的星期幾、月份中的某些日子或月份中的特定日子執行。 Runbook 可以連結至多個排程，而排程可以有多個與其連結的 Runbook。

> [!NOTE]
> Azure 自動化支援日光節約時間，並針對自動化作業適當地進行排程。

> [!NOTE]
> 目前不針對 Azure Automation DSC 設定啟用排程。

## <a name="powershell-cmdlets-used-to-access-schedules"></a>用來存取排程的 PowerShell Cmdlet

下表中的 Cmdlet 會使用 PowerShell 來建立及管理自動化排程。 其會隨附於 [Az 模組](modules.md#az-modules)中。 

| 指令程式 | 描述 |
|:--- |:--- |
| [Get-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationSchedule?view=azps-3.7.0) |擷取排程。 |
| [Get-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationscheduledrunbook?view=azps-3.7.0) |擷取排程的 Runbook。 |
| [New-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) |建立新排程。 |
| [Register-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook?view=azps-3.7.0) |將 Runbook 與排程相關聯。 |
| [Remove-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationSchedule?view=azps-3.7.0) |移除排程。 |
| [Set-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationSchedule?view=azps-3.7.0) |設定現有排程的屬性。 |
| [Unregister-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Unregister-AzAutomationScheduledRunbook?view=azps-3.7.0) |從排程分離 Runbook。 |

## <a name="create-a-schedule"></a>建立排程

您可以在 Azure 入口網站中或使用 PowerShell，為 Runbook 建立新排程。 為了避免影響 Runbook 和它們所自動化的處理序，您應該先使用專用於測試的自動化帳戶測試任何具有連結排程的 Runbook。 測試會驗證您排定的 Runbook 是否繼續正常運作。 如果發現問題，您可以在將更新的 Runbook 版本遷移至生產環境之前，進行疑難排解並套用所需的任何變更。

> [!NOTE]
> 除非您已選取 [模組] 中的[更新 Azure 模組](../automation-update-azure-modules.md)選項進行手動更新，否則自動化帳戶不會自動取得任何新版本的模組。 執行新的排程工作時，Azure 自動化會使用自動化帳戶中的最新模組。 

### <a name="create-a-new-schedule-in-the-azure-portal"></a>在 Azure 入口網站中建立新排程

1. 在 Azure 入口網站中，從您的自動化帳戶選取左側 [共用資源] 區段底下的 [排程]。
1. 在頁面頂端選取 [新增排程]。
1. 在 [新增排程] 窗格上，為新排程輸入名稱並選擇性地輸入描述。
1. 透過選取 [一次] 或 [定期]，以決定排程僅執行一次或定期執行。 如果選取 [一次]，請指定開始時間，然後選取 [建立]。 如果您選取 [重複執行]，請指定開始時間。 針對 [重複頻率]，選取您想要讓 Runbook 重複的頻率。 選取依小時、天、週或月。
    1. 如果您選取 [週]，系統會提供星期幾供您選擇。 選取您需要的天數。 首次執行排程的時間將會是開始時間後所選的第一天。 例如，若要選擇週末排程，請選取星期六和星期日。
    
       ![設定週末重複執行排程](../media/schedules/week-end-weekly-recurrence.png)

    2. 如果您選取 [月]，系統會提供您不同選項。 若是 [每月發生次數] 選項，請選取 [日期] 或 [星期]。 如果您選取 [日期]，則會出現一個行事曆，讓您可以視需要選擇多個日期。 如果您選擇的日期 (例如 31 日) 未出現在目前月份中，則排程不會執行。 如果您希望在最後一天執行排程，請在 [在每月最後一天執行] 下選取 [是]。 如果您選取 [星期]，[重複頻率] 選項就會出現。 選擇 [第一週]、[第二週]、[第三週]、[第四週] 或 [最後一週]。 最後，請選擇重複執行的日期。

       ![每月排定在當月的第一天、第十五天和最後一天](../media/schedules/monthly-first-fifteenth-last.png)

1. 完成之後，選取 [建立]。

### <a name="create-a-new-schedule-with-powershell"></a>使用 PowerShell 建立新排程

使用 [New-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) Cmdlet 來建立排程。 您必須指定排程的開始時間，以及其應該執行的頻率。 下列範例顯示如何建立多個不同的排程情節。

#### <a name="create-a-one-time-schedule"></a>建立單次排程

下列範例會建立單次排程。

```azurepowershell-interactive
$TimeZone = ([System.TimeZoneInfo]::Local).Id
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule01" -StartTime "23:00" -OneTime -ResourceGroupName "ResourceGroup01" -TimeZone $TimeZone
```

#### <a name="create-a-recurring-schedule"></a>建立重複執行排程

下列範例顯示如何建立每日下午 1:00 執行的重複執行排程，為期一年。

```azurepowershell-interactive
$StartTime = Get-Date "13:00:00"
$EndTime = $StartTime.AddYears(1)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule02" -StartTime $StartTime -ExpiryTime $EndTime -DayInterval 1 -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule"></a>建立每週重複執行排程

下列範例顯示如何建立只在工作日執行的每週排程。

```azurepowershell-interactive
$StartTime = (Get-Date "13:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekDays = @([System.DayOfWeek]::Monday..[System.DayOfWeek]::Friday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule03" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule-for-weekends"></a>為工作日建立每週重複執行排程

下列範例顯示如何建立只在週末執行的每週排程。

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekendDays = @([System.DayOfWeek]::Saturday,[System.DayOfWeek]::Sunday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Weekends 6PM" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekendDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-recurring-schedule-for-the-first-fifteenth-and-last-days-of-the-month"></a>為當月的第一天、第十五天和最後一天建立重複執行排程

下列範例顯示如何建立在當月第一天、第十五天和最後一天執行的重複執行排程。

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
New-AzAutomationSchedule -AutomationAccountName "TestAzureAuto" -Name "1st, 15th and Last" -StartTime $StartTime -DaysOfMonth @("One", "Fifteenth", "Last") -ResourceGroupName "TestAzureAuto" -MonthInterval 1
```

## <a name="link-a-schedule-to-a-runbook"></a>將排程連結至 Runbook

Runbook 可以連結至多個排程，而排程可以有多個與其連結的 Runbook。 如果 Runbook 有參數，您可以為它們提供值。 您必須為任何必要參數提供值，也可以為任何選擇性參數提供值。 每次此排程啟動 Runbook 時會使用這些值。 您可以將相同的 Runbook 附加到另一個排程，並指定不同的參數值。

### <a name="link-a-schedule-to-a-runbook-with-the-azure-portal"></a>使用 Azure 入口網站將排程連結至 Runbook

1. 在 Azure 入口網站中，從您的自動化帳戶選取 [程序自動化] 底下的 [Runbook]。
1. 選取要排程的 Runbook 名稱。
1. 如果 Runbook 目前未連結至排程，則會提供您建立新排程或連結至現有排程的選項。
1. 如果 Runbook 有參數，您可以選取 [修改執行設定 (預設值: Azure)] 選項，而且 [參數] 窗格隨即出現。 您可以在這裡輸入參數資訊。

### <a name="link-a-schedule-to-a-runbook-with-powershell"></a>使用 PowerShell 將排程連結至 Runbook

使用 [Register-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook?view=azps-3.7.0) Cmdlet 來連結排程。 您可以使用 Parameters 參數來指定 Runbook 參數的值。 如需如何指定參數值的詳細資訊，請參閱[在 Azure 自動化中啟動 Runbook](../automation-starting-a-runbook.md)。
下列範例顯示如何使用 Azure Resource Manager Cmdlet 搭配參數，將排程連結至 Runbook。

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
–Name $runbookName –ScheduleName $scheduleName –Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="schedule-runbooks-to-run-more-frequently"></a>將 Runbook 排定為更頻繁地執行

Azure 自動化中排程可以設定的最頻繁間隔為一小時。 如果您需要排程的執行間隔少於一小時，有兩個選項：

* 為 Runbook 建立 [Webhook](../automation-webhooks.md)，以及使用 [Azure Logic Apps](../../logic-apps/logic-apps-overview.md) 來呼叫 Webhook。 Azure Logic Apps 提供更細緻的細微性來定義排程。

* 建立四個排程，彼此全都在 15 分鐘內開始，而且每小時執行一次。 此案例可讓 Runbook 使用不同排程每 15 分鐘執行一次。

## <a name="disable-a-schedule"></a>停用排程

停用排程時，與其連結的任何 Runbook 無法再依該排程執行。 您可以手動停用排程，或在建立排程時為具有頻率的排程設定到期時間。 當到達到期時間時，就會停用排程。

### <a name="disable-a-schedule-from-the-azure-portal"></a>從 Azure 入口網站停用排程

1. 在您的自動化帳戶中，選取 [共用資源] 下的 [排程]。
1. 選取排程的名稱以開啟詳細資料窗格。
1. 將 [已啟用] 變更為 [否]。

> [!NOTE]
> 如果您想要停用過去具有開始時間的排程，必須先將開始日期變更為未來時間，然後才能儲存。

### <a name="disable-a-schedule-with-powershell"></a>使用 PowerShell 停用排程

使用 [Set-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationSchedule?view=azps-3.7.0) Cmdlet 變更現有排程的屬性。 若要停用排程，請將 `IsEnabled` 參數指定為 False。

下列範例顯示如何使用 Azure Resource Manager Cmdlet 來停用 Runbook 的排程。

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="remove-a-schedule"></a>移除排程

當準備好移除您的排程時，您可以使用 Azure 入口網站或 PowerShell。 請記住，您只能移除已停用的排程，如上一節中所述。

### <a name="remove-a-schedule-using-the-azure-portal"></a>使用 Azure 入口網站移除排程

1. 在您的自動化帳戶中，選取 [共用資源] 下的 [排程]。
2. 按一下排程的名稱以開啟詳細資料窗格。
3. 按一下 **[刪除]** 。

### <a name="remove-a-schedule-with-powershell"></a>使用 PowerShell 移除排程

您可以使用如下所示的 `Remove-AzAutomationSchedule` Cmdlet 來刪除現有的排程。 

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Remove-AzAutomationSchedule -AutomationAccountName $automationAccountName `
-Name $scheduleName -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>後續步驟

* 若要深入了解用來存取排程的 Cmdlet，請參閱[管理 Azure 自動化中的模組](modules.md)。
* 如需 Runbook 的一般資訊，請參閱 [Azure 自動化中的 Runbook 執行](../automation-runbook-execution.md)。