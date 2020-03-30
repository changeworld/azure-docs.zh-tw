---
title: 使用 Azure 監視器存檔&報表 - Azure AD 授權管理
description: 瞭解如何在 Azure 活動目錄授權管理中使用 Azure 監視器存檔日誌和創建報表。
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 070b7c5e0fef7d50f84271190432a65d29699bdf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128629"
---
# <a name="archive-logs-and-reporting-on-azure-ad-entitlement-management-in-azure-monitor"></a>Azure 監視器中的存檔日誌和 Azure AD 授權管理報告

Azure AD 在稽核記錄中存儲審核事件長達 30 天。 但是，通過將審核資料路由到 Azure 存儲帳戶或使用 Azure 監視器，可以將審核資料保留的時間超過預設保留期（Azure [AD 存儲資料多長時間）](../reports-monitoring/reference-reports-data-retention.md)中概述。 然後，您可以使用活頁簿、自訂查詢和報表來處理此資料。


## <a name="configure-azure-ad-to-use-azure-monitor"></a>將 Azure AD 配置為使用 Azure 監視器
在使用 Azure 監視器活頁簿之前，必須配置 Azure AD 以將其稽核記錄的副本發送到 Azure 監視器。

存檔 Azure AD 稽核記錄需要將 Azure 監視器保留在 Azure 訂閱中。 您可以在 Azure 監視器[中的 Azure AD 活動日誌](../reports-monitoring/concept-activity-logs-azure-monitor.md)中瞭解有關使用 Azure 監視的先決條件和估計成本。

**先決條件角色**： 全域管理員

1. 以全域管理員的使用者身份登錄到 Azure 門戶。請確保有權訪問包含 Azure 監視器工作區的資源組。
 
1. 選擇**Azure 活動目錄**，然後按一下左側導航功能表中的"監視"下的 **"診斷設置**"。 檢查是否已設置將稽核記錄發送到該工作區。

1. 如果尚未設置，請按一下"**添加診斷設置**"。 使用文章中的說明[將 Azure AD 日誌與 Azure 監視器日誌集成](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md#send-logs-to-azure-monitor)，將 Azure AD 稽核記錄發送到 Azure 監視器工作區。

    ![診斷設定窗格](./media/entitlement-management-logs-and-reporting/audit-log-diagnostics-settings.png)


1. 將日誌發送到 Azure 監視器後，選擇**日誌分析工作區**，然後選擇包含 Azure AD 稽核記錄的工作區。

1. 選擇**使用方式和估計成本**，然後按一下 **"資料保留**"。 將滑塊更改為要保留資料的天數，以滿足審核要求。

    ![日誌分析工作區窗格](./media/entitlement-management-logs-and-reporting/log-analytics-workspaces.png)

## <a name="create-custom-azure-monitor-queries-using-the-azure-portal"></a>使用 Azure 門戶創建自訂 Azure 監視器查詢
您可以在 Azure AD 審核事件（包括授權管理事件）上創建自己的查詢。  

1. 在 Azure 門戶的 Azure 活動目錄中，按一下左側導航功能表中的"監視"部分下的 **"日誌"** 以創建新的查詢頁。

1. 工作區應顯示在查詢頁的左上角。 如果有多個 Azure 監視器工作區，並且未顯示用於存儲 Azure AD 審核事件的工作區，請按一下"**選擇範圍**"。 然後，選擇正確的訂閱和工作區。

1. 接下來，在查詢文本區域中，刪除字串"search +"並將其替換為以下查詢：

    ```
    AuditLogs | where Category == "EntitlementManagement"
    ```

1. 然後按一下 **[執行]**。 

    ![按一下"運行"開始查詢](./media/entitlement-management-logs-and-reporting/run-query.png)

預設情況下，該表將顯示從最後一小時開始用於授權管理的稽核記錄事件。 您可以更改"時間範圍"設置以查看較舊的事件。 但是，更改此設置將僅顯示 Azure AD 配置為將事件發送到 Azure 監視器後發生的事件。

如果想知道 Azure 監視器中保持的最舊和最新的審核事件，請使用以下查詢：

```
AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type
```

有關為 Azure 監視器中的審核事件存儲的列的詳細資訊，請參閱在[Azure 監視器 中解釋 Azure AD 稽核記錄架構](../reports-monitoring/reference-azure-monitor-audit-log-schema.md)。

## <a name="create-custom-azure-monitor-queries-using-azure-powershell"></a>使用 Azure PowerShell 創建自訂 Azure 監視器查詢

在將 Azure AD 配置為將日誌發送到 Azure 監視器後，可以通過 PowerShell 訪問日誌。 然後，從腳本或 PowerShell 命令列發送查詢，而無需成為租戶中的全域管理員。 

### <a name="ensure-the-user-or-service-principal-has-the-correct-role-assignment"></a>確保使用者或服務主體具有正確的角色指派

請確保您是要向 Azure AD 進行身份驗證的使用者或服務主體，在日誌分析工作區中處於相應的 Azure 角色中。 角色選項是日誌分析讀取器或日誌分析參與者。 如果您已在這些角色之一中，請跳過以[使用一個 Azure 訂閱檢索日誌分析 ID。](#retrieve-log-analytics-id-with-one-azure-subscription)

要設置角色指派並創建查詢，請執行以下步驟：
1. 在 Azure 門戶中，找到[日誌分析工作區](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces
)。

1. 選擇**存取控制 （IAM）。**

1. 然後按一下"**添加**"以添加角色指派。

    ![新增角色指派](./media/entitlement-management-logs-and-reporting/workspace-set-role-assignment.png)

### <a name="install-azure-powershell-module"></a>安裝 Azure PowerShell 模組

獲得適當的角色指派後，啟動 PowerShell 並[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps?view=azps-3.3.0)（如果尚未分配），通過鍵入：

```azurepowershell
install-module -Name az -allowClobber -Scope CurrentUser
```
    
現在，您可以對 Azure AD 進行身份驗證，並檢索要查詢的日誌分析工作區的 ID。

### <a name="retrieve-log-analytics-id-with-one-azure-subscription"></a>使用一個 Azure 訂閱檢索日誌分析 ID
如果只有一個 Azure 訂閱和單個日誌分析工作區，則鍵入以下內容以驗證到 Azure AD、連接到該訂閱並檢索該工作區：
 
```azurepowershell
Connect-AzAccount
$wks = Get-AzOperationalInsightsWorkspace
```
 
### <a name="retrieve-log-analytics-id-with-multiple-azure-subscriptions"></a>使用多個 Azure 訂閱檢索日誌分析 ID

 [獲取 Az 操作見解工作區](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace)一次在一個訂閱中運行。 因此，如果您有多個 Azure 訂閱，則需要確保連接到具有 Azure AD 日誌的日誌分析工作區的訂閱。 
 
 以下 Cmdlet 顯示訂閱清單，並查找具有日誌分析工作區的訂閱 ID：
 
```azurepowershell
Connect-AzAccount
$subs = Get-AzSubscription
$subs | ft
```
 
您可以使用 命令（如`Connect-AzAccount –Subscription $subs[0].id`）重新驗證 PowerShell 會話並將其關聯到該訂閱。 要瞭解有關如何從 PowerShell 進行身份驗證（包括非互動式）的更多詳細資訊，請參閱[使用 Azure PowerShell 登錄](/powershell/azure/authenticate-azureps?view=azps-3.3.0&viewFallbackFrom=azps-2.5.0
)。

如果該訂閱中有多個日誌分析工作區，則 Cmdlet [Get-Az操作見解工作區](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace)將返回工作區清單。 然後，您可以找到具有 Azure AD 日誌的日誌。 此`CustomerId`Cmdlet 返回的欄位與日誌分析工作區概述中的 Azure 門戶中顯示的"工作區 ID"的值相同。
 
```powershell
$wks = Get-AzOperationalInsightsWorkspace
$wks | ft CustomerId, Name
```

### <a name="send-the-query-to-the-log-analytics-workspace"></a>將查詢發送到日誌分析工作區
最後，在確定工作區後，可以使用[Invoke-Az操作見解查詢](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery?view=azps-3.3.0
)向該工作區發送 Kusto 查詢。 這些查詢是用[Kusto 查詢語言](https://docs.microsoft.com/azure/kusto/query/)編寫的。
 
例如，可以從日誌分析工作區檢索審核事件記錄的日期範圍，使用 PowerShell Cmdlet 發送查詢，如下所示：
 
```powershell
$aQuery = "AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type"
$aResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $aQuery
$aResponse.Results |ft
```

您還可以使用如下查詢檢索授權管理事件：

```azurepowershell
$bQuery = = 'AuditLogs | where Category == "EntitlementManagement"'
$bResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $Query
$bResponse.Results |ft 
```

## <a name="next-steps"></a>後續步驟：
- [使用 Azure 監視器活頁簿建立互動式報表](../../azure-monitor/app/usage-workbooks.md) 

