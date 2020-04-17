---
title: 將 Azure 自動化作業資料轉送至 Azure 監視器記錄
description: 本文演示如何將作業狀態和 Runbook 作業流發送到 Azure 監視器日誌,以提供其他見解和管理。
services: automation
ms.subservice: process-automation
ms.date: 02/05/2019
ms.topic: conceptual
ms.openlocfilehash: a9f4e641e60d6cf1c481c445767422e8b4df683b
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457683"
---
# <a name="forward-job-status-and-job-streams-from-automation-to-azure-monitor-logs"></a>將作業狀態與工作串流從自動化轉寄到 Azure 監視器紀錄

「自動化」可以將 Runebook 工作狀態和工作資料流傳送到您的 Log Analytics 工作區。 此程序不會涉及工作區連結且完全獨立。 作業記錄和作業串流會顯示於 Azure 入口網站中，或是使用 PowerShell，針對個別作業，而這可讓您執行簡單的調查。 現在,使用 Azure 監視器紀錄,您可以:

* 深入瞭解自動化作業的狀態。
* 根據您的 Runbook 作業狀態 (例如失敗或已暫止) 觸發電子郵件或警示。
* 撰寫作業串流之間的進階查詢。
* 將「自動化」帳戶之間的作業相互關聯。
* 使用自定義檢視和搜索查詢來可視化 Runbook 結果、Runbook 作業狀態以及其他相關關鍵指標或指標。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有關混合 Runbook 輔助角色上的 Az 模組安裝說明,請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 對於自動化帳戶,可以使用[「如何更新 Azure 自動化 中的 Azure PowerShell」模組](automation-update-azure-modules.md)將模組更新到最新版本。

## <a name="prerequisites-and-deployment-considerations"></a>先決條件和部署考量

要開始將自動化紀錄傳送到 Azure 監視器紀錄,您需要:

* [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/)的最新版本。
* Log Analytics 工作區。 有關詳細資訊,請參閱使用[Azure 監視器日誌入門](../log-analytics/log-analytics-get-started.md)。
* Azure 自動化帳戶的資源 ID。

使用以下指令尋找 Azure 自動化帳戶的資源 ID:

```powershell-interactive
# Find the ResourceId for the Automation account
Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
```

要查找紀錄分析工作區的資源 ID,請執行以下 PowerShell 命令:

```powershell-interactive
# Find the ResourceId for the Log Analytics workspace
Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

如果上述命令的輸出中有多個自動化帳戶或工作區,請找到配置和複製資源 ID 的值所需的名稱。

1. 在 Azure 門戶中,從 **「自動化」帳戶**邊欄選項卡中選擇自動化帳戶,然後選擇 **「所有設置**」。 
2. 從「**所有設定」** 邊欄選項卡中,在 **「帳戶設定」** 下,選擇 **「屬性**」。  
3. 在 **「屬性」** 邊欄選項卡中,請注意如下所示的屬性。

    ![自動化帳戶屬性](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="azure-monitor-log-records"></a>Azure 監視器記錄

Azure 自動化診斷在 Azure 監視器紀錄中建立兩種類型`AzureDiagnostics`的記錄 ,標記為 。 下一節中的表是 Azure 自動化生成的記錄示例和日誌搜尋結果中顯示的數據類型。

### <a name="job-logs"></a>作業記錄

| 屬性 | 描述 |
| --- | --- |
| TimeGenerated |Runbook 作業的執行日期和時間。 |
| RunbookName_s |Runbook 的名稱。 |
| Caller_s |啟動操作的調用方。 可能的值為電子郵件地址或排程作業的系統。 |
| Tenant_g | 標識調用方的租戶的 GUID。 |
| JobId_g |標識 Runbook 作業的 GUID。 |
| ResultType |Runbook 作業的狀態。 可能的值包括：<br>- New (新增)<br>- 已建立<br>- Started (已啟動)<br>- Stopped (已停止)<br>- Suspended (暫止)<br>- Failed (失敗)<br>- Completed (已完成) |
| 類別 | 資料類型的分類。 對自動化來說，該值是 JobLogs。 |
| OperationName | 在 Azure 中執行的操作類型。 對自動化來說，該值是 Job。 |
| 資源 | 自動化帳戶名稱 |
| SourceSystem | Azure 監視器日誌用於收集數據的系統。 該值始終為 Azure 診斷的 Azure。 |
| ResultDescription |Runbook 作業結果狀態。 可能的值包括：<br>- Job is started (工作已啟動)<br>- Job Failed (工作失敗)<br>- Job Completed |
| CorrelationId |runbook 作業的相關 GUID。 |
| ResourceId |Runbook 的 Azure 自動化帳戶資源 ID。 |
| SubscriptionId | 自動化帳戶的 Azure 訂閱 GUID。 |
| ResourceGroup | 自動化帳戶的資源組的名稱。 |
| ResourceProvider | 資源提供者。 值是 MICROSOFT。自動化。 |
| ResourceType | 資源類型。 值為"自動化帳戶"。 |

### <a name="job-streams"></a>作業串流
| 屬性 | 描述 |
| --- | --- |
| TimeGenerated |Runbook 作業的執行日期和時間。 |
| RunbookName_s |Runbook 的名稱。 |
| Caller_s |啟動操作的調用方。 可能的值為電子郵件地址或排程作業的系統。 |
| StreamType_s |作業串流的類型。 可能的值包括：<br>-Progress (進度)<br>- Output<br>- Warning (警告)<br>- Error (錯誤)<br>- Debug (偵錯)<br>- Verbose |
| Tenant_g | 標識調用方的租戶的 GUID。 |
| JobId_g |標識 Runbook 作業的 GUID。 |
| ResultType |Runbook 作業的狀態。 可能的值包括：<br>- In Progress |
| 類別 | 資料類型的分類。 對自動化來說，該值是 JobStreams。 |
| OperationName | 在 Azure 中執行的操作類型。 對自動化來說，該值是 Job。 |
| 資源 | 自動化帳戶的名稱。 |
| SourceSystem | Azure 監視器日誌用於收集數據的系統。 該值始終為 Azure 診斷的 Azure。 |
| ResultDescription |包含 Runbook 的輸出流的說明。 |
| CorrelationId |runbook 作業的相關 GUID。 |
| ResourceId |Runbook 的 Azure 自動化帳戶資源 ID。 |
| SubscriptionId | 自動化帳戶的 Azure 訂閱 GUID。 |
| ResourceGroup | 自動化帳戶的資源組的名稱。 |
| ResourceProvider | 資源提供者。 值是 MICROSOFT。自動化。 |
| ResourceType | 資源類型。 值為"自動化帳戶"。 |

## <a name="setting-up-integration-with-azure-monitor-logs"></a>設定與 Azure 監視器紀錄的整合

1. 在您的電腦上，從 [開始]**** 畫面啟動 Windows PowerShell。
2. 運行以下 PowerShell 命令,然後使用`[your resource ID]`上`[resource ID of the log analytics workspace]`一節的值編輯 值。

   ```powershell-interactive
   $workspaceId = "[resource ID of the log analytics workspace]"
   $automationAccountId = "[resource ID of your Automation account]"

   Set-AzDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled 1
   ```

運行此文稿後,可能需要一個小時才能開始查看 Azure 監視器日誌`JobLogs`中的新`JobStreams`記錄或 正在寫入的記錄。

要檢視紀錄,請在紀錄分析紀錄搜尋中執行以下查詢:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>驗證組態

要確認您的自動化帳戶正在向日誌分析工作區發送日誌,請使用以下 PowerShell 命令檢查自動化帳戶上的診斷是否正確配置。

```powershell-interactive
Get-AzDiagnosticSetting -ResourceId $automationAccountId
```

在輸出中,確保:

* 下`Logs`的值`Enabled`為"True"。
* `WorkspaceId`設置為日誌分析工作區`ResourceId`的值。

## <a name="viewing-automation-logs-in-azure-monitor-logs"></a>在 Azure 監視器紀錄中檢視自動化紀錄

現在,您已開始將自動化作業日誌發送到 Azure 監視器日誌,讓我們看看在 Azure 監視器日誌中可以對這些日誌執行哪些操作。

若要查看記錄，請執行下列查詢：`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>在 Runbook 工作失敗或暫停時傳送電子郵件

客戶最常要求的其中一項功能便是希望系統能在 Runbook 作業發生問題時，傳送電子郵件或文字通知。

要建立警報規則,請首先為應呼叫警報的 Runbook 作業記錄創建日誌搜尋。 按一下 [警示]**** 按鈕，以建立並設定警示規則。

1. 在日誌分析工作區概述頁中,按一下「**查看日誌**」。。
2. 通過在查詢欄位中鍵入以下搜尋,為警報建立日誌搜尋查詢:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")`<br><br>您還可以使用以下功能依 Runbook 名稱進行群組:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   如果您將來自多個「自動化」帳戶或訂用帳戶的記錄設定到您的工作區，就能依訂用帳戶或「自動化」帳戶來將警示分組。 在搜索 中可以`Resource`找到自動化帳戶名稱。 `JobLogs`
3. 要開啟「**建立規則」** 螢幕,請按下頁面頂部**的新警報規則**。 如需設定警示選項的詳細資訊，請參閱 [Azure 中的記錄警示](../azure-monitor/platform/alerts-unified-log.md)。

### <a name="find-all-jobs-that-have-completed-with-errors"></a>尋找所有已完成但發生錯誤的工作

除了失敗的警示，您還可以尋找 Runbook 作業發生非終止錯誤的時間。 在這些情況下,PowerShell 會生成錯誤流,但非終止錯誤不會導致作業掛起或失敗。

1. 在日誌分析工作區中,按一下 **「日誌**」。。
2. 在查詢欄位中,鍵入`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g`。
3. 按下 **「搜索」** 按鈕。

### <a name="view-job-streams-for-a-job"></a>檢視工作的工作資料流

調試作業時,可能還需要查看作業流。 下列查詢會顯示單一作業具有 GUID 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0 的所有資料流：

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort by TimeGenerated asc | project ResultDescription`

### <a name="view-historical-job-status"></a>檢視歷史工作狀態

最後,您可能希望隨著時間的推移可視化工作歷史記錄。 您可以使用此查詢來搜尋您的工作在一段時間的狀態。

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started" | summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)`
<br> ![Log Analytics 歷史工作狀態圖表](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="removing-diagnostic-settings"></a>刪除診斷設定

要從自動化帳戶中刪除診斷設定,請執行以下命令:

```powershell-interactive
$automationAccountId = "[resource ID of your Automation account]"

Remove-AzDiagnosticSetting -ResourceId $automationAccountId
```
## <a name="next-steps"></a>後續步驟

* 有關解決日誌分析疑難的説明,請參閱[疑難排解日誌分析不再收集資料的原因](../azure-monitor/platform/manage-cost-storage.md#troubleshooting-why-log-analytics-is-no-longer-collecting-data)。
* 要瞭解有關如何建構不同的搜尋查詢與使用 Azure 監視器紀錄檢視自動化作業日誌的更多資訊,請參閱[Azure 監視器紀錄中的紀錄搜尋](../log-analytics/log-analytics-log-searches.md)。
* 若要瞭解如何從 Runbook 建立與檢索輸出和錯誤訊息,請參閱[Runbook 輸出和訊息](automation-runbook-output-and-messages.md)。
* 若要深入了解 Runbook 執行方式、如何監視 Runbook 作業，以及其他技術性詳細資料，請參閱[追蹤 Runbook 作業](automation-runbook-execution.md)。
* 要瞭解有關 Azure 監視器紀錄與資料收集來源的詳細資訊,請參閱[在 Azure 監視器紀錄概述中收集 Azure 儲存資料](../azure-monitor/platform/collect-azure-metrics-logs.md)。