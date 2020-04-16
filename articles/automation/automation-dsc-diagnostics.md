---
title: 將 Azure 自動化狀態設定報告資料轉寄到 Azure 監視器紀錄
description: 本文演示如何將所需狀態配置 (DSC) 報告數據從 Azure 自動化狀態設定發送到 Azure 監視器日誌以提供其他見解和管理。
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: badd8ba676ef25c33a5034bb04d616faeb4ef1b0
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392105"
---
# <a name="forward-azure-automation-state-configuration-reporting-data-to-azure-monitor-logs"></a>將 Azure 自動化狀態設定報告資料轉寄到 Azure 監視器紀錄

Azure 自動化狀態配置保留節點狀態數據 30 天。 如果您希望將此數據保留更長時間,則可以將節點狀態數據發送到日誌分析工作區。 節點以及節點組態中個別 DSC 資源的合規性狀態會顯示在 Azure 入口網站中或使用 PowerShell 顯示。 

Azure 監視器日誌為自動化狀態配置數據提供了更高的操作可見性,並有助於更快地處理事件。 使用 Azure 監視器紀錄,您可以:

- 獲取託管節點和單個資源的合規性資訊。
- 根據合規性狀態觸發電子郵件或警報。
- 跨託管節點編寫高級查詢。
- 關聯自動化帳戶的合規性狀態。
- 使用自定義檢視和搜索查詢來可視化 Runbook 結果、Runbook 作業狀態以及其他相關關鍵指標或指標。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有關混合 Runbook 輔助角色上的 Az 模組安裝說明,請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 對於自動化帳戶,可以使用[「如何更新 Azure 自動化 中的 Azure PowerShell」模組](automation-update-azure-modules.md)將模組更新到最新版本。

## <a name="prerequisites"></a>Prerequisites

要開始將自動化狀態設定報告傳送到 Azure 監視器紀錄,您需要:

- 2016 年 11 月或更新版本的 [Azure PowerShell](/powershell/azure/overview) (v2.3.0)。
- Azure 自動化帳戶。 有關詳細資訊,請參閱[Azure 自動化 簡介](automation-intro.md)。
- 具有自動化&控制服務服務的日誌分析工作區。 如需詳細資訊，請參閱[開始使用 Azure 監視器中的 Log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)。
- 至少一個 Azure Automation State Configuration 節點。 有關詳細資訊,請參閱[載入電腦,以便由 Azure 自動化狀態設定進行管理](automation-dsc-onboarding.md)。
- [xDsc診斷](https://www.powershellgallery.com/packages/xDscDiagnostics/2.7.0.0)模組,版本2.7.0.0或更高版本。 有關安裝步驟,請參閱[排除 Azure 自動化所需狀態配置的疑難排解](./troubleshoot/desired-state-configuration.md)。

## <a name="set-up-integration-with-azure-monitor-logs"></a>設定與 Azure 監視器紀錄的整合

要開始將資料從 Azure 自動化狀態設定匯入 Azure 監視器日誌,請參閱以下步驟:

1. 在 PowerShell 中登入您的 Azure 帳戶。 請參考[Azure PowerShell 登入](https://docs.microsoft.com/powershell/azure/authenticate-azureps)。
1. 通過運行以下 PowerShell cmdlet 獲取自動化帳戶的資源 ID。 如果您有多個自動化帳戶,請選擇要配置的帳戶的資源 ID。

   ```powershell
   # Find the ResourceId for the Automation account
   Get-AzResource -ResourceType 'Microsoft.Automation/automationAccounts'
   ```

1. 通過運行以下 PowerShell cmdlet 獲取日誌分析工作區的資源 ID。 如果您有多個工作區,請選擇要設定的工作區的資源 ID。

   ```powershell
   # Find the ResourceId for the Log Analytics workspace
   Get-AzResource -ResourceType 'Microsoft.OperationalInsights/workspaces'
   ```

1. 執行以下 PowerShell cmdlet,`<AutomationResourceId>`取代`<WorkspaceResourceId>`和使用`ResourceId`上述每個步驟的值。

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Category 'DscNodeStatus'
   ```

1. 如果要停止將數據從 Azure 自動化狀態設定導入 Azure 監視器日誌,請運行以下 PowerShell cmdlet。

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Category 'DscNodeStatus'
   ```

## <a name="view-the-state-configuration-logs"></a>檢視 State Configuration 記錄

為自動化狀態設定資料設定與 Azure 監視器日誌的集成後,可以通過在狀態配置 (DSC) 頁面左側窗格中的 **「監視**」部分中選擇 **「日誌**」來查看這些日誌。

![記錄](media/automation-dsc-diagnostics/automation-dsc-logs-toc-item.png)

"日誌搜索"窗格將打開,查詢區域將限定到您的自動化帳戶資源。 通過在 Azure 監視器日誌中搜索,可以搜尋狀態配置日誌以查找 DSC 操作。 DSC 操作的記錄存儲`AzureDiagnostics`在 表中。 例如,要查找不符合要求的節點,請鍵入以下查詢。

```AzureDiagnostics
| where Category == 'DscNodeStatus' 
| where OperationName contains 'DSCNodeStatusData'
| where ResultType != 'Compliant'
```

篩選詳細資訊:

* 篩選`DscNodeStatusData`以返回每個狀態配置節點的操作。
* 篩選`DscResourceStatusData`以返回應用於該資源的節點配置中調用的每個 DSC 資源的操作。 
* 篩選`DscResourceStatusData`以返回任何失敗的 DSC 資源的錯誤資訊。

要瞭解有關建構紀錄查詢以尋找資料的詳細資訊,請參閱[Azure 監視器 中的紀錄查詢概述](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)。

### <a name="send-an-email-when-a-state-configuration-compliance-check-fails"></a>State Configuration 合規性檢查失敗時傳送電子郵件

我們最常從客戶收到的其中一個問題，便是希望系統能在 DSC 設定發生問題時，傳送電子郵件或簡訊通知他們。

要建立警報規則,請首先為應呼叫警報的狀態配置報表記錄創建日誌搜索。 按下 **「新警報規則」** 按鈕以創建和設定警報規則。

1. 在日誌分析工作區概述頁中,按一下 **「日誌**」。。
1. 通過在查詢欄位中鍵入以下搜尋,為警報建立日誌搜尋查詢:`Type=AzureDiagnostics Category='DscNodeStatus' NodeName_s='DSCTEST1' OperationName='DscNodeStatusData' ResultType='Failed'`

   如果您已將來自多個自動化帳戶或訂用帳戶的記錄設定到您的工作區，就能依訂用帳戶或自動化帳戶來將警示分組。 從搜索`Resource``DscNodeStatusData`記錄中的欄位派生自動化帳戶名稱。
1. 要開啟「**建立規則」** 螢幕,請按下頁面頂部**的新警報規則**。 

關於設定警示的選項的詳細資訊,請參考[以建立警報規則](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md)。

### <a name="find-failed-dsc-resources-across-all-nodes"></a>在所有節點間尋找失敗的 DSC 資源

使用 Azure 監視器日誌的一個優點是可以跨節點搜尋失敗的檢查。 要查找已失敗的 DSC 資源的所有實體,:

1. 在日誌分析工作區概述頁上,按一下 **「日誌**」。。
1. 在查詢欄位中鍵入下列搜尋內容來為您的警示建立記錄搜尋查詢：`Type=AzureDiagnostics Category='DscNodeStatus' OperationName='DscResourceStatusData' ResultType='Failed'`。

### <a name="view-historical-dsc-node-status"></a>檢視歷程記錄 DSC 節點狀態

要隨著時間的推移可視化 DSC 節點狀態歷史記錄,可以使用此查詢:

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`

此查詢顯示節點狀態隨時間的圖表。

## <a name="azure-monitor-logs-records"></a>Azure 監視器記錄

Azure 自動化診斷在 Azure 監視器日誌中建立兩類記錄:

* 節點狀態資料`DscNodeStatusData`( )
* 資源狀態資料`DscResourceStatusData`( )

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| 屬性 | 描述 |
| --- | --- |
| TimeGenerated |執行合規性檢查的日期和時間。 |
| OperationName |`DscNodeStatusData`. |
| ResultType |指示節點是否合規的值。 |
| NodeName_s |受控節點名稱。 |
| NodeComplianceStatus_s |指定節點是否符合的狀態值。 |
| DscReportStatus |狀態值,指示合規性檢查是否成功運行。 |
| ConfigurationMode | 用於將配置應用於節點的模式。 可能的值包括： <ul><li>`ApplyOnly`:DSC 應用配置,除非將新配置推送到目標節點或從伺服器提取新配置時,否則不會執行任何進一步操作。 初始套用新的設定之後，DSC 不會檢查先前設定的狀態是否漂移。 DSC 嘗試應用配置,`ApplyOnly`直到 該配置在值生效之前成功。 </li><li>`ApplyAndMonitor`︰這是預設值。 LCM 會套用任何新的設定。 初始套用新設定之後，如果目標節點從所需狀態漂移，DSC 會在記錄中報告差異。 DSC 嘗試應用配置,`ApplyAndMonitor`直到 該配置在值生效之前成功。</li><li>`ApplyAndAutoCorrect`:DSC 應用任何新配置。 初始套用新設定之後，如果目標節點從所需狀態漂移，DSC 會在記錄中報告差異，然後重新套用目前的設定。</li></ul> |
| HostName_s | 受控節點名稱。 |
| IPAddress | 受控節點的 IPv4 位址。 |
| 類別 | `DscNodeStatus`. |
| 資源 | Azure 自動化帳戶的名稱。 |
| Tenant_g | 標識調用方的租戶的 GUID。 |
| NodeId_g | 識別受控節點的 GUID。 |
| DscReportId_g | 識別報表的 GUID。 |
| LastSeenTime_t | 上一次檢視報表的日期和時間。 |
| ReportStartTime_t | 報表開始的日期和時間。 |
| ReportEndTime_t | 報表完成的日期和時間。 |
| NumberOfResources_d | 在節點套用的設定中呼叫的 DSC 資源數目。 |
| SourceSystem | 源系統標識 Azure 監視器日誌如何收集數據。 始終`Azure`用於 Azure 診斷。 |
| ResourceId |Azure 自動化帳戶的資源標識符。 |
| ResultDescription | 此操作的資源描述。 |
| SubscriptionId | 自動化帳戶的 Azure 訂閱 ID (GUID)。 |
| ResourceGroup | 自動化帳戶的資源組的名稱。 |
| ResourceProvider | 微軟。自動化。 |
| ResourceType | 自動化帳戶。 |
| CorrelationId | 作為合規性報告的相關標識符的 GUID。 |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| 屬性 | 描述 |
| --- | --- |
| TimeGenerated |執行合規性檢查的日期和時間。 |
| OperationName |`DscResourceStatusData`.|
| ResultType |資源是否符合規範。 |
| NodeName_s |受控節點名稱。 |
| 類別 | DscNode 狀態。 |
| 資源 | Azure 自動化帳戶的名稱。 |
| Tenant_g | 標識調用方的租戶的 GUID。 |
| NodeId_g |識別受控節點的 GUID。 |
| DscReportId_g |識別報表的 GUID。 |
| DscResourceId_s |DSC 資源執行個體的名稱。 |
| DscResourceName_s |DSC 資源的名稱。 |
| DscResourceStatus_s |DSC 資源是否符合規範。 |
| DscModuleName_s |包含 DSC 資源的 PowerShell 模組名稱。 |
| DscModuleVersion_s |包含 DSC 資源的 PowerShell 模組版本。 |
| DscConfigurationName_s |節點套用的設定名稱。 |
| ErrorCode_s | 資源失敗時的錯誤代碼。 |
| ErrorMessage_s |資源失敗時的錯誤訊息。 |
| DscResourceDuration_d |DSC 資源執行的時間，以秒為單位。 |
| SourceSystem | Azure 監視器日誌如何收集數據。 始終`Azure`用於 Azure 診斷。 |
| ResourceId |Azure 自動化帳戶的標識符。 |
| ResultDescription | 此作業的描述。 |
| SubscriptionId | 自動化帳戶的 Azure 訂閱 ID (GUID)。 |
| ResourceGroup | 自動化帳戶的資源組的名稱。 |
| ResourceProvider | 微軟。自動化。 |
| ResourceType | 自動化帳戶。 |
| CorrelationId |GUID,它是合規性報告的相關 ID。 |


## <a name="next-steps"></a>後續步驟

- 有關概述,請參閱[Azure 自動化狀態設定](automation-dsc-overview.md)。
- 要開始,請參閱[開始使用 Azure 自動化狀態設定](automation-dsc-getting-started.md)。
- 要瞭解如何編譯 DSC 設定以便將它們分配給目標節點,請參閱[在 Azure 自動化狀態設定中編譯設定](automation-dsc-compile.md)。
- 有關 PowerShell cmdlet 引用,請參閱[Az.自動化](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)。
- 有關定價資訊,請參閱[Azure 自動化狀態設定定價](https://azure.microsoft.com/pricing/details/automation/)。
- 要檢視在連續部署導管中使用 Azure 自動化狀態設定的範例,請參考[Azure 自動化狀態設定進行持續部署和巧克力](automation-dsc-cd-chocolatey.md)。
- 要瞭解有關如何建構不同的搜尋查詢與使用 Azure 監視器紀錄檢視自動化狀態設定紀錄的更多資訊,請參閱[Azure 監視器紀錄中的紀錄搜尋](../log-analytics/log-analytics-log-searches.md)。
- 要瞭解有關 Azure 監視器紀錄與資料收集來源的詳細資訊,請參閱[在 Azure 監視器紀錄概述中收集 Azure 儲存資料](../azure-monitor/platform/collect-azure-metrics-logs.md)。
