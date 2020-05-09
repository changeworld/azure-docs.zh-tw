---
title: 將 Azure 自動化狀態設定報告資料轉送至 Azure 監視器記錄
description: 本文示範如何將 Desired State Configuration （DSC）報告資料從 Azure 自動化狀態設定傳送到 Azure 監視器記錄，以提供額外的深入解析和管理。
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 0b0ee75c39ba87503f150ffb72b7ab95aaf83999
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2020
ms.locfileid: "82996058"
---
# <a name="forward-state-configuration-reporting-data-to-azure-monitor-logs"></a>將 State Configuration 報告資料轉送至 Azure 監視器記錄

Azure 自動化狀態設定會保留節點狀態資料30天。 如果您想要將此資料保留一段較長的時間，您可以將節點狀態資料傳送至 Log Analytics 工作區。 節點以及節點組態中個別 DSC 資源的合規性狀態會顯示在 Azure 入口網站中或使用 PowerShell 顯示。 

Azure 監視器記錄可為您的自動化狀態設定資料提供更高的操作可見度，並有助於更快速地解決事件。 有了 Azure 監視器記錄，您可以：

- 取得受管理節點和個別資源的相容性資訊。
- 根據合規性狀態觸發電子郵件或警示。
- 在您的受管理節點上撰寫先進的查詢。
- 讓自動化帳戶之間的合規性狀態相互關聯。
- 使用自訂視圖和搜尋查詢來視覺化您的 runbook 結果、runbook 作業狀態，以及其他相關的關鍵指標或計量。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


## <a name="prerequisites"></a>Prerequisites

若要開始將您的自動化狀態設定報告傳送至 Azure 監視器記錄，您需要：

- 2016 年 11 月或更新版本的 [Azure PowerShell](/powershell/azure/overview) (v2.3.0)。
- Azure 自動化帳戶。 如需詳細資訊，請參閱[Azure 自動化簡介](automation-intro.md)。
- 具有自動化 & 控制服務供應專案的 Log Analytics 工作區。 如需詳細資訊，請參閱[開始使用 Azure 監視器中的 Log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)。
- 至少一個 Azure Automation State Configuration 節點。 如需詳細資訊，請參閱將[機器上架以進行管理 Azure 自動化狀態設定](automation-dsc-onboarding.md)。
- [XDscDiagnostics](https://www.powershellgallery.com/packages/xDscDiagnostics/2.7.0.0)模組，版本2.7.0.0 或更高。 如需安裝步驟，請參閱針對[Azure 自動化 Desired State Configuration 進行疑難排解](./troubleshoot/desired-state-configuration.md)。

## <a name="set-up-integration-with-azure-monitor-logs"></a>設定與 Azure 監視器記錄的整合

若要開始將資料從 Azure 自動化狀態設定匯入 Azure 監視器記錄檔中，請完成下列步驟：

1. 在 PowerShell 中登入您的 Azure 帳戶。 請參閱[使用 Azure PowerShell 登入](https://docs.microsoft.com/powershell/azure/authenticate-azureps)。
1. 藉由執行下列 PowerShell Cmdlet，取得自動化帳戶的資源識別碼。 如果您有一個以上的自動化帳戶，請選擇您想要設定之帳戶的資源識別碼。

   ```powershell
   # Find the ResourceId for the Automation account
   Get-AzResource -ResourceType 'Microsoft.Automation/automationAccounts'
   ```

1. 執行下列 PowerShell Cmdlet，以取得 Log Analytics 工作區的資源識別碼。 如果您有多個工作區，請選擇您想要設定之工作區的資源識別碼。

   ```powershell
   # Find the ResourceId for the Log Analytics workspace
   Get-AzResource -ResourceType 'Microsoft.OperationalInsights/workspaces'
   ```

1. 執行下列 PowerShell Cmdlet，並將`<AutomationResourceId>`和`<WorkspaceResourceId>`取代為`ResourceId`上述每個步驟的值。

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Category 'DscNodeStatus'
   ```

1. 如果您想要停止將資料從 Azure 自動化狀態設定匯入 Azure 監視器記錄檔，請執行下列 PowerShell Cmdlet。

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Category 'DscNodeStatus'
   ```

## <a name="view-the-state-configuration-logs"></a>檢視 State Configuration 記錄

設定與自動化狀態設定資料的 Azure 監視器記錄整合之後，您可以在 [狀態設定（DSC）] 頁面的左窗格中選取 [**監視**] 區段中的 [**記錄**]，以查看它們。

![記錄](media/automation-dsc-diagnostics/automation-dsc-logs-toc-item.png)

[記錄搜尋] 窗格隨即開啟，其中包含範圍設定為您的自動化帳戶資源的查詢區域。 您可以藉由搜尋 Azure 監視器記錄，搜尋 DSC 作業的狀態設定記錄。 DSC 作業的記錄會儲存在`AzureDiagnostics`資料表中。 例如，若要尋找不符合規範的節點，請輸入下列查詢。

```AzureDiagnostics
| where Category == 'DscNodeStatus' 
| where OperationName contains 'DSCNodeStatusData'
| where ResultType != 'Compliant'
```

篩選詳細資料：

* 篩選`DscNodeStatusData`以傳回每個狀態設定節點的作業。
* 篩選`DscResourceStatusData`以針對套用至該資源的節點設定中呼叫的每個 DSC 資源傳回作業。 
* 針對任何`DscResourceStatusData`失敗的 DSC 資源，進行篩選以傳回錯誤資訊。

若要深入瞭解如何建立記錄查詢來尋找資料，請參閱[Azure 監視器中記錄查詢的總覽](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)。

### <a name="send-an-email-when-a-state-configuration-compliance-check-fails"></a>State Configuration 合規性檢查失敗時傳送電子郵件

我們最常從客戶收到的其中一個問題，便是希望系統能在 DSC 設定發生問題時，傳送電子郵件或簡訊通知他們。

若要建立警示規則，請從建立記錄搜尋，尋找應叫用警示的狀態設定報告記錄。 按一下 [**新增警示規則**] 按鈕，以建立並設定警示規則。

1. 從 Log Analytics 工作區的 [總覽] 頁面，按一下 [**記錄**]。
1. 在查詢欄位中輸入下列搜尋，為您的警示建立記錄搜尋查詢：`Type=AzureDiagnostics Category='DscNodeStatus' NodeName_s='DSCTEST1' OperationName='DscNodeStatusData' ResultType='Failed'`

   如果您已將來自多個自動化帳戶或訂用帳戶的記錄設定到您的工作區，就能依訂用帳戶或自動化帳戶來將警示分組。 從`Resource` `DscNodeStatusData`記錄搜尋中的欄位衍生自動化帳戶名稱。
1. 若要開啟 [**建立規則**] 畫面，請按一下頁面頂端的 [**新增警示規則**]。 

如需設定警示選項的詳細資訊，請參閱[建立警示規則](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md)。

### <a name="find-failed-dsc-resources-across-all-nodes"></a>在所有節點間尋找失敗的 DSC 資源

使用 Azure 監視器記錄的優點之一，是您可以在節點之間搜尋失敗的檢查。 若要尋找所有失敗的 DSC 資源實例：

1. 在 Log Analytics 工作區的 [總覽] 頁面上，按一下 [**記錄**]。
1. 在查詢欄位中鍵入下列搜尋內容來為您的警示建立記錄搜尋查詢：`Type=AzureDiagnostics Category='DscNodeStatus' OperationName='DscResourceStatusData' ResultType='Failed'`。

### <a name="view-historical-dsc-node-status"></a>檢視歷程記錄 DSC 節點狀態

若要視覺化一段時間的 DSC 節點狀態歷程記錄，您可以使用此查詢：

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`

此查詢會顯示一段時間內節點狀態的圖表。

## <a name="azure-monitor-logs-records"></a>Azure 監視器記錄檔記錄

Azure 自動化診斷會在 Azure 監視器記錄檔中建立兩種記錄類別：

* 節點狀態資料（`DscNodeStatusData`）
* 資源狀態資料（`DscResourceStatusData`）

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| 屬性 | 描述 |
| --- | --- |
| TimeGenerated |執行合規性檢查的日期和時間。 |
| OperationName |`DscNodeStatusData`. |
| ResultType |指出節點是否符合規範的值。 |
| NodeName_s |受控節點名稱。 |
| NodeComplianceStatus_s |指定節點是否符合規範的狀態值。 |
| DscReportStatus |指出是否已成功執行合規性檢查的狀態值。 |
| ConfigurationMode | 用來將設定套用至節點的模式。 可能的值包括： <ul><li>`ApplyOnly`： DSC 會套用設定，並且不執行任何進一步的動作，除非新的設定已推送至目標節點，或從伺服器提取新設定。 初始套用新的設定之後，DSC 不會檢查先前設定的狀態是否漂移。 DSC 會嘗試套用設定，直到成功為止， `ApplyOnly`值才會生效。 </li><li>`ApplyAndMonitor`︰這是預設值。 LCM 會套用任何新的設定。 初始套用新設定之後，如果目標節點從所需狀態漂移，DSC 會在記錄中報告差異。 DSC 會嘗試套用設定，直到成功為止， `ApplyAndMonitor`值才會生效。</li><li>`ApplyAndAutoCorrect`： DSC 會套用任何新的設定。 初始套用新設定之後，如果目標節點從所需狀態漂移，DSC 會在記錄中報告差異，然後重新套用目前的設定。</li></ul> |
| HostName_s | 受控節點名稱。 |
| IPAddress | 受控節點的 IPv4 位址。 |
| 類別 | `DscNodeStatus`. |
| 資源 | Azure 自動化帳戶的名稱。 |
| Tenant_g | 識別呼叫者之租使用者的 GUID。 |
| NodeId_g | 識別受控節點的 GUID。 |
| DscReportId_g | 識別報表的 GUID。 |
| LastSeenTime_t | 上一次檢視報表的日期和時間。 |
| ReportStartTime_t | 報表開始的日期和時間。 |
| ReportEndTime_t | 報表完成的日期和時間。 |
| NumberOfResources_d | 在節點套用的設定中呼叫的 DSC 資源數目。 |
| SourceSystem | 識別 Azure 監視器記錄如何收集資料的來源系統。 一律`Azure`適用于 Azure 診斷。 |
| ResourceId |Azure 自動化帳戶的資源識別碼。 |
| ResultDescription | 這種作業的資源描述。 |
| SubscriptionId | 自動化帳戶的 Azure 訂用帳戶識別碼（GUID）。 |
| ResourceGroup | 自動化帳戶的資源組名。 |
| ResourceProvider | MICROSOFT.AUTOMATION. |
| ResourceType | AUTOMATIONACCOUNTS. |
| CorrelationId | GUID，這是符合性報告的相互關聯識別碼。 |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| 屬性 | 描述 |
| --- | --- |
| TimeGenerated |執行合規性檢查的日期和時間。 |
| OperationName |`DscResourceStatusData`.|
| ResultType |資源是否符合規範。 |
| NodeName_s |受控節點名稱。 |
| 類別 | DscNodeStatus. |
| 資源 | Azure 自動化帳戶的名稱。 |
| Tenant_g | 識別呼叫者之租使用者的 GUID。 |
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
| SourceSystem | Azure 監視器記錄檔收集資料的方式。 一律`Azure`適用于 Azure 診斷。 |
| ResourceId |Azure 自動化帳戶的識別碼。 |
| ResultDescription | 此作業的描述。 |
| SubscriptionId | 自動化帳戶的 Azure 訂用帳戶識別碼（GUID）。 |
| ResourceGroup | 自動化帳戶的資源組名。 |
| ResourceProvider | MICROSOFT.AUTOMATION. |
| ResourceType | AUTOMATIONACCOUNTS. |
| CorrelationId |符合性報告之相互關聯識別碼的 GUID。 |


## <a name="next-steps"></a>後續步驟

- 如需總覽，請參閱[Azure 自動化狀態設定](automation-dsc-overview.md)。
- 若要開始使用，請參閱[Azure 自動化狀態設定](automation-dsc-getting-started.md)使用者入門。
- 若要瞭解如何編譯 DSC 設定，讓您可以將它們指派給目標節點，請參閱[在 Azure 自動化狀態設定中編譯](automation-dsc-compile.md)設定。
- 如需 PowerShell Cmdlet 參考，請參閱 [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)。
- 如需定價資訊，請參閱[Azure 自動化狀態設定定價](https://azure.microsoft.com/pricing/details/automation/)。
- 若要查看在持續部署管線中使用 Azure 自動化狀態設定的範例，請參閱[使用 Azure 自動化狀態設定和 Chocolatey 的持續部署](automation-dsc-cd-chocolatey.md)。
- 若要深入瞭解如何使用 Azure 監視器記錄來建立不同的搜尋查詢，並查看自動化狀態設定記錄，請參閱[Azure 監視器記錄檔中的記錄搜尋](../log-analytics/log-analytics-log-searches.md)。
- 若要深入瞭解 Azure 監視器記錄和資料收集來源，請參閱[在 Azure 監視器記錄中收集 Azure 儲存體資料總覽](../azure-monitor/platform/collect-azure-metrics-logs.md)。
