---
title: 使用 Azure 監視器存檔&報表 ─ Azure AD 授權管理
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
ms.date: 04/14/2020
ms.author: barclayn
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: d59a508d03730a51e793a5e30e2c99a91af77ce8
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380176"
---
# <a name="archive-logs-and-reporting-on-azure-ad-entitlement-management-in-azure-monitor"></a>Azure 監視器中的存檔紀錄與 Azure AD 授權管理報告

Azure AD 在審核日誌中存儲審核事件長達 30 天。 但是,通過將審核數據路由到 Azure 儲存帳戶或使用 Azure 監視器,可以將審核數據保留的時間超過預設保留期(Azure [AD 存儲數據多長時間)](../reports-monitoring/reference-reports-data-retention.md)中概述。 然後,您可以使用工作簿、自定義查詢和報表來處理此數據。


## <a name="configure-azure-ad-to-use-azure-monitor"></a>將 Azure AD 設定為使用 Azure 監視器
在使用 Azure 監視器工作簿之前,必須配置 Azure AD 以將其審核日誌的副本發送到 Azure 監視器。

存檔 Azure AD 稽核紀錄需要將 Azure 監視器保留在 Azure 訂閱中。 您可以在 Azure 監視器[中的 Azure AD 活動日誌](../reports-monitoring/concept-activity-logs-azure-monitor.md)中瞭解有關使用 Azure 監視的先決條件和估計成本。

**先決條件角色**: 全域管理員

1. 以全域管理員的使用者身份登錄到 Azure 門戶。請確保有權訪問包含 Azure 監視器工作區的資源組。
 
1. 選擇**Azure 活動目錄**,然後單擊左側導航功能表中的「監視」下的 **「診斷設置**」 。 檢查是否已設置將審核日誌發送到該工作區。

1. 如果尚未設置,請單擊「**添加診斷設置**」。。 使用文章中的說明[將 Azure AD 日誌與 Azure 監視器日誌集成](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md#send-logs-to-azure-monitor),將 Azure AD 審核日誌發送到 Azure 監視器工作區。

    ![診斷設定窗格](./media/entitlement-management-logs-and-reporting/audit-log-diagnostics-settings.png)


1. 將日誌發送到 Azure 監視器後,選擇**日誌分析工作區**,然後選擇包含 Azure AD 審核紀錄的工作區。

1. 選擇**使用方式和估計成本**,然後按下 **「數據保留**」。 將滑塊更改為要保留數據的天數,以滿足審核要求。

    ![紀錄分析工作區窗格](./media/entitlement-management-logs-and-reporting/log-analytics-workspaces.png)

1. 稍後,要查看工作區中保留的日期範圍,可以使用*存檔日誌日期範圍*工作簿:  
    
    1. 選擇**Azure 活動目錄**,然後單擊**工作簿**。 
    
    1. 展開**Azure 活動目錄故障排除**部分,然後單擊 **「存檔日誌日期範圍**」。 


## <a name="view-events-for-an-access-package"></a>檢視存取套件的事件  

要檢視存取套件的事件,您必須有權存取基礎 Azure 監視器工作區(請參閱[管理 Azure 監視器中日誌資料和工作區的存取](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#manage-access-using-azure-permissions))以及以下角色之一: 

- 全域管理員  
- 安全性系統管理員  
- 安全性讀取者  
- 報告閱讀器  
- 應用程式管理員  

使用以下過程檢視事件: 

1. 在 Azure 門戶中,選擇**Azure 活動目錄**,然後單擊**工作簿**。 如果只有一個訂閱,則繼續執行步驟 3。 

1. 如果您有多個訂閱,請選擇包含工作區的訂閱。  

1. 選擇名為 *「訪問包活動*」的工作簿。 

1. 在該工作簿中,選擇時間範圍(如果不確定,則更改為**全部**),並從該時間範圍內具有活動的所有訪問包的下拉清單中選擇訪問包 ID。 將顯示與選定時間範圍內發生的訪問包相關的事件。  

    ![檢視存取套件事件](./media/entitlement-management-logs-and-reporting/view-events-access-package.png) 

    每行包括時間、訪問包 ID、操作的名稱、物件 ID、UPN 以及啟動操作的使用者的顯示名稱。  JSON 中包含其他詳細資訊。   


## <a name="create-custom-azure-monitor-queries-using-the-azure-portal"></a>使用 Azure 門戶建立自訂 Azure 監視器查詢
您可以在 Azure AD 審核事件(包括授權管理事件)上創建自己的查詢。  

1. 在 Azure 門戶的 Azure 活動目錄中,按一下左側導航功能表中的「監視」部分下的 **「日誌」** 以創建新的查詢頁。

1. 工作區應顯示在查詢頁的左上角。 如果有多個 Azure 監視器工作區,並且未顯示用於存儲 Azure AD 審核事件的工作區,請按兩下選擇**範圍**"。 然後,選擇正確的訂閱和工作區。

1. 接下來,在查詢文本區域中,刪除字串「search +」並將其替換為以下查詢:

    ```
    AuditLogs | where Category == "EntitlementManagement"
    ```

1. 然後按一下 **[執行]**。 

    ![點選「執行」開始查詢](./media/entitlement-management-logs-and-reporting/run-query.png)

默認情況下,該表將顯示從最後一小時開始用於授權管理的審核日誌事件。 您可以更改「時間範圍」設定以檢視較舊的事件。 但是,更改此設置將僅顯示 Azure AD 設定為將事件發送到 Azure 監視器後發生的事件。

如果想知道 Azure 監視器中保持的最舊和最新的審核事件,請使用以下查詢:

```
AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type
```

有關為 Azure 監視器中的稽核事件儲存的欄的詳細資訊,請參閱在[Azure 監視器 中解釋 Azure AD 稽核紀錄架構](../reports-monitoring/reference-azure-monitor-audit-log-schema.md)。

## <a name="create-custom-azure-monitor-queries-using-azure-powershell"></a>使用 Azure PowerShell 建立自訂 Azure 監視器查詢

在將 Azure AD 設定為將日誌發送到 Azure 監視器後,可以通過 PowerShell 訪問日誌。 然後,從腳本或 PowerShell 命令行發送查詢,而無需成為租戶中的全域管理員。 

### <a name="ensure-the-user-or-service-principal-has-the-correct-role-assignment"></a>確保使用者或服務主體具有正確的角色分配

請確保您是要向 Azure AD 進行身份驗證的使用者或服務主體,在日誌分析工作區中處於相應的 Azure 角色中。 角色選項是日誌分析讀取器或日誌分析參與者。 如果您已在這些角色之一中,請跳過以[使用一個 Azure 訂閱檢索日誌分析 ID。](#retrieve-log-analytics-id-with-one-azure-subscription)

要設定角色分配並建立查詢,請執行以下步驟:

1. 在 Azure 門戶中,找到[紀錄分析工作區](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces
)。

1. 選擇**訪問控制 (IAM)。**

1. 然後按下「**添加**」 以添加角色分配。

    ![新增角色指派](./media/entitlement-management-logs-and-reporting/workspace-set-role-assignment.png)

### <a name="install-azure-powershell-module"></a>安裝 Azure PowerShell 模組

取得適當的角色分配後,啟動 PowerShell 並[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps?view=azps-3.3.0)(如果尚未分配),透過鍵入:

```azurepowershell
install-module -Name az -allowClobber -Scope CurrentUser
```
    
現在,您可以對 Azure AD 進行身份驗證,並檢索要查詢的日誌分析工作區的 ID。

### <a name="retrieve-log-analytics-id-with-one-azure-subscription"></a>使用 Azure 訂閱檢索日誌分析 ID
如果只有 Azure 訂閱和單一日誌分析工作區,則鍵入以下內容以驗證到 Azure AD、連接到該訂閱並檢索該工作區:
 
```azurepowershell
Connect-AzAccount
$wks = Get-AzOperationalInsightsWorkspace
```
 
### <a name="retrieve-log-analytics-id-with-multiple-azure-subscriptions"></a>使用多個 Azure 訂閱檢索紀錄分析 ID

 [獲取 Az 操作見解工作區](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace)一次在一個訂閱中運行。 因此,如果您有多個 Azure 訂閱,則需要確保連接到具有 Azure AD 日誌的日誌分析工作區的訂閱。 
 
 以下 cmdlet 顯示訂閱清單,並尋找具有紀錄分析工作區的訂閱 ID:
 
```azurepowershell
Connect-AzAccount
$subs = Get-AzSubscription
$subs | ft
```
 
您可以使用 命令(`Connect-AzAccount –Subscription $subs[0].id`如 )重新驗證 PowerShell 工作階段並將其關聯到該訂閱。 要瞭解有關如何從 PowerShell 進行身份驗證(包括非互動式)的更多詳細資訊,請參閱[使用 Azure PowerShell 登入](/powershell/azure/authenticate-azureps?view=azps-3.3.0&viewFallbackFrom=azps-2.5.0
)。

如果該訂閱中有多個日誌分析工作區,則 cmdlet [Get-Az 操作見解工作區](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace)將返回工作區清單。 然後,您可以找到具有 Azure AD 日誌的日誌。 此`CustomerId`cmdlet 傳回的欄位與日誌分析工作區概述中的 Azure 門戶中顯示的「工作區 ID」的值相同。
 
```powershell
$wks = Get-AzOperationalInsightsWorkspace
$wks | ft CustomerId, Name
```

### <a name="send-the-query-to-the-log-analytics-workspace"></a>將查詢傳送到紀錄分析工作區
最後,在確定工作區后,可以使用[Invoke-Az 操作見解查詢](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery?view=azps-3.3.0
)向該工作區發送 Kusto 查詢。 這些查詢是用[Kusto 查詢語言](https://docs.microsoft.com/azure/kusto/query/)編寫的。
 
例如,可以從日誌分析工作區檢索審核事件記錄的日期範圍,使用 PowerShell cmdlet 發送查詢,如下所示:
 
```powershell
$aQuery = "AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type"
$aResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $aQuery
$aResponse.Results |ft
```

您還可以使用以下查詢檢索授權管理事件:

```azurepowershell
$bQuery = 'AuditLogs | where Category == "EntitlementManagement"'
$bResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $Query
$bResponse.Results |ft 
```

## <a name="next-steps"></a>後續步驟：
- [使用 Azure 監視器活頁簿建立互動式報表](../../azure-monitor/app/usage-workbooks.md) 

