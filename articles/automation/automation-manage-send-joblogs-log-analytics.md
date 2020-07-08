---
title: 將 Azure 自動化作業資料轉送至 Azure 監視器記錄
description: 本文說明如何將作業狀態和 Runbook 作業串流傳送至 Azure 監視器記錄。
services: automation
ms.subservice: process-automation
ms.date: 05/22/2020
ms.topic: conceptual
ms.openlocfilehash: ba498fe9f70664a801172a6ff3705ac41a6371ef
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.contentlocale: zh-TW
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835237"
---
# <a name="forward-azure-automation-job-data-to-azure-monitor-logs"></a>將 Azure 自動化作業資料轉送至 Azure 監視器記錄

Azure 自動化可以將 Runebook 作業狀態和作業資料流傳送到您的 Log Analytics 工作區。 此程序不會涉及工作區連結且完全獨立。 作業記錄和作業串流會顯示於 Azure 入口網站中，或是使用 PowerShell，針對個別作業，而這可讓您執行簡單的調查。 現在，使用 Azure 監視器記錄，您可以：

* 取得自動化作業狀態的深入解析。
* 根據您的 Runbook 作業狀態 (例如失敗或已暫止) 觸發電子郵件或警示。
* 撰寫作業串流之間的進階查詢。
* 將「自動化」帳戶之間的作業相互關聯。
* 使用自訂檢視和搜尋查詢，以視覺化方式檢視您的 Runbook 結果、Runbook 作業狀態，以及其他相關的關鍵指標或計量。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites-and-deployment-considerations"></a>先決條件和部署考量

若要開始將自動化記錄傳送到 Azure 監視器記錄，您需要：

* 最新版的 [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/)。
* Log Analytics 工作區。 如需詳細資訊，請參閱[開始使用 Azure 監視器記錄](../log-analytics/log-analytics-get-started.md)。
* 您「Azure 自動化」帳戶的資源識別碼。

使用下列命令尋找您「Azure 自動化」帳戶的資源識別碼：

```powershell-interactive
# Find the ResourceId for the Automation account
Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
```

若要尋找您 Log Analytics 工作區的資源識別碼，請執行下列 PowerShell 命令：

```powershell-interactive
# Find the ResourceId for the Log Analytics workspace
Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

如果您在上述命令的輸出中有一個以上的自動化帳戶或工作區，您可以執行下列動作，以尋找屬於自動化帳戶完整資源識別碼的名稱和其他相關屬性：

1. 在 **Azure 入口網站**中，從 [自動化帳戶] 頁面選取您的自動化帳戶。 
2. 在所選自動化帳戶頁面的 [帳戶設定] 下，選取 [內容]。  
3. 在 [屬性] 頁面上，記下如下所示的詳細資料。

    ![自動化帳戶屬性](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png)。

## <a name="azure-monitor-log-records"></a>Azure 監視器記錄

Azure 自動化診斷會在 Azure 監視器記錄檔中建立兩種類型的記錄，並標記為 `AzureDiagnostics`。 下一節中的表格是 Azure 自動化產生的記錄範例，以及記錄搜尋結果中所顯示的資料類型。

### <a name="job-logs"></a>作業記錄

| 屬性 | 描述 |
| --- | --- |
| TimeGenerated |Runbook 作業的執行日期和時間。 |
| RunbookName_s |Runbook 的名稱。 |
| Caller_s |起始作業的呼叫者。 可能的值為電子郵件地址或排程作業的系統。 |
| Tenant_g | 識別呼叫端租用戶的 GUID。 |
| JobId_g |識別 Runbook 作業的 GUID。 |
| ResultType |Runbook 作業的狀態。 可能的值包括：<br>- New (新增)<br>- Created (已建立)<br>- Started (已啟動)<br>- Stopped (已停止)<br>- Suspended (暫止)<br>- Failed (失敗)<br>- Completed (已完成) |
| 類別 | 資料類型的分類。 對自動化來說，該值是 JobLogs。 |
| OperationName | 在 Azure 中執行的作業類型。 對自動化來說，該值是 Job。 |
| 資源 | 自動化帳戶的名稱 |
| SourceSystem | Azure 監視器記錄檔用來收集資料的系統。 針對 Azure 診斷，該值一律為 Azure。 |
| ResultDescription |Runbook 作業的結果狀態。 可能的值包括：<br>- Job is started (工作已啟動)<br>- Job Failed (工作失敗)<br>- Job Completed |
| CorrelationId |Runbook 作業的相互關聯識別碼。 |
| ResourceId |Runbook 的 Azure 自動化帳戶資源識別碼。 |
| SubscriptionId | 自動化帳戶的 Azure 訂用帳戶 GUID。 |
| ResourceGroup | 自動化帳戶的資源群組名稱。 |
| ResourceProvider | 資源提供者。 該值是 MICROSOFT.AUTOMATION. |
| ResourceType | 資源類型。 此值為 AUTOMATIONACCOUNTS。 |

### <a name="job-streams"></a>作業串流
| 屬性 | 描述 |
| --- | --- |
| TimeGenerated |Runbook 作業的執行日期和時間。 |
| RunbookName_s |Runbook 的名稱。 |
| Caller_s |起始作業的呼叫者。 可能的值為電子郵件地址或排程作業的系統。 |
| StreamType_s |作業串流的類型。 可能的值包括：<br>-Progress (進度)<br>- Output<br>- Warning (警告)<br>- Error (錯誤)<br>- Debug (偵錯)<br>- Verbose |
| Tenant_g | 識別呼叫端租用戶的 GUID。 |
| JobId_g |識別 Runbook 作業的 GUID。 |
| ResultType |Runbook 作業的狀態。 可能的值包括：<br>- In Progress |
| 類別 | 資料類型的分類。 對自動化來說，該值是 JobStreams。 |
| OperationName | 執行的作業類型。 對自動化來說，該值是 Job。 |
| 資源 | 自動化帳戶的名稱。 |
| SourceSystem | Azure 監視器記錄檔用來收集資料的系統。 針對 Azure 診斷，該值一律為 Azure。 |
| ResultDescription |來自 Runbook 的輸出串流包含在內的描述。 |
| CorrelationId |Runbook 作業的相互關聯識別碼。 |
| ResourceId |Runbook 的 Azure 自動化帳戶資源識別碼。 |
| SubscriptionId | 自動化帳戶的 Azure 訂用帳戶 GUID。 |
| ResourceGroup | 自動化帳戶的資源群組名稱。 |
| ResourceProvider | 資源提供者。 該值是 MICROSOFT.AUTOMATION. |
| ResourceType | 資源類型。 此值為 AUTOMATIONACCOUNTS。 |

## <a name="set-up-integration-with-azure-monitor-logs"></a>設定與 Azure 監視器記錄進行的整合

1. 在您的電腦上，從 [開始] 畫面啟動 Windows PowerShell。
2. 執行下列 PowerShell 命令，然後使用先前區段的值來編輯 `$automationAccountId` 和 `$workspaceId` 的值。

   ```powershell-interactive
   $workspaceId = "resource ID of the log analytics workspace"
   $automationAccountId = "resource ID of your Automation account"

   Set-AzDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled 1
   ```

執行這個指令碼之後，可能在一小時後，您才會開始在 Azure 監視器記錄中看見寫入新 `JobLogs` 或 `JobStreams` 的記錄。

若要查看記錄，請在 Log Analytics 記錄搜尋中執行下列查詢：`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>驗證組態

若要確認您的「自動化」帳戶會將記錄傳送到 Log Analytics 工作區，請使用下列 PowerShell 命令來確認已在「自動化」帳戶上正確設定診斷。

```powershell-interactive
Get-AzDiagnosticSetting -ResourceId $automationAccountId
```

在輸出中，確定：

* 在 `Logs` 底下，`Enabled` 的值為 True。
* `WorkspaceId` 是設定為 Log Analytics 工作區的 `ResourceId` 值。

## <a name="view-automation-logs-in-azure-monitor-logs"></a>檢視 Azure 監視器記錄中的自動化記錄

既然您已經開始將「自動化」作業記錄傳送到 Azure 監視器記錄，讓我們來看看這些記錄在 Azure 監視器記錄中的運用方式。

若要查看記錄，請執行下列查詢：`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>在 Runbook 工作失敗或暫停時傳送電子郵件

下列步驟示範如何在 Azure 監視器中設定警示，以在 Runbook 工作發生錯誤時通知您。

若要建立警示規則，首先針對應叫用警示的 Runbook 工作記錄，建立記錄搜尋。 按一下 [警示] 按鈕，以建立並設定警示規則。

1. 從 [Log Analytics 工作區概觀] 頁面，按一下 [檢視記錄]。

2. 在查詢欄位中鍵入下列搜尋內容來為您的警示建立記錄搜尋查詢：`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")`<br><br>您也可以透過使用下列內容來依 RunbookName 分組：`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   如果您將來自多個「自動化」帳戶或訂用帳戶的記錄設定到您的工作區，就能依訂用帳戶或「自動化」帳戶來將警示分組。 您可以在搜尋 JobLogs 時，在 `Resource` 欄位中找到 `JobLogs` 帳戶名稱。

3. 若要開啟 [建立規則] 畫面，按一下頁面頂端的 [新增警示規則]。 如需設定警示選項的詳細資訊，請參閱 [Azure 中的記錄警示](../azure-monitor/platform/alerts-unified-log.md)。

### <a name="find-all-jobs-that-have-completed-with-errors"></a>尋找所有已完成但發生錯誤的工作

除了失敗的警示，您還可以尋找 Runbook 作業發生非終止錯誤的時間。 在這些情況下，PowerShell 會產生錯誤串流，但非終止錯誤不會造成您的作業暫止或失敗。

1. 在 Log Analytics 工作區中，按一下 [記錄]。

2. 在查詢欄位中，輸入 `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g`。

3. 按一下 [搜尋] 按鈕。

### <a name="view-job-streams-for-a-job"></a>檢視工作的工作資料流

當您在針對作業進行偵錯時，也可以深入查看作業串流。 下列查詢會顯示單一作業具有 GUID 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0 的所有資料流：

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort by TimeGenerated asc | project ResultDescription`

### <a name="view-historical-job-status"></a>檢視歷史工作狀態

最後，您可能會想以視覺化方式呈現一段時間的作業記錄。 您可以使用此查詢來搜尋您的工作在一段時間的狀態。

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started" | summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)`
<br> ![Log Analytics 歷史工作狀態圖表](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="remove-diagnostic-settings"></a>移除診斷設定

若要從自動化帳戶中移除診斷設定，請執行下列命令：

```powershell-interactive
$automationAccountId = "[resource ID of your Automation account]"

Remove-AzDiagnosticSetting -ResourceId $automationAccountId
```

## <a name="next-steps"></a>後續步驟

* 若要瞭解如何建構搜尋查詢，以及使用 Azure 監視器記錄檢閱自動化工作記錄，請參閱 [Azure 監視器記錄中的記錄搜尋](../log-analytics/log-analytics-log-searches.md)。
* 若要瞭解從 Runbook 建立及擷取輸出與錯誤訊息，請參閱[監視器 Runbook 輸出](automation-runbook-output-and-messages.md)。
* 若要深入瞭解 Runbook 執行方式、如何監視 Runbook 作業，以及其他技術性詳細資料，請參閱 [Azure 自動化中的 Runbook 執行](automation-runbook-execution.md)。
* 若要深入瞭解 Azure 監視器記錄和資料收集來源，請參閱[在 Azure 監視器記錄中收集 Azure 儲存體資料概觀](../azure-monitor/platform/collect-azure-metrics-logs.md)。
* 如需 Log Analytics 疑難排解的說明，請參閱[針對 Log Analytics 為什麼不再收集資料的問題進行疑難排解](../azure-monitor/platform/manage-cost-storage.md#troubleshooting-why-log-analytics-is-no-longer-collecting-data)。