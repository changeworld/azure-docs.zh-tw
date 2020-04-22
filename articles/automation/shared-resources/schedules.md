---
title: 在 Azure 自動化中管理計劃
description: 自動化排程是用來排程讓 Azure 自動化中的 Runbook 自動啟動。 描述如何建立和管理排程，以便以特定時間或循環排程來自動啟動 Runbook。
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 3603e76186ce30fb491d829d3a804837f4ac2e6d
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732784"
---
# <a name="manage-schedules-in-azure-automation"></a>在 Azure 自動化中管理計劃

若要排程在指定時間啟動 Azure 自動化中的 Runbook，您會將它連結到一個或多個排程。 您可以在 Azure 入口網站中，將 Runbook 排程設定成執行一次，或是每小時或每天重複發生。 您也可以將其排定為在每週、每月、特定的星期幾、月份中的某些日子或月份中的特定日子執行。 Runbook 可以連結至多個排程，而排程可以有多個與其連結的 Runbook。

> [!NOTE]
> 排程目前不支援 Azure Automation DSC 組態。

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有關混合 Runbook 輔助角色上的 Az 模組安裝說明,請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 對於自動化帳戶,可以使用[「如何更新 Azure 自動化 中的 Azure PowerShell」模組](../automation-update-azure-modules.md)將模組更新到最新版本。

## <a name="powershell-cmdlets"></a>PowerShell Cmdlet

下表中的 cmdlet 用於在 Azure 自動化中使用 PowerShell 創建和管理計畫。 它們是隨著 [Azure PowerShell 模組](/powershell/azure/overview)的一部分推出。

| 指令程式 | 描述 |
|:--- |:--- |
| [取得自動化計劃](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationSchedule?view=azps-3.7.0) |擷取排程。 |
| [取得阿茲自動化計畫執行簿](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationscheduledrunbook?view=azps-3.7.0) |擷取排程的 Runbook。 |
| [新自動化計劃](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) |建立新排程。 |
| [註冊-阿茲自動化計劃執行簿](https://docs.microsoft.com/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook?view=azps-3.7.0) |將 Runbook 與排程相關聯。 |
| [刪除-阿茲自動化計劃](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationSchedule?view=azps-3.7.0) |移除排程。 |
| [設定-阿茲自動化時程表](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationSchedule?view=azps-3.7.0) |設定現有排程的屬性。 |
| [未註冊─阿茲自動化計劃執行簿](https://docs.microsoft.com/powershell/module/Az.Automation/Unregister-AzAutomationScheduledRunbook?view=azps-3.7.0) |從排程分離 Runbook。 |

## <a name="creating-a-schedule"></a>建立排程

您可以在 Azure 門戶中或使用 PowerShell 為 Runbook 創建新計畫。

> [!NOTE]
> 執行新的排程工作時，Azure 自動化會使用自動化帳戶中的最新模組。  為了避免影響 Runbook 和它們所自動化的處理序，您應該先使用專用於測試的自動化帳戶測試任何具有連結排程的 Runbook。  這會驗證您排程的 Runbook 會持續正確運作，否則，您可以進一步進行疑難排解，並套用任何必要變更，再將已更新的 Runbook 版本移轉至生產環境。
> 除非您已選取 [模組]**** 中的[更新 Azure 模組](../automation-update-azure-modules.md)選項進行手動更新，否則自動化帳戶不會自動取得任何新版本的模組。

### <a name="create-a-new-schedule-in-the-azure-portal"></a>在 Azure 門戶中建立新計畫

1. 在 Azure 門戶中,從自動化帳戶中選擇**左側**「**共用資源**」一節下的計畫。
2. 在分頁的頂端按一下 [加入排程]****。
3. 在"新建計劃"窗格中,鍵入名稱,並可選擇為新計劃提供說明。
4. 透過選取 [一次]**** 或 [定期]****，以決定排程僅執行一次或定期執行。 如果選擇 **"一次**",請指定開始時間,然後單擊"**創建**"。 如果選擇 **「定期**」,請指定開始時間。 對 **「每次重複」 選擇**希望 Runbook 重複的頻率 - 按小時、天、周或月。
    1. 如果選擇**周**,則顯示星期的天數供您選擇。 選取您需要的天數。 首次執行排程的時間將會是開始時間後所選的第一天。 例如,要選擇周末時程表,請選擇星期六和周日。 
    
       ![設定週末定期計劃](../media/schedules/week-end-weekly-recurrence.png)

    2. 如果選擇**月份**,則為您提供不同的選項。 對於每月**匹配選項**,選擇**月天**或 **「周天**」。 如果選擇 **「月天**」,將顯示一個日曆,允許您根據需要選擇任意多天。 如果選擇的日期(如當月未發生的 31 日),則計劃不會運行。 如果您希望在最後一天執行排程，請在 [在每月最後一天執行]**** 下選取 [是]****。 如果您選擇 [星期]****，系統會顯示 [重複頻率]**** 選項。 選擇 [第一週]****、[第二週]****、[第三週]****、[第四週]**** 或 [最後一週]****。 最後，請選擇重複執行的日期。

       ![每月第一天、第十五天和最後一天的每月時程表](../media/schedules/monthly-first-fifteenth-last.png)

5. 完成後，按一下 [建立]****。

### <a name="create-a-new-schedule-with-powershell"></a>使用 PowerShell 建立新計畫

使用[「新自動化計畫](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0)」cmdlet 創建計畫。 您必須指定排程的開始時間，以及其應該執行的頻率。 以下範例展示如何創建許多不同的計畫方案。

#### <a name="create-a-one-time-schedule"></a>建立一次性計劃

以下示例命令創建一次性計劃。

```azurepowershell-interactive
$TimeZone = ([System.TimeZoneInfo]::Local).Id
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule01" -StartTime "23:00" -OneTime -ResourceGroupName "ResourceGroup01" -TimeZone $TimeZone
```

#### <a name="create-a-recurring-schedule"></a>建立定期計劃

下面的示例演示如何創建一年每天下午 1:00 運行的定期計劃。

```azurepowershell-interactive
$StartTime = Get-Date "13:00:00"
$EndTime = $StartTime.AddYears(1)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule02" -StartTime $StartTime -ExpiryTime $EndTime -DayInterval 1 -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule"></a>建立每週定期計劃

下面的範例展示如何創建僅在工作日運行的每周計畫。

```azurepowershell-interactive
$StartTime = (Get-Date "13:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekDays = @([System.DayOfWeek]::Monday..[System.DayOfWeek]::Friday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule03" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule-for-weekends"></a>建立週末的每週定期計劃

以下範例命令演示如何創建僅在週末運行的每周計劃。

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekendDays = @([System.DayOfWeek]::Saturday,[System.DayOfWeek]::Sunday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Weekends 6PM" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekendDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-recurring-schedule-for-first-15th-and-last-days-of-the-month"></a>為每月的第一天、第 15 天和最後幾天創建定期計劃

下面的範例展示如何創建在一個月的 1、15 和最後一天運行的定期計畫。

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
New-AzAutomationSchedule -AutomationAccountName "TestAzureAuto" -Name "1st, 15th and Last" -StartTime $StartTime -DaysOfMonth @("One", "Fifteenth", "Last") -ResourceGroupName "TestAzureAuto" -MonthInterval 1
```

## <a name="linking-a-schedule-to-a-runbook"></a>將排程連結至 Runbook

Runbook 可以連結至多個排程，而排程可以有多個與其連結的 Runbook。 如果 Runbook 有參數，您可以為它們提供值。 您必須為任何必要參數提供值，並可以提供任何選擇性參數的值。 每次此排程啟動 Runbook 時會使用這些值。 您可以將相同的 Runbook 附加到另一個排程，並指定不同的參數值。

### <a name="link-a-schedule-to-a-runbook-with-the-azure-portal"></a>將計畫連結到具有 Azure 門戶的 Runbook

1. 在 Azure 門戶中,從自動化帳戶中選擇 **「流程自動化**」下的**Runbook。**
2. 按一下要排程的 Runbook 名稱。
3. 如果 Runbook 當前未連結到計畫,則提供創建新計畫或連結到現有計畫的選項。
4. 如果 Runbook 具有參數,則可以選擇 **「修改執行設定」(預設:Azure)** 選項,並顯示「參數」窗格。 您可以在此處輸入參數資訊。

### <a name="link-a-schedule-to-a-runbook-with-powershell"></a>使用 PowerShell 將計畫連結到 Runbook

使用[註冊-阿茲自動化計劃 Runbook](https://docs.microsoft.com/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook?view=azps-3.7.0) cmdlet 連結計劃。 您可以使用 Parameters 參數來指定 Runbook 參數的值。 如需如何指定參數值的詳細資訊，請參閱[在 Azure 自動化中啟動 Runbook](../automation-starting-a-runbook.md)。
下面的範例展示如何使用具有參數的 Azure 資源管理器 cmdlet 將計畫連結到 Runbook。

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
–Name $runbookName –ScheduleName $scheduleName –Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="scheduling-runbooks-to-run-more-frequently"></a>排程執行簿以更頻繁地執行

可為 Azure 自動化中的排程設定的最頻繁間隔是一小時。 如果您需要讓排程以比一小時更頻繁地頻率來執行，您有兩個選項：

* 為 Runbook 創建[Web 掛鈎](../automation-webhooks.md),並使用[Azure 邏輯應用](../../logic-apps/logic-apps-overview.md)呼叫 Webhook。 在定義計劃時,Azure邏輯應用提供了更細粒度的粒度。

* 建立四個排程，讓其每小時執行一次，且彼此全都會在相差 15 分鐘內開始。 此案例可讓 Runbook 使用不同排程每 15 分鐘執行一次。

## <a name="disabling-a-schedule"></a>停用排程

停用排程時，與其連結的任何 Runbook 無法再依該排程執行。 您可以手動停用排程，或在建立排程時為具有頻率的排程設定到期時間。 達到過期時間后,計劃將禁用。

### <a name="disable-a-schedule-from-the-azure-portal"></a>從 Azure 門戶禁用計劃

1. 在自動化帳戶中,選擇 **「共用資源**」下的 **「計畫**」。
2. 按一下排程的名稱以開啟詳細資料窗格。
3. 將 [已啟用]**** 變更為 [否]****。

> [!NOTE]
> 如果要禁用過去具有開始時間的計劃,則必須將開始日期更改為將來的時間,然後才能保存它。

### <a name="disable-a-schedule-with-powershell"></a>使用 PowerShell 關閉計畫

使用[「設定-Az自動化計畫](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationSchedule?view=azps-3.7.0)cmdlet」更改現有計畫的屬性。 要禁用計畫,請為`IsEnabled`參數指定 False。

下面的範例示範如何使用 Azure 資源管理器 cmdlet 禁用 Runbook 的計畫。

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>後續步驟

* 要開始在 Azure 自動化中使用 Runbook,請參閱[在 Azure 自動化 中啟動 Runbook。](../automation-starting-a-runbook.md)