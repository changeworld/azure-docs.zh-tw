---
title: 使用 Azure 監視器 - Azure AD 權利管理來封存與報告
description: 了解如何使用 Azure 監視器在 Azure Active Directory 權利管理中封存記錄及建立報告。
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
ms.date: 04/14/2020
ms.author: barclayn
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87bb08e08bca3a9f715590098cfaa22ce7da8017
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/22/2020
ms.locfileid: "83799513"
---
# <a name="archive-logs-and-reporting-on-azure-ad-entitlement-management-in-azure-monitor"></a>在 Azure 監視器中的 Azure AD 權利管理封存記錄和報告

Azure AD 會將稽核事件儲存在稽核記錄中最多 30 天。 不過，您可以將稽核資料路由傳送至 Azure 儲存體帳戶或是使用 Azure 監視器，藉此將稽核資料保留超過 [Azure AD 會將報告資料儲存多久？](../reports-monitoring/reference-reports-data-retention.md)中所述的預設保留期間。 接著，您可以使用活頁簿及自訂此資料的查詢和報告。


## <a name="configure-azure-ad-to-use-azure-monitor"></a>設定 Azure AD 以使用 Azure 監視器
使用 Azure 監視器活頁簿之前，您必須先設定 Azure AD，將其審核記錄的複本傳送至 Azure 監視器。

如果您要封存 Azure AD 稽核記錄，必須在 Azure 訂用帳戶中具有 Azure 監視器。 您可以在 [Azure 監視器中的 Azure AD 活動記錄](../reports-monitoring/concept-activity-logs-azure-monitor.md)中，深入閱讀關於使用 Azure 監視器的必要條件和估計成本。

**必要角色**：全域管理員

1. 以全域管理員的使用者身分登入 Azure 入口網站。請確定您可以存取包含 Azure 監視器工作區的資源群組。
 
1. 選取 **Azure Active Directory** 然後按一下左側導覽功能表中 [監視] 底下的 [診斷設定]。 檢查是否已有將稽核記錄傳送至該工作區的設定。

1. 如果尚未設定，請按一下 [新增診斷設定]。 使用[將 Azure AD 記錄與 Azure 監視器記錄進行整合](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md#send-logs-to-azure-monitor)一文中的指示，將 Azure AD 稽核記錄傳送至 Azure 監視器工作區。

    ![診斷設定窗格](./media/entitlement-management-logs-and-reporting/audit-log-diagnostics-settings.png)


1. 將記錄傳送至 Azure 監視器之後，請選取 [Log Analytics 工作區]，然後選取包含 Azure AD 稽核記錄的工作區。

1. 選取 [使用量和估計成本] 然後按一下 [資料保留]。 將滑杆變更為您想要保留資料的天數，以符合您的審核需求。

    ![Log Analytics 工作區窗格](./media/entitlement-management-logs-and-reporting/log-analytics-workspaces.png)

1. 稍後，若要查看工作區中保留的日期範圍，您可以使用「封存的記錄日期範圍」活頁簿：  
    
    1. 選取 [Azure Active Directory] 然後按一下 [活頁簿]。 
    
    1. 展開 [Azure Active Directory 疑難排解] 區段，然後按一下 [封存的記錄日期範圍]。 


## <a name="view-events-for-an-access-package"></a>檢視存取套件的事件  

若要檢視存取套件的事件，您必須具有基礎 Azure 監視器工作區的存取權 (請參閱[管理 Azure 監視器中的記錄資料和工作區存取權](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#manage-access-using-azure-permissions)以取得資訊)，並具有下列其中一個角色： 

- 全域管理員  
- 安全性系統管理員  
- 安全性讀取者  
- 報告讀取者  
- 應用程式管理員  

使用下列程序來檢視事件： 

1. 在 Azure 入口網站中選取 [Azure Active Directory]，然後按一下 [活頁簿]。 如果您只有一個訂用帳戶，請移至步驟 3。 

1. 如果您有多個訂用帳戶，請選取包含工作區的訂用帳戶。  

1. 選取名為「存取套件活動」的活頁簿。 

1. 在該活頁簿中，選取 [時間範圍] (如果不確定請變更為 [所有])，然後從該時間範圍期間有活動的所有存取套件下拉式清單中，選取存取套件識別碼。 將會顯示與所選時間範圍期間所發生之存取套件相關的事件。  

    ![檢視存取套件事件](./media/entitlement-management-logs-and-reporting/view-events-access-package.png) 

    每個資料列都包含時間、存取套件識別碼、作業名稱、物件識別碼、UPN，以及啟動作業之使用者的顯示名稱。  JSON 中包含其他詳細資料。   


## <a name="create-custom-azure-monitor-queries-using-the-azure-portal"></a>使用 Azure 入口網站建立自訂 Azure 監視器查詢
您可以針對 Azure AD 稽核事件 (包括權利管理事件) 建立自己的查詢。  

1. 在 Azure 入口網站的 Azure Active Directory 中，按一下左側導覽功能表中 [監視] 區段下的 [記錄]，以建立新的查詢頁面。

1. 您的工作區應該會顯示在 [查詢] 頁面的左上方。 如果您有多個 Azure 監視器工作區，且您用來儲存 Azure AD 稽核事件的工作區未顯示，請按一下 [選取範圍]。 然後，選取正確的訂用帳戶和工作區。

1. 接下來，在 [查詢文字] 區域中，刪除字串「search *」，並將其取代為下列查詢：

    ```
    AuditLogs | where Category == "EntitlementManagement"
    ```

1. 然後按一下 [執行]。 

    ![按一下 [執行] 以啟動查詢](./media/entitlement-management-logs-and-reporting/run-query.png)

根據預設，資料表會顯示最後一個小時內權利管理的審核記錄事件。 您可以變更 [時間範圍] 設定來檢視較舊的事件。 不過，變更此設定將只會顯示在 Azure AD 設定為將事件傳送至 Azure 監視器之後發生的事件。

如果您想要知道 Azure 監視器中保留的最舊和最新稽核事件，請使用下列查詢：

```
AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type
```

如需 Azure 監視器中針對稽核事件儲存之資料行的詳細資訊，請參閱[解譯 Azure 監視器中的 Azure AD 稽核記錄架構](../reports-monitoring/reference-azure-monitor-audit-log-schema.md)。

## <a name="create-custom-azure-monitor-queries-using-azure-powershell"></a>使用 Azure PowerShell 建立自訂 Azure 監視器查詢

設定 Azure AD 以將記錄傳送至 Azure 監視器之後，您可以透過 PowerShell 存取記錄。 然後，從指令碼或 PowerShell 命令列傳送查詢，而不需要成為租用戶中的全域管理員。 

### <a name="ensure-the-user-or-service-principal-has-the-correct-role-assignment"></a>確定使用者或服務主體具有正確的角色指派

請確定您 (將向 Azure AD 進行驗證的使用者或服務主體) 位於 Log Analytics 工作區中的適當 Azure 角色。 角色選項為 Log Analytics 讀者或 Log Analytics 參與者。 如果您已經是這些角色的其中一個，請跳到[使用一個 Azure 訂用帳戶來擷取 Log Analytics 識別碼](#retrieve-log-analytics-id-with-one-azure-subscription)。

若要設定角色指派和建立查詢，請執行下列步驟：

1. 在 Azure 入口網站中，找出 [Log Analytics 工作區](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces
)。

1. 選取 [存取控制 (IAM)]。

1. 然後按一下 [新增] 以新增角色指派。

    ![新增角色指派](./media/entitlement-management-logs-and-reporting/workspace-set-role-assignment.png)

### <a name="install-azure-powershell-module"></a>安裝 Azure PowerShell 模組

一旦您擁有適當的角色指派後，請啟動 PowerShell，並[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps?view=azps-3.3.0) (如果您尚未這麼做)，請輸入：

```azurepowershell
install-module -Name az -allowClobber -Scope CurrentUser
```
    
現在您已準備好向 Azure AD 進行驗證，並擷取您要查詢的 Log Analytics 工作區識別碼。

### <a name="retrieve-log-analytics-id-with-one-azure-subscription"></a>使用一個 Azure 訂用帳戶擷取 Log Analytics 識別碼
如果您只有單一 Azure 訂用帳戶和單一 Log Analytics 工作區，請輸入下列內容，以向 Azure AD 進行驗證、連線至該訂用帳戶，並擷取該工作區：
 
```azurepowershell
Connect-AzAccount
$wks = Get-AzOperationalInsightsWorkspace
```
 
### <a name="retrieve-log-analytics-id-with-multiple-azure-subscriptions"></a>使用多個 Azure 訂用帳戶擷取 Log Analytics 識別碼

 [Get-AzOperationalInsightsWorkspace](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace) 一次只會在一個訂用帳戶中運作。 因此，如果您有多個 Azure 訂用帳戶，則要確定您連線的是具有包含 Azure AD 記錄的 Log Analytics 工作區。 
 
 下列 Cmdlet 會顯示訂用帳戶的清單，並尋找具有 Log Analytics 工作區的訂用帳戶識別碼：
 
```azurepowershell
Connect-AzAccount
$subs = Get-AzSubscription
$subs | ft
```
 
您可以使用 `Connect-AzAccount –Subscription $subs[0].id`之類的命令，重新驗證您的 PowerShell 工作階段，並將其與該訂用帳戶產生關聯。 若要深入了解如何從 PowerShell 向 Azure 進行驗證 (包括非互動)，請參閱[使用 Azure PowerShell 進行登入](/powershell/azure/authenticate-azureps?view=azps-3.3.0&viewFallbackFrom=azps-2.5.0
)。

如果您在該訂用帳戶中有多個 Log Analytics 工作區，則 [Get-AzOperationalInsightsWorkspace](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace) Cmdlet 會傳回工作區的清單。 接著，您可以找到具有 Azure AD 記錄的帳戶。 此 Cmdlet 所傳回的 `CustomerId` 欄位與 Log Analytics 工作區概觀中 Azure 入口網站所顯示的「工作區識別碼」值相同。
 
```powershell
$wks = Get-AzOperationalInsightsWorkspace
$wks | ft CustomerId, Name
```

### <a name="send-the-query-to-the-log-analytics-workspace"></a>將查詢傳送至 Log Analytics 工作區
最後，一旦識別出工作區之後，您就可以使用 [Invoke-AzOperationalInsightsQuery](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery?view=azps-3.3.0
)，將 Kusto 查詢傳送至該工作區。 這些查詢會以 [Kusto 查詢語言](https://docs.microsoft.com/azure/kusto/query/)撰寫。
 
例如，您可以從 Log Analytics 工作區擷取稽核事件記錄的日期範圍，並使用 PowerShell Cmdlet 來傳送類似下列的查詢：
 
```powershell
$aQuery = "AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type"
$aResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $aQuery
$aResponse.Results |ft
```

您也可以使用如下查詢來擷取權利管理事件：

```azurepowershell
$bQuery = 'AuditLogs | where Category == "EntitlementManagement"'
$bResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $Query
$bResponse.Results |ft 
```

## <a name="next-steps"></a>後續步驟：
- [使用 Azure 監視器活頁簿建立互動式報告](../../azure-monitor/platform/workbooks-overview.md) 

