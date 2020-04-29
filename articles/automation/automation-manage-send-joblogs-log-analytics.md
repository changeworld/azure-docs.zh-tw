---
title: 將 Azure 自動化作業資料轉送至 Azure 監視器記錄
description: 本文示範如何將作業狀態和 runbook 作業串流傳送至 Azure 監視器記錄，以提供額外的深入解析和管理。
services: automation
ms.subservice: process-automation
ms.date: 02/05/2019
ms.topic: conceptual
ms.openlocfilehash: a9f4e641e60d6cf1c481c445767422e8b4df683b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81457683"
---
# <a name="forward-job-status-and-job-streams-from-automation-to-azure-monitor-logs"></a>將作業狀態和作業串流從自動化轉送至 Azure 監視器記錄

「自動化」可以將 Runebook 工作狀態和工作資料流傳送到您的 Log Analytics 工作區。 此程序不會涉及工作區連結且完全獨立。 作業記錄和作業串流會顯示於 Azure 入口網站中，或是使用 PowerShell，針對個別作業，而這可讓您執行簡單的調查。 現在有了 Azure 監視器的記錄，您可以：

* 取得自動化作業狀態的深入解析。
* 根據您的 Runbook 作業狀態 (例如失敗或已暫止) 觸發電子郵件或警示。
* 撰寫作業串流之間的進階查詢。
* 將「自動化」帳戶之間的作業相互關聯。
* 使用自訂視圖和搜尋查詢來視覺化您的 runbook 結果、runbook 作業狀態，以及其他相關的關鍵指標或計量。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 如需有關混合式 Runbook 背景工作角色的 Az 模組安裝指示，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 針對您的自動化帳戶，您可以使用[如何更新 Azure 自動化中的 Azure PowerShell 模組](automation-update-azure-modules.md)，將模組更新為最新版本。

## <a name="prerequisites-and-deployment-considerations"></a>先決條件和部署考量

若要開始將自動化記錄傳送到 Azure 監視器記錄檔，您需要：

* [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/)的最新版本。
* Log Analytics 工作區。 如需詳細資訊，請參閱[開始使用 Azure 監視器記錄](../log-analytics/log-analytics-get-started.md)。
* Azure 自動化帳戶的資源識別碼。

使用下列命令來尋找 Azure 自動化帳戶的資源識別碼：

```powershell-interactive
# Find the ResourceId for the Automation account
Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
```

若要尋找 Log Analytics 工作區的資源識別碼，請執行下列 PowerShell 命令：

```powershell-interactive
# Find the ResourceId for the Log Analytics workspace
Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

如果您在上述命令的輸出中有一個以上的自動化帳戶或工作區，請尋找您需要設定的名稱，並複製資源識別碼的值。

1. 在 [Azure 入口網站中，從 [**自動化帳戶**] 分頁中選取您的自動化帳戶，然後選取 [**所有設定**]。 
2. 從 [**所有設定**] 分頁的 [**帳戶設定**] 底下，選取 [**屬性**]。  
3. 在 [**屬性**] 視窗中，記下如下所示的屬性。

    ![自動化帳戶屬性](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="azure-monitor-log-records"></a>Azure 監視器記錄

Azure 自動化診斷會在 Azure 監視器記錄檔中建立兩種類型的`AzureDiagnostics`記錄，並標記為。 下一節中的表格是 Azure 自動化產生的記錄範例，以及記錄搜尋結果中所顯示的資料類型。

### <a name="job-logs"></a>作業記錄

| 屬性 | 描述 |
| --- | --- |
| TimeGenerated |Runbook 作業的執行日期和時間。 |
| RunbookName_s |Runbook 的名稱。 |
| Caller_s |起始作業的呼叫端。 可能的值為電子郵件地址或排程作業的系統。 |
| Tenant_g | 識別呼叫者之租使用者的 GUID。 |
| JobId_g |識別 runbook 作業的 GUID。 |
| ResultType |Runbook 作業的狀態。 可能的值包括：<br>- New (新增)<br>-已建立<br>- Started (已啟動)<br>- Stopped (已停止)<br>- Suspended (暫止)<br>- Failed (失敗)<br>- Completed (已完成) |
| 類別 | 資料類型的分類。 對自動化來說，該值是 JobLogs。 |
| OperationName | 在 Azure 中執行的作業類型。 對自動化來說，該值是 Job。 |
| 資源 | 自動化帳戶的名稱 |
| SourceSystem | Azure 監視器記錄檔用來收集資料的系統。 此值一律適用于 azure 診斷的 Azure。 |
| ResultDescription |Runbook 作業結果狀態。 可能的值包括：<br>- Job is started (工作已啟動)<br>- Job Failed (工作失敗)<br>- Job Completed |
| CorrelationId |Runbook 作業的相互關聯 GUID。 |
| ResourceId |Runbook 的 Azure 自動化帳戶資源識別碼。 |
| SubscriptionId | 自動化帳戶的 Azure 訂用帳戶 GUID。 |
| ResourceGroup | 自動化帳戶的資源組名。 |
| ResourceProvider | 資源提供者。 值為 MICROSOFT。AUTOMATION. |
| ResourceType | 資源類型。 此值為 AUTOMATIONACCOUNTS。 |

### <a name="job-streams"></a>作業串流
| 屬性 | 描述 |
| --- | --- |
| TimeGenerated |Runbook 作業的執行日期和時間。 |
| RunbookName_s |Runbook 的名稱。 |
| Caller_s |起始作業的呼叫端。 可能的值為電子郵件地址或排程作業的系統。 |
| StreamType_s |作業串流的類型。 可能的值包括：<br>-Progress (進度)<br>- Output<br>- Warning (警告)<br>- Error (錯誤)<br>- Debug (偵錯)<br>- Verbose |
| Tenant_g | 識別呼叫者之租使用者的 GUID。 |
| JobId_g |識別 runbook 作業的 GUID。 |
| ResultType |Runbook 作業的狀態。 可能的值包括：<br>- In Progress |
| 類別 | 資料類型的分類。 對自動化來說，該值是 JobStreams。 |
| OperationName | 在 Azure 中執行的作業類型。 對自動化來說，該值是 Job。 |
| 資源 | 自動化帳戶的名稱。 |
| SourceSystem | Azure 監視器記錄檔用來收集資料的系統。 此值一律適用于 azure 診斷的 Azure。 |
| ResultDescription |包含 runbook 輸出資料流程的描述。 |
| CorrelationId |Runbook 作業的相互關聯 GUID。 |
| ResourceId |Runbook 的 Azure 自動化帳戶資源識別碼。 |
| SubscriptionId | 自動化帳戶的 Azure 訂用帳戶 GUID。 |
| ResourceGroup | 自動化帳戶的資源組名。 |
| ResourceProvider | 資源提供者。 值為 MICROSOFT。AUTOMATION. |
| ResourceType | 資源類型。 此值為 AUTOMATIONACCOUNTS。 |

## <a name="setting-up-integration-with-azure-monitor-logs"></a>設定與 Azure 監視器記錄的整合

1. 在您的電腦上，從 [開始]**** 畫面啟動 Windows PowerShell。
2. 執行下列 PowerShell 命令，並使用上一節中`[your resource ID]`的`[resource ID of the log analytics workspace]`值來編輯和的值。

   ```powershell-interactive
   $workspaceId = "[resource ID of the log analytics workspace]"
   $automationAccountId = "[resource ID of your Automation account]"

   Set-AzDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled 1
   ```

執行此腳本之後，可能需要一小時的時間，才能在新`JobLogs`的或`JobStreams`寫入的 Azure 監視器記錄中查看記錄。

若要查看記錄，請在 log analytics 記錄檔搜尋中執行下列查詢：`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>驗證組態

若要確認您的自動化帳戶正在將記錄傳送到 Log Analytics 工作區，請使用下列 PowerShell 命令，檢查是否已在自動化帳戶上正確設定診斷。

```powershell-interactive
Get-AzDiagnosticSetting -ResourceId $automationAccountId
```

在輸出中，確定：

* 在`Logs`下，的值`Enabled`為 True。
* `WorkspaceId`設定為 Log Analytics `ResourceId`工作區的值。

## <a name="viewing-automation-logs-in-azure-monitor-logs"></a>在 Azure 監視器記錄中查看自動化記錄

既然您已開始將自動化作業記錄傳送到 Azure 監視器記錄，讓我們來瞭解如何在 Azure 監視器記錄檔中使用這些記錄。

若要查看記錄，請執行下列查詢：`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>在 Runbook 工作失敗或暫停時傳送電子郵件

客戶最常要求的其中一項功能便是希望系統能在 Runbook 作業發生問題時，傳送電子郵件或文字通知。

若要建立警示規則，請從建立記錄搜尋，尋找應叫用警示的 runbook 作業記錄。 按一下 [警示]**** 按鈕，以建立並設定警示規則。

1. 從 Log Analytics 工作區的 [總覽] 頁面，按一下 [**查看記錄**]。
2. 在查詢欄位中輸入下列搜尋，為您的警示建立記錄搜尋查詢：`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")`<br><br>您也可以使用下列方式來分組 runbook 名稱：`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   如果您將來自多個「自動化」帳戶或訂用帳戶的記錄設定到您的工作區，就能依訂用帳戶或「自動化」帳戶來將警示分組。 自動化帳戶名稱可以在搜尋的`Resource`欄位中找到。 `JobLogs`
3. 若要開啟 [**建立規則**] 畫面，請按一下頁面頂端的 [**新增警示規則**]。 如需設定警示選項的詳細資訊，請參閱 [Azure 中的記錄警示](../azure-monitor/platform/alerts-unified-log.md)。

### <a name="find-all-jobs-that-have-completed-with-errors"></a>尋找所有已完成但發生錯誤的工作

除了失敗的警示，您還可以尋找 Runbook 作業發生非終止錯誤的時間。 在這些情況下，PowerShell 會產生錯誤串流，但非終止錯誤不會造成您的作業暫停或失敗。

1. 在您的 Log Analytics 工作區中，按一下 [**記錄**]。
2. 在查詢欄位中，輸入`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g`。
3. 按一下 [**搜尋**] 按鈕。

### <a name="view-job-streams-for-a-job"></a>檢視工作的工作資料流

當您要對作業進行調試時，您可能也會想要查看作業資料流程。 下列查詢會顯示單一作業具有 GUID 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0 的所有資料流：

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort by TimeGenerated asc | project ResultDescription`

### <a name="view-historical-job-status"></a>檢視歷史工作狀態

最後，您可能會想要在一段時間後將作業歷程記錄視覺化。 您可以使用此查詢來搜尋您的工作在一段時間的狀態。

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started" | summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)`
<br> ![Log Analytics 歷史工作狀態圖表](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="removing-diagnostic-settings"></a>正在移除診斷設定

若要從自動化帳戶移除診斷設定，請執行下列命令：

```powershell-interactive
$automationAccountId = "[resource ID of your Automation account]"

Remove-AzDiagnosticSetting -ResourceId $automationAccountId
```
## <a name="next-steps"></a>後續步驟

* 如需針對 Log Analytics 進行疑難排解的說明，請參閱[疑難排解 Log analytics 不再收集資料的原因](../azure-monitor/platform/manage-cost-storage.md#troubleshooting-why-log-analytics-is-no-longer-collecting-data)。
* 若要深入瞭解如何使用 Azure 監視器記錄來建立不同的搜尋查詢，並查看自動化作業記錄，請參閱[Azure 監視器記錄檔中的記錄搜尋](../log-analytics/log-analytics-log-searches.md)。
* 若要瞭解如何建立和取出 runbook 的輸出和錯誤訊息，請參閱[runbook 輸出和訊息](automation-runbook-output-and-messages.md)。
* 若要深入了解 Runbook 執行方式、如何監視 Runbook 作業，以及其他技術性詳細資料，請參閱[追蹤 Runbook 作業](automation-runbook-execution.md)。
* 若要深入瞭解 Azure 監視器記錄和資料收集來源，請參閱[在 Azure 監視器記錄中收集 Azure 儲存體資料總覽](../azure-monitor/platform/collect-azure-metrics-logs.md)。