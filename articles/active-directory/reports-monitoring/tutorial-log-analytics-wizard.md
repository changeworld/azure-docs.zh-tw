---
title: 在 Azure AD 中設定記錄分析精靈 | Microsoft Docs
description: 了解如何設定記錄分析。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 08/05/2020
ms.author: markvi
author: MarkusVi
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 643010ef1f6e941a57673a711e1871aafd1e341d
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91361903"
---
# <a name="tutorial-configure-the-log-analytics-wizard"></a>教學課程：設定記錄分析精靈


在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 針對您的稽核和登入記錄設定記錄分析工作區
> * 使用 Kusto 查詢語言執行查詢 (KQL)
> * 建立警示規則，以在使用特定帳戶時傳送警示
> * 使用快速入門範本建立自訂活頁簿
> * 將查詢加入至現有的活頁簿範本

## <a name="prerequisites"></a>先決條件

- 至少有一個 P1 授權管理員的 Azure 訂用帳戶。如果您沒有 Azure 訂用帳戶，您可以[註冊免費試用](https://azure.microsoft.com/free/)。

- Azure AD 租用戶。

- 使用者，身分是該 Azure AD 租用戶的「全域管理員」或「安全性管理員」。


熟悉下列文章：

- [教學課程：從 Azure 資源收集資源記錄並加以分析](../../azure-monitor/learn/tutorial-resource-logs.md)

- [如何整合活動記錄與 Log Analytics](./howto-integrate-activity-logs-with-log-analytics.md)

- [在 Azure AD 中管理緊急存取帳戶](../users-groups-roles/directory-emergency-access.md)

- [KQL 快速參考](/azure/data-explorer/kql-quick-reference)

- [Azure 監視器活頁簿](../../azure-monitor/platform/workbooks-overview.md)



## <a name="configure-a-workspace"></a>設定工作區 

此程程序會概述如何為您的稽核和登入記錄設定記錄分析工作區。
設定記錄分析工作區包含兩個主要步驟：
 
1. 建立記錄分析工作區
2. 設定診斷設定

**設定工作區：** 


1. 以系統管理員身分登入 [Azure 入口網站](https://portal.azure.com)。

2. 搜尋**記錄分析工作區**。

    ![搜尋資源服務和文件](./media/tutorial-log-analytics-wizard/search-services.png)

3. 在記錄分析工作區頁面上，按一下 [新增]。

    ![此螢幕擷取畫面顯示 Log Analytics 工作區頁面中的 [新增] 按鈕。](./media/tutorial-log-analytics-wizard/add.png)

4.  在 [建立 Log Analytics 工作區] 頁面上，執行下列步驟：

    ![建立 Log Analytics 工作區](./media/tutorial-log-analytics-wizard/create-log-analytics-workspace.png)

    1. 選取您的訂用帳戶。

    2. 選取資源群組。
 
    3. 在 [名稱] 文字方塊中，輸入名稱 (例如：MytestWorkspace1)。

    4. 選取您的區域。

5. 按一下 [檢閱 + 建立]。

    ![檢閱及建立](./media/tutorial-log-analytics-wizard/review-create.png)

6. 按一下 [建立] 並等待部署成功。 您可能需要重新整理頁面，才能看到新的工作區。

    ![建立](./media/tutorial-log-analytics-wizard/create-workspace.png)

7. 搜尋 **Azure Active Directory**。

    ![Azure Active Directory](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

8. 在 [監視] 區段中，按一下 [診斷設定]。

    ![Azure Active Directory](./media/tutorial-log-analytics-wizard/diagnostic-settings.png)

9. 在 [診斷設定] 頁面上，按一下 [新增診斷設定]。

    ![新增診斷設定](./media/tutorial-log-analytics-wizard/add-diagnostic-setting.png)

10. 在 [診斷設定] 頁面上，執行下列步驟：

    ![選取 [診斷設定]](./media/tutorial-log-analytics-wizard/select-diagnostics-settings.png)

    1. 在 [類別詳細資料] 底下，選取 [AuditLogs] 和 [SigninLogs]。

    2. 在 [目的地詳細資料] 中，選取傳送至 [Log Analytics]，然後選取新的記錄分析工作區。 
   
    3. 按一下 [檔案] 。 

## <a name="run-queries"></a>執行查詢  

此程序將說明如何使用 **Kusto 查詢語言 (KQL)** 來執行查詢。


**若要執行查詢：**


1. 以系統管理員身分登入 [Azure 入口網站](https://portal.azure.com)。

2. 搜尋 **Azure Active Directory**。

    ![Azure Active Directory](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

3. 在 [監視] 區段中，按一下 [記錄]。

4. 在 [記錄] 頁面上，按一下 [開始使用]。

5. 在 [搜尋] 文字方塊中，輸入您的查詢。

6. 按一下 **[執行]** 。  


### <a name="kql-query-examples"></a>KQL 查詢範例

從輸入資料中取出 10 個隨機項目：

`SigninLogs | take 10`

查看成功使用條件式存取的登入

`SigninLogs | where ConditionalAccessStatus == "success" | project UserDisplayName, ConditionalAccessStatus` 


計算已有多少成功次數

`SigninLogs | where ConditionalAccessStatus == "success" | project UserDisplayName, ConditionalAccessStatus | count`


彙總使用者每天成功登入的計數：

`SigninLogs | where ConditionalAccessStatus == "success" | summarize SuccessfulSign-ins = count() by UserDisplayName, bin(TimeGenerated, 1d)` 


檢視使用者在特定時間週期中執行特定作業的次數：

`AuditLogs | where TimeGenerated > ago(30d) | where OperationName contains "Add member to role" | summarize count() by OperationName, Identity`


對作業名稱上的結果進行樞紐分析

`AuditLogs | where TimeGenerated > ago(30d) | where OperationName contains "Add member to role" | project OperationName, Identity | evaluate pivot(OperationName)`


使用內部聯結將稽核和登入記錄合併在一起：

`AuditLogs |where OperationName contains "Add User" |extend UserPrincipalName = tostring(TargetResources[0].userPrincipalName) | |project TimeGenerated , UserPrincipalName |join kind = inner (SigninLogs) on UserPrincipalName |summarize arg_min(TimeGenerated, *) by UserPrincipalName |extend SigninDate = TimeGenerated` 


檢視用戶端應用程式類型的登入數目：

`SigninLogs | summarize count() by ClientAppUsed`

計算每日的登入計數：

`SigninLogs | summarize NumberOfEntries=count() by bin(TimeGenerated, 1d)`

取用 5 個隨機項目，並在結果中投影您想要查看的資料行：

`SigninLogs | take 5 | project ClientAppUsed, Identity, ConditionalAccessStatus, Status, TimeGenerated `


取得前 5 個項目 (以遞減方式排序)，並投影您想要查看的資料行

`SigninLogs | take 5 | project ClientAppUsed, Identity, ConditionalAccessStatus, Status, TimeGenerated `

將值與其他兩個資料行結合，以建立新的資料行：

`SigninLogs | limit 10 | extend RiskUser = strcat(RiskDetail, "-", Identity) | project RiskUser, ClientAppUsed`




## <a name="create-an-alert-rule"></a>建立警示規則  

此程序將說明如何在使用 Breakglass 帳戶時傳送警示。

**若要建立警示規則：**


1. 以系統管理員身分登入 [Azure 入口網站](https://portal.azure.com)。

2. 搜尋 **Azure Active Directory**。

    ![Azure Active Directory](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

3. 在 [監視] 區段中，按一下 [記錄]。

4. 在 [記錄] 頁面上，按一下 [開始使用]。

5. 在 [搜尋] 文字方塊中，輸入：`SigninLogs |where UserDisplayName contains "BreakGlass" | project UserDisplayName`

6. 按一下 **[執行]** 。  

7. 在工具列中，按一下 [新增警示規則]。

    ![新增警示規則](./media/tutorial-log-analytics-wizard/new-alert-rule.png)

8. 在 [建立警示規則] 頁面上，確認範圍正確。

9. 在 [條件] 底下，按一下：**每當平均自訂記錄搜尋大於 <logic undefined> 計數時**

    ![預設條件](./media/tutorial-log-analytics-wizard/default-condition.png)

10. 在 [設定訊號邏輯] 頁面的 [警示邏輯] 區段中，執行下列步驟：

    ![警示邏輯](./media/tutorial-log-analytics-wizard/alert-logic.png)

    1. 針對 [依據]，選取 [結果數目]。

    2. 針對 [運算子]，選取 [大於]。

    3. 針對 [閾值]，選取 [0]。 

11. 在 [設定訊號邏輯] 頁面的 [評估依據] 區段中，執行下列步驟：

    ![評估依據](./media/tutorial-log-analytics-wizard/evaluated-based-on.png)

    1. 針對 [期間 (以分鐘為單位)]，選取 [5]。

    2. 針對 [頻率 (以分鐘為單位)]，選取 [5]。

    3. 按一下 [完成]。 

12. 在 [動作群組] 下方，按一下 [選取動作群組]。 

    ![動作群組](./media/tutorial-log-analytics-wizard/action-group.png)

13. 在 [選取要附加至此警示規則的動作群組] 中，按一下 [建立動作群組]。 

    ![建立動作群組](./media/tutorial-log-analytics-wizard/create-action-group.png)

14. 在 [建立動作群組] 頁面上，執行下列步驟：

    ![執行個體詳細資料](./media/tutorial-log-analytics-wizard/instance-details.png)

    1. 在 [動作群組名稱] 文字方塊中，輸入「我的動作群組」。

    2. 在 [顯示名稱] 文字方塊中，輸入「我的動作」。

    3. 按一下 [檢閱 + 建立]。 

    4. 按一下頁面底部的 [新增] 。


15. 在 [自訂動作] 底下，執行下列步驟：

    ![自訂動作](./media/tutorial-log-analytics-wizard/customize-actions.png)

    1. 選取 [電子郵件主旨]。

    2. 在 [主旨列] 文字方塊中，輸入：`Breakglass account has been used`

16. 在 [警示規則詳細資料] 底下，執行下列步驟：

    ![警示規則詳細資料](./media/tutorial-log-analytics-wizard/alert-rule-details.png)

    1. 在 [警示規則名稱] 文字方塊中，輸入：`Breakglass account`

    2. 在 [描述] 文字方塊中，輸入：`Your emergency access account has been used`

17. 按一下 [建立警示規則]。   


## <a name="create-a-custom-workbook"></a>建立自訂活頁簿

此程序將說明如何使用快速入門範本來建立新的活頁簿。




1. 以系統管理員身分登入 [Azure 入口網站](https://portal.azure.com)。

2. 搜尋 **Azure Active Directory**。

    ![Azure Active Directory](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

3. 在 [監視] 區段中，按一下 [活頁簿]。

    ![此螢幕擷取畫面顯示 Azure 入口網站功能表中已選取活頁簿的 [監視]。](./media/tutorial-log-analytics-wizard/workbooks.png)

4. 在 [快速入門] 區段中，按一下 [空白]。

    ![快速入門](./media/tutorial-log-analytics-wizard/quick-start.png)

5. 按一下 [新增] 。

    ![新增活頁簿](./media/tutorial-log-analytics-wizard/add-workbook.png)

6. 按一下 [新增文字]。

    ![加入文字](./media/tutorial-log-analytics-wizard/add-text.png)


7. 在文字方塊中，輸入：`# Client apps used in the past week`，然後按一下 [完成編輯]。

    ![活頁簿文字](./media/tutorial-log-analytics-wizard/workbook-text.png)

8. 在新的活頁簿中，按一下 [新增]，然後按一下 [新增查詢]。

    ![新增查詢](./media/tutorial-log-analytics-wizard/add-query.png)

9. 在 [查詢] 文字方塊中，輸入：`SigninLogs | where TimeGenerated > ago(7d) | project TimeGenerated, UserDisplayName, ClientAppUsed | summarize count() by ClientAppUsed`

10. 按一下 [Run Query (執行查詢)]****。

    ![執行查詢](./media/tutorial-log-analytics-wizard/run-workbook-query.png)

11. 在工具列的 [視覺效果] 底下，按一下 [圓形圖]。

    ![圓形圖](./media/tutorial-log-analytics-wizard/pie-chart.png)

12. 按一下 [完成編輯]。

    ![已完成編輯](./media/tutorial-log-analytics-wizard/done-workbook-editing.png)



## <a name="add-a-query-to-a-workbook-template"></a>將查詢新增至活頁簿範本  

此程序將說明如何將查詢新增至現有的活頁簿範本。 此範例所依據的查詢會顯示條件式存取成功到失敗的分佈。


1. 以系統管理員身分登入 [Azure 入口網站](https://portal.azure.com)。

2. 搜尋 **Azure Active Directory**。

    ![Azure Active Directory](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

3. 在 [監視] 區段中，按一下 [活頁簿]。

    ![此螢幕擷取畫面顯示功能表中已選取活頁簿的 [監視]。](./media/tutorial-log-analytics-wizard/workbooks.png)

4. 在 [條件式存取] 區段中，按一下 [條件式存取深入解析和報告]。

    ![條件式存取範本](./media/tutorial-log-analytics-wizard/conditional-access-template.png)

5. 在工具列中，按一下 [編輯]。

    ![條件式存取範本](./media/tutorial-log-analytics-wizard/edit-workbook-template.png)

6. 在工具列中，按一下三個點，然後依序選取 [新增] 和 [新增查詢]。

    ![新增活頁簿查詢](./media/tutorial-log-analytics-wizard/add-custom-workbook-query.png)

7. 在 [查詢] 文字方塊中，輸入：`SigninLogs | where TimeGenerated > ago(20d) | where ConditionalAccessPolicies != "[]" | summarize dcount(UserDisplayName) by bin(TimeGenerated, 1d), ConditionalAccessStatus`

8. 按一下 [Run Query (執行查詢)]****。

    ![執行查詢](./media/tutorial-log-analytics-wizard/run-workbook-insights-query.png)

9. 按一下 [時間範圍]，然後選取 [在查詢中設定]。

10. 按一下 [視覺效果]，然後選取 [長條圖]。 

11. 按一下 [進階設定]，並輸入 `Conditional Access status over the last 20 days` 作為圖表標題，然後按一下 [完成編輯]。 

    ![設定圖標題](./media/tutorial-log-analytics-wizard/set-chart-title.png)








## <a name="next-steps"></a>後續步驟

請前往下一篇文章，以了解如何使用 Azure 入口網站來管理裝置身分識別。
> [!div class="nextstepaction"]
> [監視](overview-monitoring.md)