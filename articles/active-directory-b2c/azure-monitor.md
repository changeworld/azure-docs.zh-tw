---
title: 使用 Azure 監視器監視 Azure AD B2C
titleSuffix: Azure AD B2C
description: 瞭解如何使用委派的資源管理來記錄 Azure 監視器的 Azure AD B2C 事件。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.author: mimart
ms.subservice: B2C
ms.date: 11/12/2020
ms.openlocfilehash: b41f5e9a3bd4d3cbe52cf2e1c567d24de8a661f4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95992829"
---
# <a name="monitor-azure-ad-b2c-with-azure-monitor"></a>使用 Azure 監視器監視 Azure AD B2C

使用 Azure 監視器將 Azure Active Directory B2C (Azure AD B2C) 登入和 [審核](view-audit-logs.md) 記錄傳送至不同的監視解決方案。 您可以保留記錄以供長期使用，或與協力廠商安全性資訊和事件管理整合 (SIEM) 工具，以深入瞭解您的環境。

您可以將記錄事件路由至：

* Azure [儲存體帳戶](../storage/blobs/storage-blobs-introduction.md)。
* [Log Analytics 工作區](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace) (分析資料、建立儀表板，以及) 特定事件的警示。
* Azure [事件中樞](../event-hubs/event-hubs-about.md) (，並與您的 Splunk 和 Sumo 邏輯實例) 整合。

![Azure 監視器](./media/azure-monitor/azure-monitor-flow.png)

在本文中，您將瞭解如何將記錄傳送至 Azure Log Analytics 工作區。 然後，您可以建立儀表板，或根據 Azure AD B2C 使用者的活動建立警示。

## <a name="deployment-overview"></a>部署概觀

Azure AD B2C 利用 [Azure Active Directory 監視](../active-directory/reports-monitoring/overview-monitoring.md)。 若要在 Azure AD B2C 租使用者內的 Azure Active Directory 中啟用 *診斷設定* ，請使用 [Azure Lighthouse](../lighthouse/concepts/azure-delegated-resource-management.md) 來 [委派資源](../lighthouse/concepts/azure-delegated-resource-management.md)，讓 Azure AD B2C (**服務提供者**) 管理 **客戶** Azure AD 資源 (。 完成本文中的步驟之後，您就可以存取 *azure ad-b2c-監視* 資源群組，其中包含您 **Azure AD B2C** 入口網站中的 [Log Analytics 工作區](../azure-monitor/learn/quick-create-workspace.md)。 您也可以從 Azure AD B2C 將記錄傳送至 Log Analytics 工作區。

在此部署期間，您會授權 Azure AD B2C 目錄中的使用者或群組，以在包含您 Azure 訂用帳戶的租使用者中設定 Log Analytics 工作區實例。 若要建立授權，請將 [Azure Resource Manager](../azure-resource-manager/index.yml) 範本部署到包含訂用帳戶的 Azure AD 租使用者。

下圖描述您將在 Azure AD 和 Azure AD B2C 租使用者中設定的元件。

![資源群組投射](./media/azure-monitor/resource-group-projection.png)

在此部署期間，您將會設定您的 Azure AD B2C 租使用者，以及將裝載 Log Analytics 工作區的 Azure AD 租使用者。 用來執行部署的帳戶必須獲指派這兩個租使用者中的 [全域管理員](../active-directory/roles/permissions-reference.md#limit-use-of-global-administrator) 角色。 此外，請務必確定您已在完成每個步驟時登入正確的目錄，如所述。

## <a name="1-create-or-choose-resource-group"></a>1. 建立或選擇資源群組

首先，建立或選擇資源群組，其中包含將從 Azure AD B2C 接收資料的目的地 Log Analytics 工作區。 當您部署 Azure Resource Manager 範本時，會指定資源組名。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 選取入口網站工具列中的 [ **目錄 + 訂** 用帳戶] 圖示，然後選取包含您 **Azure AD 租** 使用者的目錄。
1. [建立資源群組](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) ，或選擇現有的資源群組。 此範例會使用名為 *azure-ad-b2c-monitor* 的資源群組。

## <a name="2-create-a-log-analytics-workspace"></a>2. 建立 Log Analytics 工作區

**Log Analytics 工作區** 是 Azure 監視器記錄資料的唯一環境。 您將使用此 Log Analytics 工作區從 Azure AD B2C 的 [audit 記錄](view-audit-logs.md)檔收集資料，然後使用查詢和活頁簿將資料視覺化，或建立警示。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 選取入口網站工具列中的 [ **目錄 + 訂** 用帳戶] 圖示，然後選取包含您 **Azure AD 租** 使用者的目錄。
1. [建立 Log Analytics 工作區](../azure-monitor/learn/quick-create-workspace.md)。 此範例會使用名為 *AzureAdB2C* 的 Log Analytics 工作區，在名為 *azure ad-b2c-monitor* 的資源群組中。

## <a name="3-delegate-resource-management"></a>3. 委派資源管理

在此步驟中，您會選擇您的 Azure AD B2C 租使用者作為 **服務提供者**。 您也會定義您需要的授權，以將適當的 Azure 內建角色指派給 Azure AD 租使用者中的群組。

### <a name="31-get-your-azure-ad-b2c-tenant-id"></a>3.1 取得您 Azure AD B2C 的租使用者識別碼

首先，取得您 Azure AD B2C 目錄的 **租使用者識別碼** (也稱為目錄識別碼) 。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. 選取入口網站工具列中的 [ **目錄 + 訂** 用帳戶] 圖示，然後選取包含您 **Azure AD B2C** 租使用者的目錄。
1. 選取 **Azure Active Directory**，然後選取 **[總覽**]。
1. 記錄 **租使用者識別碼**。

### <a name="32-select-a-security-group"></a>3.2 選取安全性群組

現在，請選取您想要將許可權授與您稍早在包含訂用帳戶的目錄中所建立之資源群組的 Azure AD B2C 群組或使用者。  

為了讓管理更容易，我們建議您針對每個角色使用 Azure AD 使用者 *群組* ，讓您可以新增或移除群組中的個別使用者，而不是直接將許可權指派給該使用者。 在此逐步解說中，我們將新增安全性群組。

> [!IMPORTANT]
> 為了新增 Azure AD 群組的許可權， **群組類型** 必須設定為 [ **安全性**]。 建立群組時，會選取此選項。 如需詳細資訊，請參閱[使用 Azure Active Directory 建立基本群組並新增成員](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。

1. 在 **Azure AD B2C** 目錄中仍選取 **Azure Active Directory** ，請選取 [**群組**]，然後選取群組。 如果您沒有現有的群組，請建立 **安全** 組，然後新增成員。 如需詳細資訊，請遵循 [使用 Azure Active Directory 建立基本群組和新增成員](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。 
1. 選取 **[總覽**]，並記錄群組的 **物件識別碼**。

### <a name="33-create-an-azure-resource-manager-template"></a>3.3 建立 Azure Resource Manager 範本

接下來，您將建立 Azure Resource Manager 範本，將 Azure AD B2C 存取權授與您稍早建立的 Azure AD 資源群組 (例如， *Azure-AD-B2C-監視器*) 。 使用 [ **部署至 Azure** ] 按鈕，從 GitHub 範例部署範本，此按鈕會開啟 Azure 入口網站，並可讓您直接在入口網站中設定和部署範本。 針對這些步驟，請確定您已登入 Azure AD 租使用者， (不是 Azure AD B2C 租使用者) 。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取入口網站工具列中的 [ **目錄 + 訂** 用帳戶] 圖示，然後選取包含您 **Azure AD** 租使用者的目錄。
3. 使用 [ **部署至 Azure** ] 按鈕來開啟 Azure 入口網站，並直接在入口網站中部署範本。 如需詳細資訊，請參閱 [建立 Azure Resource Manager 範本](../lighthouse/how-to/onboard-customer.md#create-an-azure-resource-manager-template)。

   [![部署至 Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Lighthouse-samples%2Fmaster%2Ftemplates%2Frg-delegated-resource-management%2FrgDelegatedResourceManagement.json)

5. 在 [ **自訂部署** ] 頁面上，輸入下列資訊：

   | 欄位   | 定義 |
   |---------|------------|
   | 訂用帳戶 |  選取包含 azure 訂用帳戶的目錄，其中建立了 *azure ad-b2c-監視* 資源群組。 |
   | 區域| 選取將在其中部署資源的區域。  | 
   | Msp 供應專案名稱| 說明此定義的名稱。 例如， *Azure AD B2C 監視*]。  |
   | Msp 供應專案描述| 您供應專案的簡短描述。 例如，會 *啟用 Azure AD B2C 中的 Azure 監視器*。|
   | 受租使用者識別碼管理| 您 Azure AD B2C 租使用者的 **租使用者識別碼** (也稱為目錄識別碼) 。 |
   |授權|指定包含 Azure AD `principalId` 、和 Azure 之物件的 JSON 陣列 `principalIdDisplayName` `roleDefinitionId` 。 `principalId`是 B2C 群組或使用者的 **物件識別碼**，可存取此 Azure 訂用帳戶中的資源。 在這個逐步解說中，請指定您稍早記錄的群組物件識別碼。 針對 `roleDefinitionId` ，請使用「*參與者」角色* 的 [內建角色](../role-based-access-control/built-in-roles.md)值 `b24988ac-6180-42a0-ab88-20f7382dd24c` 。|
   | Rg 名稱 | 您稍早在 Azure AD 租使用者中建立的資源組名。 例如， *azure-ad-b2c-監視器*。 |

   下列範例示範具有一個安全性群組的授權陣列。

   ```json
   [
       {
           "principalId": "<Replace with group's OBJECT ID>",
           "principalIdDisplayName": "Azure AD B2C tenant administrators",
           "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
       }
   ]
   ```

部署範本之後，可能需要幾分鐘的時間 (通常不會有五個以上的資源投射完成的) 。 您可以驗證 Azure AD 租使用者中的部署，並取得資源投射的詳細資料。 如需詳細資訊，請參閱 [查看和管理服務提供者](../lighthouse/how-to/view-manage-service-providers.md)。

## <a name="4-select-your-subscription"></a>4. 選取您的訂用帳戶

在您部署範本並等候幾分鐘的時間完成資源投射之後，請依照下列步驟將您的訂用帳戶與您的 Azure AD B2C 目錄建立關聯。

1. 如果您目前已登入，請登出 Azure 入口網站 (如此可讓您的會話認證在下一個步驟) 中重新整理。
2. 使用您 **Azure AD B2C** 的系統管理帳戶登入 [Azure 入口網站](https://portal.azure.com)。 此帳戶必須是您在 [委派資源管理](#3-delegate-resource-management) 步驟中所指定之安全性群組的成員。
3. 選取入口網站工具列中的 **目錄 + 訂閱** 圖示。
4. 選取包含 Azure 訂用帳戶的 Azure AD 目錄，以及您所建立的 *azure AD b2c 監視器* 資源群組。

    ![切換目錄](./media/azure-monitor/azure-monitor-portal-03-select-subscription.png)

1. 確認您已選取正確的目錄和訂用帳戶。 在此範例中，會選取所有目錄和所有訂閱。

    ![目錄 & 訂用帳戶篩選中選取的所有目錄](./media/azure-monitor/azure-monitor-portal-04-subscriptions-selected.png)

## <a name="5-configure-diagnostic-settings"></a>5. 設定診斷設定

診斷設定會定義應該如何傳送資源的記錄和計量。 可能的目的地包括：

- [Azure 儲存體帳戶](../azure-monitor/platform/resource-logs-collect-storage.md)
- [事件中樞](../azure-monitor/platform/resource-logs-stream-event-hubs.md) 解決方案
- [Log Analytics 工作區](../azure-monitor/platform/resource-logs-collect-workspace.md)

在此範例中，我們使用 Log Analytics 工作區來建立儀表板。

### <a name="51-create-diagnostic-settings"></a>5.1 建立診斷設定

您已經準備好在 Azure 入口網站中 [建立診斷設定](../active-directory/reports-monitoring/overview-monitoring.md) 。

若要設定 Azure AD B2C 活動記錄的監視設定：

1. 使用您 Azure AD B2C 的系統管理帳戶登入 [Azure 入口網站](https://portal.azure.com/) 。 此帳戶必須是您在 [ [選取安全性群組](#32-select-a-security-group) ] 步驟中所指定之安全性群組的成員。
1. 在入口網站工具列中選取 **目錄 + 訂用帳戶** 圖示，然後選取包含 Azure AD B2C 租用戶的目錄。
1. 選取 **Azure Active Directory**
1. 在 [監視]  下方，選取 [診斷設定]  。
1. 如果資源有現有的設定，您會看到已設定的設定清單。 選取 [ **新增診斷設定** ] 以新增設定，或選取 [ **編輯** ] 以編輯現有的設定。 每個設定都不能有一個以上的目的地類型。

    ![Azure 入口網站中的 [診斷設定] 窗格](./media/azure-monitor/azure-monitor-portal-05-diagnostic-settings-pane-enabled.png)

1. 如果您的設定還沒有名稱，請為您的設定命名。
1. 核取每個目的地的核取方塊，以傳送記錄。 選取 [ **設定** ] 以指定其設定， **如下表所述**。
1. 選取 [ **傳送至 Log Analytics**]，然後選取您稍早建立 **的工作區名稱** (`AzureAdB2C`) 。
1. 選取 [ **AuditLogs** ] 和 [ **SignInLogs**]。
1. 選取 [儲存]。

> [!NOTE]
> 發出事件之後，最多可能需要15分鐘的時間，才 [會出現在 Log Analytics 工作區中](../azure-monitor/platform/data-ingestion-time.md)。 此外，深入瞭解 [Active Directory 報告延遲](../active-directory/reports-monitoring/reference-reports-latencies.md)，這可能會影響過期的資料，並在報告中扮演重要角色。

如果您看到錯誤訊息：「若要為您的 Azure AD B2C 目錄設定診斷設定以使用 Azure 監視器，您必須設定委派的資源管理」，請確定您是以 [安全性群組](#32-select-a-security-group) 成員的使用者身分登入，然後 [選取您的訂](#4-select-your-subscription)用帳戶。

## <a name="6-visualize-your-data"></a>6. 視覺化您的資料

現在您可以設定 Log Analytics 工作區，以將資料視覺化並設定警示。 您可以在 Azure AD 租使用者和 Azure AD B2C 租使用者中進行這些設定。

### <a name="61-create-a-query"></a>6.1 建立查詢

記錄查詢可協助您充分利用 Azure 監視器記錄中收集的資料值。 強大的查詢語言可讓您聯結來自多個資料表的資料、匯總大型資料集，並以基本程式碼執行複雜作業。 幾乎任何問題只要已收集到支援資料，就可以回答和分析，而且您瞭解如何建立正確的查詢。 如需詳細資訊，請參閱 [Azure 監視器中的開始使用記錄查詢](../azure-monitor/log-query/get-started-queries.md)。

1. 從 **Log Analytics 工作區** 選取 **記錄**
1. 在 [查詢編輯器] 中，貼上下列 [Kusto 查詢語言](https://docs.microsoft.com/azure/data-explorer/kusto/query/) 查詢。 此查詢會依操作顯示過去 x 天的原則使用方式。 預設的持續時間會設定為90天 (90d) 。 請注意，此查詢只著重于原則發出權杖/程式碼的作業。

    ```kusto
    AuditLogs
    | where TimeGenerated  > ago(90d)
    | where OperationName contains "issue"
    | extend  UserId=extractjson("$.[0].id",tostring(TargetResources))
    | extend Policy=extractjson("$.[1].value",tostring(AdditionalDetails))
    | summarize SignInCount = count() by Policy, OperationName
    | order by SignInCount desc  nulls last
    ```

1. 選取 [執行]。 查詢結果會顯示在畫面底部。
1. 若要儲存您的查詢以供稍後使用，請選取 [ **儲存**]。

   ![Log Analytics 記錄編輯器](./media/azure-monitor/query-policy-usage.png)

1. 填入下列詳細資料：

    - **名稱** -輸入您的查詢名稱。
    - **另存** 新檔 `query` 。
    - **類別** -選取 `Log` 。

1. 選取 [儲存]。

您也可以使用 [轉譯運算子來](https://docs.microsoft.com/azure/data-explorer/kusto/query/renderoperator?pivots=azuremonitor) 變更您的查詢，以將資料視覺化。

```kusto
AuditLogs
| where TimeGenerated  > ago(90d)
| where OperationName contains "issue"
| extend  UserId=extractjson("$.[0].id",tostring(TargetResources))
| extend Policy=extractjson("$.[1].value",tostring(AdditionalDetails))
| summarize SignInCount = count() by Policy
| order by SignInCount desc  nulls last
| render  piechart
```

![Log Analytics 記錄編輯器圓形圖](./media/azure-monitor/query-policy-usage-pie.png)

如需更多範例，請參閱 Azure AD B2C [SIEM GitHub](https://aka.ms/b2csiem)存放庫。

### <a name="62-create-a-workbook"></a>6.2 建立活頁簿

活頁簿提供彈性的畫布，可供您用來分析資料，並在 Azure 入口網站中建立豐富的視覺效果報表。 它們可讓您從 Azure 跨多個資料來源，並將它們結合成整合的互動式體驗。 如需詳細資訊，請參閱 [Azure 監視器活頁簿](../azure-monitor/platform/workbooks-overview.md)。

請遵循下列指示，使用 JSON 資源庫範本建立新的活頁簿。 此活頁簿會為 Azure AD B2C 租 **使用者提供使用者深入** 解析和 **驗證** 儀表板。

1. 從 **Log Analytics 工作區** 中，選取 [活頁 **簿**]。
1. 從工具列中選取 [ **+ 新增** ] 選項，以建立新的活頁簿。
1. 在 [**新增活頁簿**] 頁面上 **Advanced Editor** ，使用 **</>** 工具列上的選項選取進階編輯器。

     ![資源庫範本](./media/azure-monitor/wrkb-adv-editor.png)

1. 選取資源 **庫範本**。
1. 以 [Azure AD B2C 基本活頁簿](https://raw.githubusercontent.com/azure-ad-b2c/siem/master/workbooks/dashboard.json)中的內容取代資源 **庫範本** 中的 JSON：
1. 使用 [套用 **] 按鈕來** 套用範本。
1. 從工具列中選取 [ **完成編輯** ] 按鈕，以完成活頁簿的編輯。
1. 最後，使用工具列中的 [ **儲存** ] 按鈕來儲存活頁簿。
1. 提供 **標題**，例如 *Azure AD B2C 儀表板*。
1. 選取 [儲存]。

    ![儲存活頁簿](./media/azure-monitor/wrkb-title.png)

活頁簿會以儀表板的形式來顯示報表。

![活頁簿第一個儀表板](./media/azure-monitor/wkrb-dashboard-1.png)

![活頁簿第二個儀表板](./media/azure-monitor/wrkb-dashboard-2.png)

![活頁簿第三儀表板](./media/azure-monitor/wrkb-dashboard-3.png)


## <a name="create-alerts"></a>建立警示

警示是由 Azure 監視器中的警示規則所建立，可定期自動執行儲存的查詢或自訂的記錄搜尋。 建立警示的依據，可以是特定的效能計量、特定事件建立時、缺少某個事件或特定的時間間隔內建立了數個事件。 例如，當平均登入次數超過特定臨界值時，就可以使用警示來通知您。 如需詳細資訊，請參閱[建立警示](../azure-monitor/learn/tutorial-response.md)。


您可以使用下列指示來建立新的 Azure 警示，當 **總要求數總計** 有25% 時，就會傳送 [電子郵件通知](../azure-monitor/platform/action-groups.md#configure-notifications)至先前的期間。 警示會每隔5分鐘執行一次，並在 [過去24小時] 視窗中尋找卸載。 您可以使用 Kusto 查詢語言來建立警示。


1. 從 **Log Analytics 工作區** 中，選取 [ **記錄**]。 
1. 使用下列查詢來建立新的 **Kusto 查詢** 。

    ```kusto
    let start = ago(24h);
    let end = now();
    let threshold = -25; //25% decrease in total requests.
    AuditLogs
    | serialize TimeGenerated, CorrelationId, Result
    | make-series TotalRequests=dcount(CorrelationId) on TimeGenerated in range(start, end, 1h)
    | mvexpand TimeGenerated, TotalRequests
    | where TotalRequests > 0
    | serialize TotalRequests, TimeGenerated, TimeGeneratedFormatted=format_datetime(todatetime(TimeGenerated), 'yyyy-M-dd [hh:mm:ss tt]')
    | project   TimeGeneratedFormatted, TotalRequests, PercentageChange= ((toreal(TotalRequests) - toreal(prev(TotalRequests,1)))/toreal(prev(TotalRequests,1)))*100
    | order by TimeGeneratedFormatted
    | where PercentageChange <= threshold   //Trigger's alert rule if matched.
    ```

1. 選取 [ **執行**] 以測試查詢。 如果過去24小時內的要求總數中有25% 以上的結果，您應該會看到結果。
1. 若要根據上述查詢來建立警示規則，請使用工具列提供的 [ **+ 新增警示規則** ] 選項。
1. 在 [**建立警示規則**] 頁面上，選取 [**條件名稱**] 
1. 在 [設定 **信號邏輯** ] 頁面上，設定下列值，然後使用 [ **完成** ] 按鈕儲存變更。
    * 警示邏輯：設定 **大於** **0****的結果數目**。
    * 評估依據：選取 **1440** （以分鐘為單位） (（以分鐘為單位）) 和 **5** （以分鐘為單位） ()  

    ![建立警示規則條件](./media/azure-monitor/alert-create-rule-condition.png)

警示建立之後，請移至 **Log Analytics 工作區** ，然後選取 [ **警示**]。 此頁面會顯示在 [ **時間範圍** ] 選項設定的 [持續時間] 選項中觸發的所有警示。  

### <a name="configure-action-groups"></a>設定動作群組

Azure 監視器和服務健康狀態警示使用動作群組來通知使用者警示已被觸發。 您可以加入語音通話、SMS、電子郵件、或觸發各種類型的自動化動作。 遵循在[Azure 入口網站中建立和管理動作群組](../azure-monitor/platform/action-groups.md)的指導方針

以下是警示通知電子郵件的範例。 

   ![電子郵件通知](./media/azure-monitor/alert-email-notification.png)

## <a name="multiple-tenants"></a>多個租使用者

若要將多個 Azure AD B2C 租使用者記錄檔上線至相同的 Log Analytics 工作區 (或 Azure 儲存體帳戶或事件中樞) ，您將需要使用不同 **Msp 供應專案名稱** 值的個別部署。 確定您的 Log Analytics 工作區與您在 [建立或選擇資源群組](#1-create-or-choose-resource-group)中設定的工作區位於相同的資源群組中。

使用多個 Log Analytics 工作區時，請使用 [跨工作區查詢](../azure-monitor/log-query/cross-workspace-query.md) 來建立可跨多個工作區運作的查詢。 例如，下列查詢會根據相同的類別，從不同的租使用者執行兩個審核記錄的聯結 (例如，驗證) ：

```kusto
workspace("AD-B2C-TENANT1").AuditLogs
| join  workspace("AD-B2C-TENANT2").AuditLogs
  on $left.Category== $right.Category
```

## <a name="change-the-data-retention-period"></a>變更資料保留期

Azure 監視器記錄檔的設計目的是要調整和支援從您企業中的任何來源，或在 Azure 中部署，每天收集、編制索引和儲存大量的資料。 依預設，記錄會保留30天，但保留期間可以增加到最多兩年。 瞭解如何 [使用 Azure 監視器記錄來管理使用量和成本](../azure-monitor/platform/manage-cost-storage.md)。 選取定價層之後，您可以 [變更資料保留期限](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)。

## <a name="next-steps"></a>後續步驟

* 在 Azure AD B2C [SIEM 資源庫](https://aka.ms/b2csiem)中尋找更多範例。 

* 如需在 Azure 監視器中新增和設定診斷設定的詳細資訊，請參閱 [教學課程：收集和分析 Azure 資源的資源記錄](../azure-monitor/insights/monitor-azure-resource.md)。

* 如需將 Azure AD 記錄串流至事件中樞的相關資訊，請參閱 [教學課程：將 Azure Active Directory 記錄串流至 Azure 事件中樞](../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)。
