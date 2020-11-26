---
title: 將 Azure 自動化作業資料轉送至 Azure 監視器記錄
description: 本文說明如何將作業狀態和 Runbook 作業串流傳送至 Azure 監視器記錄。
services: automation
ms.subservice: process-automation
ms.date: 09/02/2020
ms.topic: conceptual
ms.openlocfilehash: 2e7e798967541748b5572994d48cb5bdf7474cb1
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96182864"
---
# <a name="forward-azure-automation-job-data-to-azure-monitor-logs"></a>將 Azure 自動化作業資料轉送至 Azure 監視器記錄

Azure 自動化可以將 Runebook 作業狀態和作業資料流傳送到您的 Log Analytics 工作區。 此程序不會涉及工作區連結且完全獨立。 作業記錄和作業串流會顯示於 Azure 入口網站中，或是使用 PowerShell，針對個別作業，而這可讓您執行簡單的調查。 現在，使用 Azure 監視器記錄，您可以：

* 取得自動化作業狀態的深入解析。
* 根據您的 Runbook 作業狀態 (例如失敗或已暫止) 觸發電子郵件或警示。
* 撰寫作業串流之間的進階查詢。
* 將「自動化」帳戶之間的作業相互關聯。
* 使用自訂檢視和搜尋查詢，以視覺化方式檢視您的 Runbook 結果、Runbook 作業狀態，以及其他相關的關鍵指標或計量。

## <a name="prerequisites"></a>Prerequisites

若要開始將自動化記錄傳送到 Azure 監視器記錄，您需要：

* 最新版的 [Azure PowerShell](/powershell/azure/)。

* Log Analytics 工作區及其資源識別碼。 如需詳細資訊，請參閱[開始使用 Azure 監視器記錄](../azure-monitor/overview.md)。

* 您 Azure 自動化帳戶的資源識別碼。

## <a name="how-to-find-resource-ids"></a>如何尋找資源識別碼

1. 使用下列命令尋找您「Azure 自動化」帳戶的資源識別碼：

    ```powershell-interactive
    # Find the ResourceId for the Automation account
    Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
    ```

2. 複製 **ResourceID** 的值。

3. 使用下列命令來尋找 Log Analytics 工作區的資源識別碼：

    ```powershell-interactive
    # Find the ResourceId for the Log Analytics workspace
    Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
    ```

4. 複製 **ResourceID** 的值。

若要傳回特定資源群組的結果，請包含 `-ResourceGroupName` 參數。 如需詳細資訊，請參閱 [get-azresource](/powershell/module/az.resources/get-azresource)。

如果您在上述命令的輸出中有一個以上的自動化帳戶或工作區，您可以執行下列動作，以尋找屬於自動化帳戶完整資源識別碼的名稱和其他相關屬性：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在 **Azure 入口網站** 中，從 [自動化帳戶] 頁面選取您的自動化帳戶。
1. 在所選自動化帳戶頁面的 [帳戶設定] 下，選取 [內容]。
1. 在 [屬性] 頁面上，記下如下所示的詳細資料。

    ![自動化帳戶屬性](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="configure-diagnostic-settings"></a>設定診斷設定

自動化診斷設定支援轉送下列平臺記錄和度量資料：

* JobLogs
* JobStreams
* DSCNodeStatus
* 計量-作業總計、更新部署機器執行總數、更新部署執行總數

若要開始將自動化記錄傳送至 Azure 監視器記錄檔，請參閱 [建立診斷設定](../azure-monitor/platform/diagnostic-settings.md) 以瞭解可用來設定診斷設定以傳送平臺記錄檔的功能和方法。

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

## <a name="view-automation-logs-in-azure-monitor-logs"></a>檢視 Azure 監視器記錄中的自動化記錄

既然您已經開始將自動化工作串流和記錄傳送至 Azure 監視器記錄，讓我們看看您可以如何在 Azure 監視器記錄檔中使用這些記錄。

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

當您在針對作業進行偵錯時，也可以深入查看作業串流。 下列查詢會顯示具有 GUID 的單一作業的所有資料流程 `2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0` ：

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0"
| sort by TimeGenerated asc
| project ResultDescription
```

### <a name="view-historical-job-status"></a>檢視歷史工作狀態

最後，您可能會想以視覺化方式呈現一段時間的作業記錄。 您可以使用此查詢來搜尋您的工作在一段時間的狀態。

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started"
| summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)
```

![Log Analytics 歷史工作狀態圖表](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)

### <a name="filter-job-status-output-converted-into-a-json-object"></a>篩選作業狀態輸出轉換為 JSON 物件

最近我們已變更如何將 Automation 記錄資料寫入 `AzureDiagnostics` Log Analytics 服務中的資料表，而不再將 JSON 屬性細分為個別的欄位。 如果您將 runbook 設定為將輸出資料流程中的物件格式化為個別的資料行，則必須重新設定您的查詢，以將該欄位剖析為 JSON 物件，才能存取這些屬性。 這是使用 [parsejson](/azure/data-explorer/kusto/query/samples?pivots=#parsejson) 存取已知路徑中的特定 JSON 元素來完成。

例如，runbook 會以 JSON 格式將輸出資料流程中的 *ResultDescription* 屬性格式化為多個欄位。 若要搜尋處於 [**狀態**] 欄位中所指定之失敗狀態的作業狀態，請使用此範例查詢來搜尋狀態為 [**失敗**] 的 *ResultDescription* ：

```kusto
AzureDiagnostics
| where Category == 'JobStreams'
| extend jsonResourceDescription = parse_json(ResultDescription)
| where jsonResourceDescription.Status == 'Failed'
```

![Log Analytics 歷程記錄作業串流 JSON 格式](media/automation-manage-send-joblogs-log-analytics/job-status-format-json.png)

## <a name="next-steps"></a>後續步驟

* 若要瞭解如何建構搜尋查詢，以及使用 Azure 監視器記錄檢閱自動化工作記錄，請參閱 [Azure 監視器記錄中的記錄搜尋](../azure-monitor/log-query/log-query-overview.md)。
* 若要瞭解從 Runbook 建立及擷取輸出與錯誤訊息，請參閱[監視器 Runbook 輸出](automation-runbook-output-and-messages.md)。
* 若要深入瞭解 Runbook 執行方式、如何監視 Runbook 作業，以及其他技術性詳細資料，請參閱 [Azure 自動化中的 Runbook 執行](automation-runbook-execution.md)。
* 若要深入瞭解 Azure 監視器記錄和資料收集來源，請參閱[在 Azure 監視器記錄中收集 Azure 儲存體資料概觀](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace)。
* 如需 Log Analytics 疑難排解的說明，請參閱[針對 Log Analytics 為什麼不再收集資料的問題進行疑難排解](../azure-monitor/platform/manage-cost-storage.md#troubleshooting-why-log-analytics-is-no-longer-collecting-data)。