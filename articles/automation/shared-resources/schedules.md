---
title: 管理 Azure 自動化中的排程
description: 自動化排程是用來排程讓 Azure 自動化中的 Runbook 自動啟動。 描述如何建立和管理排程，以便以特定時間或循環排程來自動啟動 Runbook。
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 17d46ddb738abc812ebfc458e25c745b84a29c2a
ms.sourcegitcommit: 1ed0230c48656d0e5c72a502bfb4f53b8a774ef1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2020
ms.locfileid: "82136595"
---
# <a name="manage-schedules-in-azure-automation"></a>管理 Azure 自動化中的排程

若要排程在指定時間啟動 Azure 自動化中的 Runbook，您會將它連結到一個或多個排程。 您可以在 Azure 入口網站中，將 Runbook 排程設定成執行一次，或是每小時或每天重複發生。 您也可以將其排定為在每週、每月、特定的星期幾、月份中的某些日子或月份中的特定日子執行。 Runbook 可以連結至多個排程，而排程可以有多個與其連結的 Runbook。

> [!NOTE]
> 排程目前不支援 Azure Automation DSC 組態。

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 如需混合式 Runbook 背景工作角色上的 Az module 安裝指示，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 針對您的自動化帳戶，您可以使用[如何更新 Azure 自動化中的 Azure PowerShell 模組](../automation-update-azure-modules.md)，將模組更新為最新版本。

## <a name="powershell-cmdlets"></a>PowerShell Cmdlet

下表中的 Cmdlet 是用來在 Azure 自動化中使用 PowerShell 建立和管理排程。 它們是隨著 [Azure PowerShell 模組](/powershell/azure/overview)的一部分推出。

| 指令程式 | 說明 |
|:--- |:--- |
| [AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationSchedule?view=azps-3.7.0) |擷取排程。 |
| [AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationscheduledrunbook?view=azps-3.7.0) |擷取排程的 Runbook。 |
| [新增-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) |建立新排程。 |
| [註冊-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook?view=azps-3.7.0) |將 Runbook 與排程相關聯。 |
| [移除-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationSchedule?view=azps-3.7.0) |移除排程。 |
| [設定-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationSchedule?view=azps-3.7.0) |設定現有排程的屬性。 |
| [取消註冊-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Unregister-AzAutomationScheduledRunbook?view=azps-3.7.0) |從排程分離 Runbook。 |

## <a name="creating-a-schedule"></a>建立排程

您可以在 Azure 入口網站中或使用 PowerShell 為 runbook 建立新的排程。

> [!NOTE]
> 執行新的排程工作時，Azure 自動化會使用自動化帳戶中的最新模組。  為了避免影響 Runbook 和它們所自動化的處理序，您應該先使用專用於測試的自動化帳戶測試任何具有連結排程的 Runbook。  這會驗證您排程的 Runbook 會持續正確運作，否則，您可以進一步進行疑難排解，並套用任何必要變更，再將已更新的 Runbook 版本移轉至生產環境。
> 除非您已選取 [模組]**** 中的[更新 Azure 模組](../automation-update-azure-modules.md)選項進行手動更新，否則自動化帳戶不會自動取得任何新版本的模組。

### <a name="create-a-new-schedule-in-the-azure-portal"></a>在 Azure 入口網站中建立新的排程

1. 在 Azure 入口網站中，從您的自動化**帳戶選取左側**[**共用資源**] 區段底下的 [排程]。
2. 在分頁的頂端按一下 [加入排程]****。
3. 在 [新增排程] 窗格中，輸入新排程的名稱和選擇性的描述。
4. 透過選取 [一次]**** 或 [定期]****，以決定排程僅執行一次或定期執行。 如果您選取 [**一次**]，請指定 [開始時間]，然後按一下 [**建立**]。 如果您選取 [**重複**執行]，請指定 [開始時間]。 針對 [**重複**頻率]，選取您想要 runbook 重複的頻率-依小時、日、周或月。
    1. 如果您選取 [**周**]，則會顯示一周的哪幾天供您選擇。 選取您需要的天數。 首次執行排程的時間將會是開始時間後所選的第一天。 例如，若要選擇週末排程，請選取 [星期六] 和 [星期日]。 
    
       ![設定週末週期性排程](../media/schedules/week-end-weekly-recurrence.png)

    2. 如果您選取 [**月**]，則會提供不同的選項。 針對 [**每月發生次數**] 選項，選取 [**月數**] 或 [**周**]。 如果您選擇 [**月日**]，則會顯示一個行事曆，讓您可以選擇所需的天數。 如果您選擇的日期（例如當月未發生的31日），則不會執行此排程。 如果您希望在最後一天執行排程，請在 [在每月最後一天執行]**** 下選取 [是]****。 如果您選擇 [星期]****，系統會顯示 [重複頻率]**** 選項。 選擇 [第一週]****、[第二週]****、[第三週]****、[第四週]**** 或 [最後一週]****。 最後，請選擇重複執行的日期。

       ![每月首次、第十五和最後一天的排程](../media/schedules/monthly-first-fifteenth-last.png)

5. 完成後，按一下 [建立]****。

### <a name="create-a-new-schedule-with-powershell"></a>使用 PowerShell 建立新排程

使用[AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) Cmdlet 來建立排程。 您必須指定排程的開始時間，以及其應該執行的頻率。 下列範例示範如何建立許多不同的排程案例。

#### <a name="create-a-one-time-schedule"></a>建立一次性排程

下列範例命令會建立一次排程。

```azurepowershell-interactive
$TimeZone = ([System.TimeZoneInfo]::Local).Id
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule01" -StartTime "23:00" -OneTime -ResourceGroupName "ResourceGroup01" -TimeZone $TimeZone
```

#### <a name="create-a-recurring-schedule"></a>建立週期性排程

下列範例示範如何建立每日執行的週期性排程，每一年執行1：00。

```azurepowershell-interactive
$StartTime = Get-Date "13:00:00"
$EndTime = $StartTime.AddYears(1)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule02" -StartTime $StartTime -ExpiryTime $EndTime -DayInterval 1 -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule"></a>建立每週週期性排程

下列範例顯示如何建立只在工作日執行的每週排程。

```azurepowershell-interactive
$StartTime = (Get-Date "13:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekDays = @([System.DayOfWeek]::Monday..[System.DayOfWeek]::Friday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule03" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule-for-weekends"></a>為週末建立每週重複排程

下列範例命令顯示如何建立僅在週末執行的每週排程。

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekendDays = @([System.DayOfWeek]::Saturday,[System.DayOfWeek]::Sunday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Weekends 6PM" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekendDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-recurring-schedule-for-first-15th-and-last-days-of-the-month"></a>建立每月第一、15和最後幾天的週期性排程

下列範例示範如何建立在一個月的第1、15和最後一天執行的週期性排程。

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
New-AzAutomationSchedule -AutomationAccountName "TestAzureAuto" -Name "1st, 15th and Last" -StartTime $StartTime -DaysOfMonth @("One", "Fifteenth", "Last") -ResourceGroupName "TestAzureAuto" -MonthInterval 1
```

## <a name="linking-a-schedule-to-a-runbook"></a>將排程連結至 Runbook

Runbook 可以連結至多個排程，而排程可以有多個與其連結的 Runbook。 如果 Runbook 有參數，您可以為它們提供值。 您必須為任何必要參數提供值，並可以提供任何選擇性參數的值。 每次此排程啟動 Runbook 時會使用這些值。 您可以將相同的 Runbook 附加到另一個排程，並指定不同的參數值。

### <a name="link-a-schedule-to-a-runbook-with-the-azure-portal"></a>使用 Azure 入口網站將排程連結至 runbook

1. 在 Azure 入口網站中，從您的自動化帳戶選取 [**進程自動化**] 底下的 [ **runbook** ]。
2. 按一下要排程的 Runbook 名稱。
3. 如果 runbook 目前未連結至排程，則會提供您建立新排程或連結至現有排程的選項。
4. 如果 runbook 有參數，您可以選取 [修改回合**設定（預設值： Azure）** ] 選項，並顯示 [參數] 窗格。 您可以在這裡輸入參數資訊。

### <a name="link-a-schedule-to-a-runbook-with-powershell"></a>使用 PowerShell 將排程連結至 runbook

使用[AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook?view=azps-3.7.0) Cmdlet 來連結排程。 您可以使用 Parameters 參數來指定 Runbook 參數的值。 如需如何指定參數值的詳細資訊，請參閱[在 Azure 自動化中啟動 Runbook](../automation-starting-a-runbook.md)。
下列範例顯示如何使用 Azure Resource Manager Cmdlet 搭配參數，將排程連結至 runbook。

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
–Name $runbookName –ScheduleName $scheduleName –Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="scheduling-runbooks-to-run-more-frequently"></a>排程 runbook 以更頻繁的執行

可為 Azure 自動化中的排程設定的最頻繁間隔是一小時。 如果您需要讓排程以比一小時更頻繁地頻率來執行，您有兩個選項：

* 建立 runbook 的[webhook](../automation-webhooks.md) ，並使用[Azure Logic Apps](../../logic-apps/logic-apps-overview.md)來呼叫 webhook。 在定義排程時，Azure Logic Apps 提供更細微的資料細微性。

* 建立四個排程，讓其每小時執行一次，且彼此全都會在相差 15 分鐘內開始。 此案例可讓 Runbook 使用不同排程每 15 分鐘執行一次。

## <a name="disabling-a-schedule"></a>停用排程

停用排程時，與其連結的任何 Runbook 無法再依該排程執行。 您可以手動停用排程，或在建立排程時為具有頻率的排程設定到期時間。 一旦達到到期時間，就會停用排程。

### <a name="disable-a-schedule-from-the-azure-portal"></a>停用 Azure 入口網站的排程

1. 在您的自動化帳戶中 **，選取 [** **共用資源**] 底下的 [排程]。
2. 按一下排程的名稱以開啟詳細資料窗格。
3. 將 [已啟用]**** 變更為 [否]****。

> [!NOTE]
> 如果您想要停用具有過去時間的排程，您必須先將開始日期變更為未來的時間，然後再儲存。

### <a name="disable-a-schedule-with-powershell"></a>使用 PowerShell 停用排程

使用[AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationSchedule?view=azps-3.7.0) Cmdlet 來變更現有排程的屬性。 若要停用排程，請為`IsEnabled`參數指定 False。

下列範例顯示如何使用 Azure Resource Manager Cmdlet 來停用 runbook 的排程。

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="removing-a-schedule"></a>移除排程

當您準備好要移除排程時，可以使用 Azure 入口網站或`Remove-AzureRmAutomationSchedule` Cmdlet。 請記住，您只能移除已停用的排程，如上一節中所述。

### <a name="remove-a-schedule-using-the-azure-portal"></a>使用 Azure 入口網站移除排程

1. 在您的自動化帳戶中 **，選取 [** **共用資源**] 底下的 [排程]。
2. 按一下排程的名稱以開啟詳細資料窗格。
3. 按一下 **[刪除]** 。

### <a name="remove-a-schedule-with-powershell"></a>使用 PowerShell 移除排程

您可以使用[AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationSchedule?view=azps-3.7.0) Cmdlet 來刪除現有的排程。 

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Remove-AzAutomationSchedule -AutomationAccountName $automationAccountName `
-Name $scheduleName -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>後續步驟

* 若要開始在 Azure 自動化中使用 runbook，請參閱[Azure 自動化中的啟動 runbook](../automation-starting-a-runbook.md)。