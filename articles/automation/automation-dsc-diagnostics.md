---
title: 與 Azure 監視器記錄整合
description: 本文說明如何將 Desired State Configuration 報告資料，從「Azure 自動化狀態設定」傳送至 Azure 監視器記錄。
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f7e24e1b4546c76348e61e3c2736fcfe4b66410d
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.contentlocale: zh-TW
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836935"
---
# <a name="integrate-with-azure-monitor-logs"></a>與 Azure 監視器記錄整合

「Azure 自動化狀態設定」會將節點狀態資料保留 30 天。 如果想要讓節點狀態資料保留更長的時間，您可以將此資料傳送至 Log Analytics 工作區。 節點以及節點組態中個別 DSC 資源的合規性狀態會顯示在 Azure 入口網站中或使用 PowerShell 顯示。 

Azure 監視器記錄讓您在「自動化狀態設定」資料上有更高的操作可見性，有利於更快處理事件。 Azure 監視器記錄可讓您：

- 取得受控節點與個別資源的合規性資訊。
- 根據合規性狀態觸發電子郵件或警示。
- 撰寫受控節點之間的進階查詢。
- 使自動化帳戶之間的合規性狀態相互關聯。
- 使用自訂檢視和搜尋查詢，以視覺化方式呈現 Runbook 結果、Runbook 作業狀態，以及其他相關的關鍵指標或計量。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Prerequisites

若要開始將「自動化狀態設定」報告傳送至 Azure 監視器記錄，您需要：

- 2016 年 11 月或更新版本的 [Azure PowerShell](/powershell/azure/overview) (v2.3.0)。
- Azure 自動化帳戶。 如需詳細資訊，請參閱 [Azure 自動化簡介](automation-intro.md)。
- Log Analytics 工作區搭配 [自動化與控制] 服務供應項目。 如需詳細資訊，請參閱[開始使用 Azure 監視器中的 Log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)。
- 至少一個 Azure Automation State Configuration 節點。 如需詳細資訊，請參閱[讓機器上線交由「Azure 自動化狀態設定」來管理](automation-dsc-onboarding.md)。
- [xDscDiagnostics](https://www.powershellgallery.com/packages/xDscDiagnostics/2.7.0.0) 模組，2.7.0.0 版或更新版本。 關於安裝步驟，請參閱[對 Azure 自動化 Desired State Configuration 進行疑難排解](./troubleshoot/desired-state-configuration.md)。

## <a name="set-up-integration-with-azure-monitor-logs"></a>設定與 Azure 監視器記錄的整合

若要開始將資料從「Azure 自動化狀態設定」匯入 Azure 監視器記錄中，請完成下列步驟：

1. 在 PowerShell 中登入您的 Azure 帳戶。 請參閱[使用 Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps) 登入。
1. 執行下列 PowerShell Cmdlet，以取得自動化帳戶的資源識別碼。 如果您有一個以上的自動化帳戶，請選擇資源識別碼，代表您想要設定的帳戶。

   ```powershell
   # Find the ResourceId for the Automation account
   Get-AzResource -ResourceType 'Microsoft.Automation/automationAccounts'
   ```

1. 執行下列 PowerShell Cmdlet，以取得 Log Analytics 工作區的資源識別碼。 如果您有一個以上的工作區，請選擇資源識別碼，代表您想要設定的工作區。

   ```powershell
   # Find the ResourceId for the Log Analytics workspace
   Get-AzResource -ResourceType 'Microsoft.OperationalInsights/workspaces'
   ```

1. 執行下列 PowerShell 命令，以先前每個步驟的 `ResourceId` 值取代 `<AutomationResourceId>` 和 `<WorkspaceResourceId>`。

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Category 'DscNodeStatus'
   ```

1. 如果您想要停止將資料從「Azure 自動化狀態設定」匯入 Azure 監視器記錄中，請執行下列 PowerShell Cmdlet。

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Category 'DscNodeStatus'
   ```

## <a name="view-the-state-configuration-logs"></a>檢視 State Configuration 記錄

針對您的「自動化狀態設定」資料，在設定與 Azure 監視器記錄的整合之後，您可以在 [狀態設定 (DSC)] 頁面的左窗格中，在 [監視] 區段中選取 [記錄]，以檢視資料。

![記錄](media/automation-dsc-diagnostics/automation-dsc-logs-toc-item.png)

[記錄搜尋] 窗格隨即開啟，其中有一個查詢區域以您的自動化帳戶資源為範圍。 您可以在 Azure 監視器記錄中搜尋，從「狀態設定」記錄中尋找 DSC 作業。 DSC 作業的記錄儲存在 `AzureDiagnostics` 資料表中。 例如，若要尋找不符合規範的節點，請輸入下列查詢。

```AzureDiagnostics
| where Category == 'DscNodeStatus' 
| where OperationName contains 'DSCNodeStatusData'
| where ResultType != 'Compliant'
```

篩選詳細資料：

* 依 `DscNodeStatusData` 篩選，以傳回每個狀態設定節點的作業。
* 依 `DscResourceStatusData` 篩選，針對套用至該資源的節點設定中呼叫的每個 DSC 資源，傳回其作業。 
* 依 `DscResourceStatusData` 篩選，針對任何失敗的 DSC 資源，傳回錯誤資訊。

若要深入了解如何建構記錄查詢來尋找資料，請參閱 [Azure 監視器中的記錄查詢概觀](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)。

### <a name="send-an-email-when-a-state-configuration-compliance-check-fails"></a>State Configuration 合規性檢查失敗時傳送電子郵件

我們最常從客戶收到的其中一個問題，便是希望系統能在 DSC 設定發生問題時，傳送電子郵件或簡訊通知他們。

若要建立警示規則，首先，請針對應該叫用警示的「狀態設定」報告記錄，建立記錄搜尋。 按一下 [新增警示規則] 按鈕，以建立並設定警示規則。

1. 從 [Log Analytics 工作區概觀] 頁面，按一下 [記錄]。
1. 在查詢欄位中輸入下列搜尋，為您的警示建立記錄搜尋查詢：`Type=AzureDiagnostics Category='DscNodeStatus' NodeName_s='DSCTEST1' OperationName='DscNodeStatusData' ResultType='Failed'`

   如果您已將來自多個自動化帳戶或訂用帳戶的記錄設定到您的工作區，就能依訂用帳戶或自動化帳戶來將警示分組。 在搜尋 `DscNodeStatusData` 記錄時，請從 `Resource` 欄位衍生出自動化帳戶名稱。
1. 若要開啟 [建立規則] 畫面，按一下頁面頂端的 [新增警示規則]。 

如需警示設定選項的詳細資訊，請參閱[建立警示規則](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md)。

### <a name="find-failed-dsc-resources-across-all-nodes"></a>在所有節點間尋找失敗的 DSC 資源

使用 Log Analytics 的優點之一是您可以在節點之間搜尋失敗檢查。 若要尋找所有失敗的 DSC 資源執行個體：

1. 在 Log Analytics 工作區的 [概觀] 頁面上，按一下 [記錄]。
1. 在查詢欄位中鍵入下列搜尋內容來為您的警示建立記錄搜尋查詢：`Type=AzureDiagnostics Category='DscNodeStatus' OperationName='DscResourceStatusData' ResultType='Failed'`。

### <a name="view-historical-dsc-node-status"></a>檢視歷程記錄 DSC 節點狀態

若要以視覺化方式呈現一段時間的 DSC 節點狀態歷程記錄，您可以使用此查詢：

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`

此查詢會顯示一段時間內的節點狀態圖表。

## <a name="azure-monitor-logs-records"></a>Azure 監視器記錄中的記錄

Azure 自動化診斷會在 Azure 監視器記錄中建立兩種類別的記錄：

* 節點狀態資料 (`DscNodeStatusData`)
* 資源狀態名稱 (`DscResourceStatusData`)

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| 屬性 | 描述 |
| --- | --- |
| TimeGenerated |執行合規性檢查的日期和時間。 |
| OperationName |第 1 課：建立 Windows Azure 儲存體物件`DscNodeStatusData`。 |
| ResultType |此值指出節點是否符合規範。 |
| NodeName_s |受控節點名稱。 |
| NodeComplianceStatus_s |狀態值，指出節點是否符合規範。 |
| DscReportStatus |狀態值，指出是否已成功執行合規性檢查。 |
| ConfigurationMode | 將設定套用至節點時採用的模式。 可能的值包括： <ul><li>`ApplyOnly`:DSC 會套用設定但不執行任何進一步的動作，除非有新的設定發送到目標節點，或從伺服器提取新的設定時。 初始套用新的設定之後，DSC 不會檢查先前設定的狀態是否漂移。 在 `ApplyOnly` 生效之前，DSC 會一直嘗試套用設定，直到成功為止。 </li><li>`ApplyAndMonitor`:這是預設值。 LCM 會套用任何新的設定。 初始套用新設定之後，如果目標節點從所需狀態漂移，DSC 會在記錄中報告差異。 在 `ApplyAndMonitor` 生效之前，DSC 會一直嘗試套用設定，直到成功為止。</li><li>`ApplyAndAutoCorrect`:DSC 會套用任何新設定。 初始套用新設定之後，如果目標節點從所需狀態漂移，DSC 會在記錄中報告差異，然後重新套用目前的設定。</li></ul> |
| HostName_s | 受控節點名稱。 |
| IPAddress | 受控節點的 IPv4 位址。 |
| 類別 | 第 1 課：建立 Windows Azure 儲存體物件`DscNodeStatus`。 |
| 資源 | Azure 自動化帳戶的名稱。 |
| Tenant_g | GUID，用於識別呼叫者的租用戶。 |
| NodeId_g | 識別受控節點的 GUID。 |
| DscReportId_g | 識別報表的 GUID。 |
| LastSeenTime_t | 上一次檢視報表的日期和時間。 |
| ReportStartTime_t | 報表開始的日期和時間。 |
| ReportEndTime_t | 報表完成的日期和時間。 |
| NumberOfResources_d | 在節點套用的設定中呼叫的 DSC 資源數目。 |
| SourceSystem | 來源系統，識別 Azure 監視器記錄如何收集資料。 一律為 `Azure`，代表 Azure 診斷。 |
| ResourceId |Azure 自動化帳戶的資源識別碼。 |
| ResultDescription | 此作業的資源描述。 |
| SubscriptionId | 自動化帳戶的 Azure 訂用帳戶識別碼 (GUID)。 |
| ResourceGroup | 自動化帳戶的資源群組名稱。 |
| ResourceProvider | MICROSOFT.AUTOMATION。 |
| ResourceType | AUTOMATIONACCOUNTS。 |
| CorrelationId | GUID，代表合規性報告的相互關聯識別碼。 |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| 屬性 | 描述 |
| --- | --- |
| TimeGenerated |執行合規性檢查的日期和時間。 |
| OperationName |第 1 課：建立 Windows Azure 儲存體物件`DscResourceStatusData`。|
| ResultType |資源是否符合規範。 |
| NodeName_s |受控節點名稱。 |
| 類別 | DscNodeStatus。 |
| 資源 | Azure 自動化帳戶的名稱。 |
| Tenant_g | GUID，用於識別呼叫者的租用戶。 |
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
| SourceSystem | Azure 監視器記錄如何收集資料。 一律為 `Azure`，代表 Azure 診斷。 |
| ResourceId |Azure 自動化帳戶的識別碼。 |
| ResultDescription | 此作業的描述。 |
| SubscriptionId | 自動化帳戶的 Azure 訂用帳戶識別碼 (GUID)。 |
| ResourceGroup | 自動化帳戶的資源群組名稱。 |
| ResourceProvider | MICROSOFT.AUTOMATION。 |
| ResourceType | AUTOMATIONACCOUNTS。 |
| CorrelationId |GUID，代表合規性報告的相互關聯識別碼。 |

## <a name="next-steps"></a>後續步驟

- 如需概觀，請參閱 [Azure 自動化狀態設定概觀](automation-dsc-overview.md)。
- 若要開始使用，請參閱[開始使用 Azure 自動化狀態設定](automation-dsc-getting-started.md)。
- 若要了解如何編譯 DSC yrhnbe 設定以指派給目標節點，請參閱[在 Azure 自動化狀態設定中編譯 DSC 設定](automation-dsc-compile.md)。
- 如需 PowerShell Cmdlet 參考，請參閱 [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)。
- 如需價格資訊，請參閱 [Azure 自動化狀態設定的價格](https://azure.microsoft.com/pricing/details/automation/)。
- 如需在持續部署管線中使用「Azure 自動化狀態設定」的範例，請參閱[使用 Chocolatey 設定持續部署](automation-dsc-cd-chocolatey.md)。
- 若要深入了解如何建構不同的搜尋查詢，以及如何使用 Azure 監視器記錄來檢閱「自動化狀態設定」記錄，請參閱 [Azure 監視器記錄中的記錄搜尋](../log-analytics/log-analytics-log-searches.md)。
- 若要深入了解 Azure 監視器記錄和資料收集來源，請參閱[在 Azure 監視器記錄中收集 Azure 儲存體資料概觀](../azure-monitor/platform/collect-azure-metrics-logs.md)。
