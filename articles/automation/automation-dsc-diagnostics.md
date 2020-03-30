---
title: 將 Azure 自動化狀態配置報告資料轉發到 Azure 監視器日誌
description: 本文演示如何將所需狀態配置 （DSC） 報告資料從 Azure 自動化狀態配置發送到 Azure 監視器日誌以提供其他見解和管理。
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 578fcf4cd03a2d4fc8400b9e84f53206750a588c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77430715"
---
# <a name="forward-azure-automation-state-configuration-reporting-data-to-azure-monitor-logs"></a>將 Azure 自動化狀態配置報告資料轉發到 Azure 監視器日誌

Azure 自動化狀態配置保留節點狀態資料 30 天。
如果您希望將此資料保留更長時間，則可以將節點狀態資料發送到日誌分析工作區。
節點以及節點組態中個別 DSC 資源的合規性狀態會顯示在 Azure 入口網站中或使用 PowerShell 顯示。
使用 Azure 監視器日誌，您可以：

- 取得受控節點與個別資源的合規性資訊
- 根據合規性狀態觸發電子郵件或警示
- 撰寫受控節點之間的進階查詢
- 相互關聯自動化帳戶之間的合規性狀態
- 以視覺化方式呈現一段時間的合規性歷程記錄

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Prerequisites

要開始將自動化狀態配置報告發送到 Azure 監視器日誌，您需要：

- 2016 年 11 月或更新版本的 [Azure PowerShell](/powershell/azure/overview) (v2.3.0)。
- Azure 自動化帳戶。 有關詳細資訊，請參閱[Azure 自動化 簡介](automation-intro.md)。
- 具有自動化&控制服務服務的日誌分析工作區。 如需詳細資訊，請參閱[開始使用 Azure 監視器中的 Log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)。
- 至少一個 Azure Automation State Configuration 節點。 有關詳細資訊，請參閱[載入電腦，以便由 Azure 自動化狀態配置進行管理](automation-dsc-onboarding.md)。
- [xDsc診斷](https://www.powershellgallery.com/packages/xDscDiagnostics/2.7.0.0)模組，版本 2.7.0.0 或更高版本。 有關安裝步驟，請參閱[排除 Azure 自動化所需狀態配置的疑難排解](./troubleshoot/desired-state-configuration.md)。

## <a name="set-up-integration-with-azure-monitor-logs"></a>設置與 Azure 監視器日誌的集成

要開始將資料從 Azure 自動化 DSC 導入 Azure 監視器日誌，請參閱以下步驟：

1. 在 PowerShell 中登入您的 Azure 帳戶。 請參閱[使用 Azure PowerShell 登錄](https://docs.microsoft.com/powershell/azure/authenticate-azureps)。
1. 通過運行以下 PowerShell Cmdlet 獲取自動化帳戶的資源識別碼。 如果您有多個自動化帳戶，請選擇要配置的帳戶的資源識別碼。

   ```powershell
   # Find the ResourceId for the Automation account
   Get-AzResource -ResourceType 'Microsoft.Automation/automationAccounts'
   ```

1. 通過運行以下 PowerShell Cmdlet 獲取日誌分析工作區的資源識別碼。 如果您有多個工作區，請選擇要配置的工作區的資源識別碼。

   ```powershell
   # Find the ResourceId for the Log Analytics workspace
   Get-AzResource -ResourceType 'Microsoft.OperationalInsights/workspaces'
   ```

1. 運行以下 PowerShell Cmdlet，`<AutomationResourceId>`替換`<WorkspaceResourceId>`和使用上述步驟中的 *"資源 Id"* 值。

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Category 'DscNodeStatus'
   ```

1. 如果要停止將資料從 Azure 自動化狀態配置導入 Azure 監視器日誌，請運行以下 PowerShell Cmdlet。

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Category 'DscNodeStatus'
   ```

## <a name="view-the-state-configuration-logs"></a>檢視 State Configuration 記錄

為自動化狀態配置資料設置與 Azure 監視器日誌的集成後，可以通過在狀態配置 （DSC） 頁面左側窗格中的 **"監視**"部分中選擇 **"日誌**"來查看這些日誌。

![記錄](media/automation-dsc-diagnostics/automation-dsc-logs-toc-item.png)

"**日誌搜索"** 窗格將打開，查詢區域將限定到您的自動化帳戶資源。 通過在 Azure 監視器日誌中搜索，可以搜索狀態配置日誌以查找 DSC 操作。 DSC 操作的記錄存儲在 Azure 診斷表中。 例如，要查找不符合要求的節點，請鍵入以下查詢。

```AzureDiagnostics
| where Category == 'DscNodeStatus' 
| where OperationName contains 'DSCNodeStatusData'
| where ResultType != 'Compliant'
```
篩選詳細資訊：

* 篩選*DscNode 狀態資料*以返回每個狀態配置節點的操作。
* 篩選*DscResourceStatusData，* 以返回應用於該資源的節點配置中調用的每個 DSC 資源的操作。 
* 篩選*DscResourceStatusData*以返回任何失敗的 DSC 資源的錯誤資訊。

要瞭解有關構造日誌查詢以查找資料的詳細資訊，請參閱[Azure 監視器 中的日誌查詢概述](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)。

### <a name="send-an-email-when-a-state-configuration-compliance-check-fails"></a>State Configuration 合規性檢查失敗時傳送電子郵件

我們最常從客戶收到的其中一個問題，便是希望系統能在 DSC 設定發生問題時，傳送電子郵件或簡訊通知他們。

要創建警報規則，請首先為應調用警報的狀態配置報表記錄創建日誌搜索。 按一下 [+ 新增警示規則]**** 按鈕，以建立並設定警示規則。

1. 在日誌分析工作區概述頁中，按一下 **"日誌**"。
1. 在查詢欄位中鍵入下列搜尋內容來為您的警示建立記錄搜尋查詢：`Type=AzureDiagnostics Category='DscNodeStatus' NodeName_s='DSCTEST1' OperationName='DscNodeStatusData' ResultType='Failed'`。

   如果您已將來自多個自動化帳戶或訂用帳戶的記錄設定到您的工作區，就能依訂用帳戶或自動化帳戶來將警示分組。 在搜索 DscNode 狀態資料時，從資源欄位派生自動化帳戶名稱。
1. 若要開啟 [建立規則]**** 畫面，按一下頁面頂端的 [+ 新增警示規則]****。 

有關配置警報的選項的詳細資訊，請參閱[創建警報規則](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md)。

### <a name="find-failed-dsc-resources-across-all-nodes"></a>在所有節點間尋找失敗的 DSC 資源

使用 Azure 監視器日誌的一個優點是可以跨節點搜索失敗的檢查。
要查找已失敗的 DSC 資源的所有實例，：

1. 在日誌分析工作區概述頁上，按一下"**日誌**"。
1. 在查詢欄位中鍵入下列搜尋內容來為您的警示建立記錄搜尋查詢：`Type=AzureDiagnostics Category='DscNodeStatus' OperationName='DscResourceStatusData' ResultType='Failed'`。

### <a name="view-historical-dsc-node-status"></a>檢視歷程記錄 DSC 節點狀態

要隨著時間的推移視覺化 DSC 節點狀態歷史記錄，可以使用此查詢：

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`

此查詢顯示節點狀態隨時間的圖表。

## <a name="azure-monitor-logs-records"></a>Azure 監視器記錄

Azure 自動化診斷在 Azure 監視器日誌中創建兩類記錄：

* 節點狀態資料（DscNode狀態資料）
* 資源狀態資料（資料資源狀態資料）

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| 屬性 | 描述 |
| --- | --- |
| TimeGenerated |執行合規性檢查的日期和時間。 |
| OperationName |DscNode 狀態資料。 |
| ResultType |節點是否符合規範。 |
| NodeName_s |受控節點名稱。 |
| NodeComplianceStatus_s |節點是否符合規範。 |
| DscReportStatus |合規性檢查是否已順利執行。 |
| ConfigurationMode | 設定如何套用至節點。 可能的值包括： <ul><li>*ApplyOnly*：DSC 會套用設定但不執行任何進一步的動作，除非有新的設定發送到目標節點，或從伺服器提取新的設定時。 初始套用新的設定之後，DSC 不會檢查先前設定的狀態是否漂移。 DSC 嘗試應用配置，直到該配置在 *"僅應用"* 值生效之前成功。 </li><li>*應用和監視器*：這是預設值。 LCM 會套用任何新的設定。 初始套用新設定之後，如果目標節點從所需狀態漂移，DSC 會在記錄中報告差異。 DSC 嘗試應用配置，直到*在應用和監視器*值生效之前成功。</li><li>*ApplyAndAutoCorrect*：DSC 會套用任何新的設定。 初始套用新設定之後，如果目標節點從所需狀態漂移，DSC 會在記錄中報告差異，然後重新套用目前的設定。</li></ul> |
| HostName_s | 受控節點名稱。 |
| IPAddress | 受控節點的 IPv4 位址。 |
| 類別 | DscNode 狀態。 |
| 資源 | Azure 自動化帳戶的名稱。 |
| Tenant_g | 識別呼叫端租用戶的 GUID。 |
| NodeId_g |識別受控節點的 GUID。 |
| DscReportId_g |識別報表的 GUID。 |
| LastSeenTime_t |上一次檢視報表的日期和時間。 |
| ReportStartTime_t |報表開始的日期和時間。 |
| ReportEndTime_t |報表完成的日期和時間。 |
| NumberOfResources_d |在節點套用的設定中呼叫的 DSC 資源數目。 |
| SourceSystem | Azure 監視器日誌如何收集資料。 Azure 診斷始終為"Azure"。 |
| ResourceId |Azure 自動化帳戶的識別碼。 |
| ResultDescription | 此作業的描述。 |
| SubscriptionId | 自動化帳戶的 Azure 訂閱 ID （GUID）。 |
| ResourceGroup | 自動化帳戶的資源群組名稱。 |
| ResourceProvider | 微軟。自動化。 |
| ResourceType | 自動化帳戶。 |
| CorrelationId |GUID，它是合規性報告的相關識別碼。 |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| 屬性 | 描述 |
| --- | --- |
| TimeGenerated |執行合規性檢查的日期和時間。 |
| OperationName |DscResourceStatusData|
| ResultType |資源是否符合規範。 |
| NodeName_s |受控節點名稱。 |
| 類別 | DscNode 狀態。 |
| 資源 | Azure 自動化帳戶的名稱。 |
| Tenant_g | 識別呼叫端租用戶的 GUID。 |
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
| SourceSystem | Azure 監視器日誌如何收集資料。 針對 Azure 診斷，一律為 Azure** 。 |
| ResourceId |指定 Azure 自動化帳戶。 |
| ResultDescription | 此作業的描述。 |
| SubscriptionId | 自動化帳戶的 Azure 訂閱 ID （GUID）。 |
| ResourceGroup | 自動化帳戶的資源群組名稱。 |
| ResourceProvider | 微軟。自動化。 |
| ResourceType | 自動化帳戶。 |
| CorrelationId |GUID，它是合規性報告的相關 ID。 |

## <a name="summary"></a>總結

通過將自動化狀態配置資料發送到 Azure 監視器日誌，可以通過以下情況更好地瞭解自動化狀態配置節點的狀態：

- 設定警示，在發生問題時通知您
- 使用自訂檢視和搜尋查詢，以視覺化方式檢視您的 Runbook 結果、Runbook 作業狀態，以及其他相關的關鍵指標或計量。

Azure 監視器日誌為自動化狀態配置資料提供了更高的操作可見度，並有助於更快地處理事件。

## <a name="next-steps"></a>後續步驟

- 如需概觀，請參閱 [Azure 自動化狀態設定](automation-dsc-overview.md)
- 若要開始使用，請參閱[開始使用 Azure 自動化狀態設定](automation-dsc-getting-started.md)
- 若要了解如何編譯 DSC 組態，以將它們指派給目標節點，請參閱[編譯 Azure Automation State Configuration 中的組態](automation-dsc-compile.md)
- 如需 PowerShell Cmdlet 參考，請參閱 [Azure 自動化狀態設定 Cmdlet](/powershell/module/azurerm.automation/#automation)
- 如需定價資訊，請參閱 [Azure 自動化狀態設定的定價](https://azure.microsoft.com/pricing/details/automation/)
- 若要查看在持續部署管線中使用 Azure 自動化狀態設定的範例，請參閱[使用 Azure 自動化狀態設定和 Chocolatey 的持續部署](automation-dsc-cd-chocolatey.md)
- 要瞭解有關如何構造不同的搜索查詢和使用 Azure 監視器日誌查看自動化狀態配置日誌的更多資訊，請參閱[Azure 監視器日誌中的日誌搜索](../log-analytics/log-analytics-log-searches.md)
- 要瞭解有關 Azure 監視器日誌和資料收集源的詳細資訊，請參閱[在 Azure 監視器日誌概述中收集 Azure 存儲資料](../azure-monitor/platform/collect-azure-metrics-logs.md)
