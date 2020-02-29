---
title: 使用 Azure 監視器 Azure AD 的權利管理來封存 & 報告
description: 瞭解如何使用 Azure Active Directory 權利管理中的 Azure 監視器來封存記錄和建立報表。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 02/27/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: f89af42e32783de479c4302b19c0a7ddc1289bb8
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78202178"
---
# <a name="archive-logs-and-reporting-on-azure-ad-entitlement-management-in-azure-monitor"></a>Azure 監視器中的 Azure AD 權利管理封存記錄和報告

Azure AD 在 audit 記錄中儲存最多30天的 audit 事件。 不過，您可以將這些審核資料保留超過預設的保留期限（ [Azure AD 儲存報告資料的時間長度](../reports-monitoring/reference-reports-data-retention.md)所述），方法是將其路由傳送至 Azure 儲存體帳戶或使用 Azure 監視器。 接著，您可以使用活頁簿和自訂查詢以及此資料的報表。


## <a name="configure-azure-ad-to-use-azure-monitor"></a>設定 Azure AD 以使用 Azure 監視器
使用 Azure 監視器活頁簿之前，您必須先設定 Azure AD，將其審核記錄的複本傳送至 Azure 監視器。

封存 Azure AD audit 記錄檔時，您必須在 Azure 訂用帳戶中具有 Azure 監視器。 您可以在 Azure 監視器中的[Azure AD 活動記錄中](../reports-monitoring/concept-activity-logs-azure-monitor.md)，閱讀更多有關必要條件和估計成本 Azure 監視器的詳細資訊。

必要條件角色：全域**系統**管理員

1. 以身為全域管理員的使用者身分登入 Azure 入口網站。請確定您可以存取包含 Azure 監視器工作區的資源群組。
 
1. 選取**Azure Active Directory**然後在左側導覽功能表中，按一下 [監視] 下的 [**診斷設定**]。 檢查是否已有將 audit 記錄檔傳送至該工作區的設定。

1. 如果尚未設定，請按一下 [**新增診斷設定**]。 使用將[Azure AD 記錄與 Azure 監視器記錄整合](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md#send-logs-to-azure-monitor)一文中的指示，將 Azure AD audit 記錄檔傳送至 Azure 監視器工作區。

    ![診斷設定窗格](./media/entitlement-management-logs-and-reporting/audit-log-diagnostics-settings.png)


1. 將記錄檔傳送至 Azure 監視器之後，請選取 [ **Log Analytics 工作區**]，然後選取包含 Azure AD audit 記錄檔的工作區。

1. 選取 [**使用量和估計成本**]，然後按一下 [**資料保留期**]。 將滑杆變更為您想要保留資料的天數，以符合您的審核需求。

    ![Log Analytics 工作區窗格](./media/entitlement-management-logs-and-reporting/log-analytics-workspaces.png)

## <a name="create-custom-azure-monitor-queries-using-the-azure-portal"></a>使用 Azure 入口網站建立自訂 Azure 監視器查詢
您可以針對 Azure AD 的 audit 事件建立自己的查詢，包括權利管理事件。  

1. 在 Azure 入口網站的 Azure Active Directory 中，按一下左側導覽功能表中 [監視] 區段底下的 [**記錄**]，以建立新的查詢頁面。

1. 您的工作區應該會顯示在 [查詢] 頁面的左上方。 如果您有多個 Azure 監視器工作區，而且您用來儲存 Azure AD audit 事件的工作區並未顯示，請按一下 [**選取範圍**]。 然後，選取正確的訂用帳戶和工作區。

1. 接下來，在 [查詢文字] 區域中刪除字串 "search *"，並將它取代為下列查詢：

    ```
    AuditLogs | where Category == "EntitlementManagement"
    ```

1. 然後按一下 **[執行]** 。 

    ![按一下 [執行] 以啟動查詢](./media/entitlement-management-logs-and-reporting/run-query.png)

根據預設，資料表會顯示最後一個小時內的權利管理的審核記錄事件。 您可以變更 [時間範圍] 設定來查看較舊的事件。 不過，變更此設定只會顯示在 Azure AD 設定為將事件傳送至 Azure 監視器之後發生的事件。

如果您想要知道 Azure 監視器中保留的最舊和最新的 audit 事件，請使用下列查詢：

```
AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type
```

如需 Azure 監視器中針對 audit 事件儲存之資料行的詳細資訊，請參閱[在 Azure 監視器中解讀 Azure AD audit logs 架構](../reports-monitoring/reference-azure-monitor-audit-log-schema.md)。

## <a name="create-custom-azure-monitor-queries-using-azure-powershell"></a>使用 Azure PowerShell 建立自訂 Azure 監視器查詢

設定 Azure AD 以將記錄檔傳送至 Azure 監視器之後，您可以透過 PowerShell 存取記錄。 然後，從腳本或 PowerShell 命令列傳送查詢，而不需要是租使用者中的全域管理員。 

### <a name="ensure-the-user-or-service-principal-has-the-correct-role-assignment"></a>確定使用者或服務主體具有正確的角色指派

請確定您（將向 Azure AD 驗證的使用者或服務主體）位於 Log Analytics 工作區中的適當 Azure 角色。 角色選項為 Log Analytics 讀取者或 Log Analytics 參與者。 如果您已經是這些角色的其中一個，請跳至[使用一個 Azure 訂用帳戶抓取 Log ANALYTICS 識別碼](#retrieve-log-analytics-id-with-one-azure-subscription)。

若要設定角色指派和建立查詢，請執行下列步驟：
1. 在 Azure 入口網站中，找出[Log Analytics 工作區](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces
)。

1. 選取 [存取控制 (IAM)]。

1. 然後按一下 [**新增**] 以新增角色指派。

    ![新增角色指派](./media/entitlement-management-logs-and-reporting/workspace-set-role-assignment.png)

### <a name="install-azure-powershell-module"></a>安裝 Azure PowerShell 模組

一旦您擁有適當的角色指派，請啟動 PowerShell，然後再[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps?view=azps-3.3.0)（如果尚未這麼做），方法是輸入：

```azurepowershell
install-module -Name az -allowClobber -Scope CurrentUser
```
    
現在您已準備好向 Azure AD 進行驗證，並取出您所查詢的 Log Analytics 工作區識別碼。

### <a name="retrieve-log-analytics-id-with-one-azure-subscription"></a>使用一個 Azure 訂用帳戶取出 Log Analytics 識別碼
如果您只有單一 Azure 訂用帳戶和單一 Log Analytics 工作區，請輸入下列內容以向 Azure AD 進行驗證、連線至該訂用帳戶，並取出該工作區：
 
```azurepowershell
Connect-AzAccount
$wks = Get-AzOperationalInsightsWorkspace
```
 
### <a name="retrieve-log-analytics-id-with-multiple-azure-subscriptions"></a>使用多個 Azure 訂用帳戶取出 Log Analytics 識別碼

 [AzOperationalInsightsWorkspace](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace)會一次在一個訂用帳戶中運作。 因此，如果您有多個 Azure 訂用帳戶，您會想要確定您連線的是具有 Log Analytics 工作區的 Azure AD 記錄。 
 
 下列 Cmdlet 會顯示訂用帳戶的清單，並尋找具有 Log Analytics 工作區的訂用帳戶識別碼：
 
```azurepowershell
Connect-AzAccount
$subs = Get-AzSubscription
$subs | ft
```
 
您可以使用 `Connect-AzAccount –Subscription $subs[0].id`之類的命令，重新驗證您的 PowerShell 會話，並將其與該訂用帳戶產生關聯。 若要深入瞭解如何從 PowerShell 向 Azure 進行驗證（包括非互動），請參閱[使用 Azure PowerShell 登入](/powershell/azure/authenticate-azureps?view=azps-3.3.0&viewFallbackFrom=azps-2.5.0
)。

如果您在該訂用帳戶中有多個 Log Analytics 工作區，則 Cmdlet [AzOperationalInsightsWorkspace](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace)會傳回工作區清單。 接著，您可以找到具有 Azure AD 記錄的帳戶。 此 Cmdlet 所傳回的 `CustomerId` 欄位，與 Log Analytics 工作區總覽中 Azure 入口網站所顯示的「工作區識別碼」值相同。
 
```powershell
$wks = Get-AzOperationalInsightsWorkspace
$wks | ft CustomerId, Name
```

### <a name="send-the-query-to-the-log-analytics-workspace"></a>將查詢傳送至 Log Analytics 工作區
最後，一旦您識別出工作區之後，您就可以使用[AzOperationalInsightsQuery](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery?view=azps-3.3.0
)將 Kusto 查詢傳送至該工作區。 這些查詢是以[Kusto 查詢語言](https://docs.microsoft.com/azure/kusto/query/)撰寫。
 
例如，您可以從 Log Analytics 工作區取出 audit 事件記錄的日期範圍，並使用 PowerShell Cmdlet 來傳送類似下列的查詢：
 
```powershell
$aQuery = "AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type"
$aResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $aQuery
$aResponse.Results |ft
```

您也可以使用如下的查詢來抓取權利管理事件：

```azurepowershell
$bQuery = = 'AuditLogs | where Category == "EntitlementManagement"'
$bResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $Query
$bResponse.Results |ft 
```

## <a name="next-steps"></a>後續步驟：
- [使用 Azure 監視器活頁簿建立互動式報表](../../azure-monitor/app/usage-workbooks.md) 

