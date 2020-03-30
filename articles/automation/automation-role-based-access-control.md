---
title: Azure 自動化中的角色型存取控制
description: 角色型存取控制 (RBAC) 可以啟用對 Azure 資源的存取權管理。 本文說明如何在 Azure 自動化中設定 RBAC。
keywords: 自動化 rbac, 角色型存取控制, azure rbac
services: automation
ms.subservice: shared-capabilities
ms.date: 05/17/2018
ms.topic: conceptual
ms.openlocfilehash: 8caf502db91ab09eea48fc8a902dacf6bf40f24c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278632"
---
# <a name="role-based-access-control-in-azure-automation"></a>Azure 自動化中的角色型存取控制

角色型存取控制 (RBAC) 可以啟用對 Azure 資源的存取權管理。 您可以使用 [RBAC](../role-based-access-control/overview.md) 來區隔小組內的職責，僅授與使用者、群組和應用程式執行作業所需的存取權範圍。 您可以使用 Azure 門戶、Azure 命令列工具或 Azure 管理 API 向使用者授予基於角色的存取權限。

## <a name="roles-in-automation-accounts"></a>自動化帳戶中的角色

在 Azure 自動化中，於自動化帳戶範圍將適當的 RBAC 角色指派給使用者、群組和應用程式，即可授與存取權。 以下是自動化帳戶支援的內建角色：

| **角色** | **描述** |
|:--- |:--- |
| 擁有者 |「擁有者」角色可讓您存取「自動化」帳戶內的所有資源和動作，包括提供存取權給其他使用者、群組及應用程式來管理「自動化」帳戶。 |
| 參與者 |參與者角色可讓您管理各個項目，修改其他使用者的自動化帳戶存取權限除外。 |
| 讀取者 |讀取者角色可讓您檢視自動化帳戶中的所有資源，但無法進行任何變更。 |
| 自動化運算子 |自動化操作員角色可讓您檢視 Runbook 的名稱和屬性，以及在自動化帳戶中建立及管理所有 Runbook 的作業。 如果要保護自動化帳戶資源（如憑據資產和 Runbook）不被查看或修改，但仍允許組織成員執行這些 Runbook，則此角色非常有用。 |
|自動化作業運算子|「自動化作業操作員」角色可讓您建立和管理自動化帳戶中的所有 Runbook 作業。|
|自動化 Runbook 運算子|「自動化 Runbook 操作員」角色可讓您檢視 Runbook 的名稱和屬性。|
| Log Analytics 參與者 | 「Log Analytics 參與者」角色可讓您讀取所有監視資料和編輯監視設定。 編輯監視設定包括將 VM 延伸模組新增至 VM、讀取儲存體帳戶金鑰以便能夠設定從 Azure 儲存體收集記錄、建立及設定「自動化」帳戶、新增解決方案，以及設定所有 Azure 資源上的 Azure 診斷。|
| Log Analytics 讀者 | 「Log Analytics 讀者」角色可讓您檢視和搜尋所有監視資料，以及檢視監視設定。 這包括檢視所有 Azure 資源上的 Azure 診斷設定。 |
| 監視參與者 | 「監視參與者」角色可讓您讀取所有監視資料和更新監視設定。|
| 監視讀取器 | 「監視讀者」角色可讓您讀取所有監視資料。 |
| 使用者存取系統管理員 |使用者存取系統管理員角色可讓您管理 Azure 自動化帳戶的使用者存取。 |

## <a name="role-permissions"></a>角色權限

下表描述賦予每個角色的特定權限。 這可以包括會授與權限的 Actions，以及會限制權限的 NotActions。

### <a name="owner"></a>擁有者

「擁有者」可以管理所有項目，包括存取權。 下表說明針對此角色授與的權限：

|動作|描述|
|---|---|
|Microsoft.Automation/automationAccounts/|建立和管理所有類型的資源。|

### <a name="contributor"></a>參與者

「參與者」可以管理存取權以外的所有項目。 下表說明針對此角色授與和拒絕的權限：

|**動作**  |**描述**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/|建立和管理所有類型的資源|
|**無法執行的動作**||
|Microsoft.Authorization/*/Delete| 刪除角色和角色指派。       |
|Microsoft.Authorization/*/Write     |  建立角色和角色指派。       |
|Microsoft.Authorization/elevateAccess/Action    | 無法建立「使用者存取系統管理員」。       |

### <a name="reader"></a>讀取者

「讀取者」角色可以檢視「自動化」帳戶中的所有資源，但無法進行任何變更。

|**動作**  |**描述**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/read|檢視「自動化」帳戶中的所有資源。 |

### <a name="automation-operator"></a>自動化運算子

自動化操作員可在自動化帳戶中建立和管理作業，以及讀取所有 Runbook 的名稱和屬性。  注意：如果您想要控制操作員存取個別 Runbook，請不要設定此角色，而是改為搭配使用「自動化作業操作員」和「自動化 Runbook 操作員」角色。 下表說明針對此角色授與的權限：

|**動作**  |**描述**  |
|---------|---------|
|Microsoft.Authorization/*/read|讀取授權。|
|Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read|讀取混合式 Runbook 背景工作角色資源。|
|Microsoft.Automation/automationAccounts/jobs/read|列出 Runbook 的作業。|
|Microsoft.Automation/automationAccounts/jobs/resume/action|繼續執行暫停的作業。|
|Microsoft.Automation/automationAccounts/jobs/stop/action|取消進行中的作業。|
|Microsoft.Automation/automationAccounts/jobs/streams/read|讀取「作業串流」和「輸出」。|
|Microsoft.Automation/automationAccounts/jobs/output/read|取得作業的輸出。|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|暫停進行中的作業。|
|Microsoft.Automation/automationAccounts/jobs/write|建立作業。|
|Microsoft.Automation/automationAccounts/jobSchedules/read|取得 Azure 自動化作業排程。|
|Microsoft.Automation/automationAccounts/jobSchedules/write|建立 Azure 自動化作業排程。|
|Microsoft.Automation/automationAccounts/linkedWorkspace/read|獲取連結到自動化帳戶的工作區。|
|Microsoft.Automation/automationAccounts/read|取得 Azure 自動化帳戶。|
|Microsoft.Automation/automationAccounts/runbooks/read|取得 Azure 自動化 Runbook。|
|Microsoft.Automation/automationAccounts/schedules/read|取得 Azure 自動化排程資產。|
|Microsoft.Automation/automationAccounts/schedules/write|建立或更新 Azure 自動化排程資產。|
|Microsoft.Resources/subscriptions/resourceGroups/read      |讀取角色和角色指派。         |
|Microsoft.Resources/deployments/*      |建立和管理資源群組部署。         |
|Microsoft.Insights/alertRules/*      | 建立和管理警示規則。        |
|Microsoft.Support/* |建立和管理支援票證。|

### <a name="automation-job-operator"></a>自動化作業運算子

授與「自動化作業操作員」角色時會在「自動化」帳戶範圍內授與。這可讓操作員有權建立和管理帳戶中所有 Runbook 作業。 下表說明針對此角色授與的權限：

|**動作**  |**描述**  |
|---------|---------|
|Microsoft.Authorization/*/read|讀取授權。|
|Microsoft.Automation/automationAccounts/jobs/read|列出 Runbook 的作業。|
|Microsoft.Automation/automationAccounts/jobs/resume/action|繼續執行暫停的作業。|
|Microsoft.Automation/automationAccounts/jobs/stop/action|取消進行中的作業。|
|Microsoft.Automation/automationAccounts/jobs/streams/read|讀取「作業串流」和「輸出」。|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|暫停進行中的作業。|
|Microsoft.Automation/automationAccounts/jobs/write|建立作業。|
|Microsoft.Resources/subscriptions/resourceGroups/read      |  讀取角色和角色指派。       |
|Microsoft.Resources/deployments/*      |建立和管理資源群組部署。         |
|Microsoft.Insights/alertRules/*      | 建立和管理警示規則。        |
|Microsoft.Support/* |建立和管理支援票證。|

### <a name="automation-runbook-operator"></a>自動化 Runbook 運算子

授與「自動化 Runbook 運算子」角色時，會在 Runbook 範圍授與。 「自動化 Runbook 操作員」可檢視 Runbook 的名稱和屬性。此角色與「自動化作業操作員」角色結合，可讓操作員也建立及管理 Runbook 的作業。 下表說明針對此角色授與的權限：

|**動作**  |**描述**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/runbooks/read     | 列出 Runbook。        |
|Microsoft.Authorization/*/read      | 讀取授權。        |
|Microsoft.Resources/subscriptions/resourceGroups/read      |讀取角色和角色指派。         |
|Microsoft.Resources/deployments/*      | 建立和管理資源群組部署。         |
|Microsoft.Insights/alertRules/*      | 建立和管理警示規則。        |
|Microsoft.Support/*      | 建立和管理支援票證。        |

### <a name="log-analytics-contributor"></a>Log Analytics 參與者

「Log Analytics 參與者」角色可以讀取所有監視資料和編輯監視設定。 編輯監視設定包括將 VM 延伸模組新增至 VM、讀取儲存體帳戶金鑰以便能夠設定從「Azure 儲存體」收集記錄、建立及設定「自動化」帳戶、新增解決方案，以及設定所有 Azure 資源上的 Azure 診斷。 下表說明針對此角色授與的權限：

|**動作**  |**描述**  |
|---------|---------|
|*/read|讀取密碼以外的所有類型的資源。|
|Microsoft.Automation/automationAccounts/*|管理自動化帳戶。|
|Microsoft.ClassicCompute/virtualMachines/extensions/*|建立和管理虛擬機器延伸模組。|
|Microsoft.ClassicStorage/storageAccounts/listKeys/action|列出傳統儲存體帳戶金鑰。|
|Microsoft.Compute/virtualMachines/extensions/*|建立和管理傳統虛擬機器延伸模組。|
|Microsoft.Insights/alertRules/*|讀取/寫入/刪除警示規則。|
|Microsoft.Insights/diagnosticSettings/*|讀取/寫入/刪除診斷設定。|
|Microsoft.OperationalInsights/*|管理 Azure 監視器日誌。|
|Microsoft.OperationsManagement/*|管理工作區中的解決方案。|
|Microsoft.Resources/deployments/*|建立和管理資源群組部署。|
|Microsoft.Resources/subscriptions/resourcegroups/deployments/*|建立和管理資源群組部署。|
|Microsoft.Storage/storageAccounts/listKeys/action|列出儲存體帳戶金鑰。|
|Microsoft.Support/*|建立和管理支援票證。|

### <a name="log-analytics-reader"></a>Log Analytics 讀者

「Log Analytics 讀者」可以檢視和搜尋所有監視資料，以及檢視監視設定，包括檢視所有 Azure 資源上的 Azure 診斷設定。 下表說明針對此角色授與或拒絕的權限：

|**動作**  |**描述**  |
|---------|---------|
|*/read|讀取密碼以外的所有類型的資源。|
|Microsoft.OperationalInsights/workspaces/analytics/query/action|管理 Azure 監視器日誌中的查詢。|
|Microsoft.OperationalInsights/workspaces/search/action|搜索 Azure 監視器日誌資料。|
|Microsoft.Support/*|建立和管理支援票證。|
|**無法執行的動作**| |
|Microsoft.OperationalInsights/workspaces/sharedKeys/read|無法讀取共用存取金鑰。|

### <a name="monitoring-contributor"></a>監視參與者

「監視參與者」可以讀取所有監視資料和更新監視設定。 下表說明針對此角色授與的權限：

|**動作**  |**描述**  |
|---------|---------|
|*/read|讀取密碼以外的所有類型的資源。|
|Microsoft.AlertsManagement/alerts/*|管理「警示」。|
|Microsoft.AlertsManagement/alertsSummary/*|管理「警示」儀表板。|
|Microsoft.Insights/AlertRules/*|管理警示規則。|
|Microsoft.Insights/components/*|管理 Application Insights 元件。|
|Microsoft.Insights/DiagnosticSettings/*|管理診斷設定。|
|Microsoft.Insights/eventtypes/*|列出訂用帳戶中的活動記錄檔事件 (管理事件)。 此權限適用於以程式設計方式存取和入口網站存取活動記錄檔。|
|Microsoft.Insights/LogDefinitions/*|此為使用者需要透過入口網站存取活動記錄時所需的權限。 列出活動記錄檔中的記錄檔分類。|
|Microsoft.Insights/MetricDefinitions/*|讀取度量定義 (可用資源的度量類型清單)。|
|Microsoft.Insights/Metrics/*|讀取資源的度量。|
|Microsoft.Insights/Register/Action|註冊 Microsoft Insights 提供者。|
|Microsoft.Insights/webtests/*|管理 Application Insights Web 測試。|
|Microsoft.OperationalInsights/workspaces/intelligencepacks/*|管理 Azure 監視器日誌解決方案包。|
|Microsoft.OperationalInsights/workspaces/savedSearches/*|管理 Azure 監視器日誌保存的搜索。|
|Microsoft.OperationalInsights/workspaces/search/action|搜尋 Log Analytics 工作區。|
|Microsoft.OperationalInsights/workspaces/sharedKeys/action|列出 Log Analytics 工作區的金鑰。|
|Microsoft.OperationalInsights/workspaces/storageinsightconfigs/*|管理 Azure 監視器日誌存儲洞察配置。|
|Microsoft.Support/*|建立和管理支援票證。|
|Microsoft.WorkloadMonitor/workloads/*|管理「工作負載」。|

### <a name="monitoring-reader"></a>監視讀取器

「監視讀者」可以讀取所有監視資料。 下表說明針對此角色授與的權限：

|**動作**  |**描述**  |
|---------|---------|
|*/read|讀取密碼以外的所有類型的資源。|
|Microsoft.OperationalInsights/workspaces/search/action|搜尋 Log Analytics 工作區。|
|Microsoft.Support/*|建立和管理支援票證|

### <a name="user-access-administrator"></a>使用者存取系統管理員

「使用者存取系統管理員」可以管理使用者對 Azure 資源的存取權。 下表說明針對此角色授與的權限：

|**動作**  |**描述**  |
|---------|---------|
|*/read|讀取所有資源|
|Microsoft.Authorization/*|管理授權|
|Microsoft.Support/*|建立和管理支援票證|

## <a name="onboarding"></a>登入

下表說明針對變更追蹤或更新管理解決方案，讓虛擬機器上線所需的最基本必要權限。

### <a name="onboarding-from-a-virtual-machine"></a>從虛擬機器上線

|**動作**  |[權限]****  |**最小範圍**  |
|---------|---------|---------|
|寫入新的部署      | Microsoft.Resources/deployments/*          |訂用帳戶          |
|寫入新的資源群組      | Microsoft.Resources/subscriptions/resourceGroups/write        | 訂用帳戶          |
|建立新的預設「工作區」      | Microsoft.OperationalInsights/workspaces/write         | 資源群組         |
|建立新的「帳戶」      |  Microsoft.Automation/automationAccounts/write        |資源群組         |
|連結工作區和帳戶      |Microsoft.OperationalInsights/workspaces/write</br>Microsoft.Automation/automationAccounts/read|工作區</br>自動化帳戶
|建立 MMA 延伸模組      | Microsoft.Compute/virtualMachines/write         | 虛擬機器         |
|建立已儲存的搜尋      | Microsoft.OperationalInsights/workspaces/write          | 工作區         |
|建立範圍設定      | Microsoft.OperationalInsights/workspaces/write          | 工作區         |
|上線狀態檢查 - 讀取工作區      | Microsoft.OperationalInsights/workspaces/read         | 工作區         |
|上線狀態檢查 - 讀取帳戶的已連結工作區屬性     | Microsoft.Automation/automationAccounts/read      | 自動化帳戶        |
|上線狀態檢查 - 讀取解決方案      | Microsoft.OperationalInsights/workspaces/intelligencepacks/read          | 解決方法         |
|上線狀態檢查 - 讀取 VM      | Microsoft.Compute/virtualMachines/read         | 虛擬機器         |
|上線狀態檢查 - 讀取帳戶      | Microsoft.Automation/automationAccounts/read  |  自動化帳戶   |
| 載入工作區檢查 VM<sup>1</sup>       | Microsoft.OperationalInsights/workspaces/read         | 訂用帳戶         |
| 註冊日誌分析提供程式 |微軟.見解/註冊/操作 | 訂用帳戶|

<sup>1</sup>通過 VM 門戶體驗，需要此許可權。

### <a name="onboarding-from-automation-account"></a>從自動化帳戶上線

|**動作**  |[權限]**** |**最基本範圍**  |
|---------|---------|---------|
|建立新的部署     | Microsoft.Resources/deployments/*        | 訂用帳戶         |
|建立新的資源群組     | Microsoft.Resources/subscriptions/resourceGroups/write         | 訂用帳戶        |
|AutomationOnboarding 刀鋒視窗 - 建立新的工作區     |Microsoft.OperationalInsights/workspaces/write           | 資源群組        |
|AutomationOnboarding 刀鋒視窗 - 讀取已連結的工作區     | Microsoft.Automation/automationAccounts/read        | 自動化帳戶       |
|AutomationOnboarding 刀鋒視窗 - 讀取解決方案     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read         | 解決方法        |
|AutomationOnboarding 刀鋒視窗 - 讀取工作區     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read        | 工作區        |
|為工作區和帳戶建立連結     | Microsoft.OperationalInsights/workspaces/write        | 工作區        |
|寫入 Shoebox 的帳戶      | Microsoft.Automation/automationAccounts/write        | 帳戶        |
|建立/編輯已儲存的搜尋     | Microsoft.OperationalInsights/workspaces/write        | 工作區        |
|建立/編輯範圍設定     | Microsoft.OperationalInsights/workspaces/write        | 工作區        |
| 註冊日誌分析提供程式 |微軟.見解/註冊/操作 | 訂用帳戶|
|**步驟 2 - 讓多個 VM 上線**     |         |         |
|VMOnboarding 刀鋒視窗 - 建立 MMA 延伸模組     | Microsoft.Compute/virtualMachines/write           | 虛擬機器        |
|建立/編輯已儲存的搜尋     | Microsoft.OperationalInsights/workspaces/write           | 工作區        |
|建立/編輯範圍設定  | Microsoft.OperationalInsights/workspaces/write   | 工作區|

## <a name="update-management"></a>更新管理

更新管理的觸角會遍及多項服務來提供其服務。 下表說明管理更新管理部署所需的權限：

|**資源**  |**角色**  |**Scope**  |
|---------|---------|---------|
|自動化帳戶     | Log Analytics 參與者       | 自動化帳戶        |
|自動化帳戶    | 虛擬機器參與者        | 帳戶的「資源群組」        |
|Log Analytics 工作區     | Log Analytics 參與者| Log Analytics 工作區        |
|Log Analytics 工作區 |Log Analytics 讀者| 訂用帳戶|
|解決方法     |Log Analytics 參與者         | 解決方法|
|虛擬機器     | 虛擬機器參與者        | 虛擬機器        |

## <a name="configure-rbac-for-your-automation-account"></a>為您的自動化帳戶設定 RBAC

以下部分介紹如何通過[門戶](#configure-rbac-using-the-azure-portal)和[PowerShell](#configure-rbac-using-powershell)在自動化帳戶上配置 RBAC。

### <a name="configure-rbac-using-the-azure-portal"></a>使用 Azure 入口網站設定 RBAC

1. 登入 [Azure 入口網站](https://portal.azure.com/)，並從 [自動化帳戶] 頁面開啟您的自動化帳戶。
2. 按一下左上角的訪問**控制 （IAM）** 控制項以打開存取控制 （IAM） 頁面。 您可以使用此頁面添加新使用者、組和應用程式來管理自動化帳戶，並查看可為自動化帳戶配置的現有角色。
3. 按一下 [角色指派]**** 索引標籤。

   ![[存取] 按鈕](media/automation-role-based-access-control/automation-01-access-button.png)

#### <a name="add-a-new-user-and-assign-a-role"></a>加入新使用者並指派角色

1. 在"存取控制 （IAM）"頁中，按一下 **"添加角色指派**"。 此操作將打開"添加角色指派"頁，您可以在其中添加使用者、組或應用程式，並分配相應的角色。

2. 從可用角色的清單中選取角色。 您可以選擇「自動化」帳戶支援的任何可用內建角色，或已定義的任何自訂角色。

3. 鍵入要在 **"選擇"** 欄位中授予許可權的使用者的使用者名。 從清單中選擇使用者，然後按一下"**保存**"。

   ![新增使用者](media/automation-role-based-access-control/automation-04-add-users.png)

   現在，您應該看到使用者添加到"使用者"頁，並分配了所選角色。

   ![列出使用者](media/automation-role-based-access-control/automation-05-list-users.png)

   您也可以從 [角色] 頁面指派角色給使用者。
4. 從 [存取控制 (IAM)] 頁面按一下 [角色]****，以開啟 [角色] 頁面。 您可以查看角色的名稱以及分配給該角色的使用者和組數。

    ![從 [使用者] 頁面指派角色](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)

   > [!NOTE]
   > 您只能在自動化帳戶範圍內設置基於角色的存取控制，而不能在自動化帳戶下的任何資源設置基於角色的存取控制。

#### <a name="remove-a-user"></a>移除使用者

您可以針對未管理「自動化」帳戶的使用者，或不再為組織工作的使用者，移除存取權限。 以下是移除使用者的步驟：

1. 從訪問控制項 （IAM） 頁中，選擇要刪除的使用者，然後按一下"**刪除**"。
2. 按一下指派詳細資料窗格上的 [移除]**** 按鈕。
3. 按一下 [是] **** 以確認移除。

   ![移除使用者](media/automation-role-based-access-control/automation-08-remove-users.png)

### <a name="configure-rbac-using-powershell"></a>使用 PowerShell 設定 RBAC

您還可以使用以下[Azure PowerShell Cmdlet](../role-based-access-control/role-assignments-powershell.md)配置對自動化帳戶的基於角色的訪問：

[Get-AzureRmRoleDefinition](/previous-versions/azure/mt603792(v=azure.100)) 會列出 Azure Active Directory 中可用的所有 RBAC 角色。 可以使用此 Cmdlet 與*Name*參數一起列出特定角色可以執行的所有操作。

```azurepowershell-interactive
Get-AzureRmRoleDefinition -Name 'Automation Operator'
```

以下是範例輸出：

```azurepowershell
Name             : Automation Operator
Id               : d3881f73-407a-4167-8283-e981cbba0404
IsCustom         : False
Description      : Automation Operators are able to start, stop, suspend, and resume jobs
Actions          : {Microsoft.Authorization/*/read, Microsoft.Automation/automationAccounts/jobs/read, Microsoft.Automation/automationAccounts/jobs/resume/action,
                   Microsoft.Automation/automationAccounts/jobs/stop/action...}
NotActions       : {}
AssignableScopes : {/}
```

[Get-AzureRmRoleAssignment](/previous-versions/azure/mt619413(v=azure.100)) 會列出在指定範圍的 Azure AD RBAC 角色指派。 沒有任何參數，此 Cmdlet 返回訂閱下的所有角色指派。 使用 *"展開主體組"* 參數列出指定使用者以及使用者所屬的組的訪問分配。

**示例：** 使用以下 Cmdlet 列出自動化帳戶中的所有使用者及其角色。

```azurepowershell-interactive
Get-AzureRMRoleAssignment -scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

以下是範例輸出：

```powershell
RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Automation/automationAccounts/myAutomationAccount/provid
                     ers/Microsoft.Authorization/roleAssignments/cc594d39-ac10-46c4-9505-f182a355c41f
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Automation/automationAccounts/myAutomationAccount
DisplayName        : admin@contoso.com
SignInName         : admin@contoso.com
RoleDefinitionName : Automation Operator
RoleDefinitionId   : d3881f73-407a-4167-8283-e981cbba0404
ObjectId           : 15f26a47-812d-489a-8197-3d4853558347
ObjectType         : User
```

使用[New-AzureRmRole 分配](/previous-versions/azure/mt603580(v=azure.100))將對使用者、組和應用程式的訪問分配給特定作用域。
    
**範例：** 請使用下列命令來為「自動化帳戶」範圍內的使用者指派「自動化運算子」角色。

```azurepowershell-interactive
New-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to grant access> -RoleDefinitionName 'Automation operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

以下是範例輸出：

```azurepowershell
RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/myResourceGroup/Providers/Microsoft.Automation/automationAccounts/myAutomationAccount/provid
                     ers/Microsoft.Authorization/roleAssignments/25377770-561e-4496-8b4f-7cba1d6fa346
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/myResourceGroup/Providers/Microsoft.Automation/automationAccounts/myAutomationAccount
DisplayName        : admin@contoso.com
SignInName         : admin@contoso.com
RoleDefinitionName : Automation Operator
RoleDefinitionId   : d3881f73-407a-4167-8283-e981cbba0404
ObjectId           : f5ecbe87-1181-43d2-88d5-a8f5e9d8014e
ObjectType         : User
```

使用 [Remove-AzureRmRoleAssignment](/previous-versions/azure/mt603781(v=azure.100)) 從特定範圍移除所指定使用者、群組或應用程式的存取權。

**範例：** 請使用下列命令，從「自動化」帳戶範圍內的「自動化操作員」角色中移除使用者。

```azurepowershell-interactive
Remove-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to remove> -RoleDefinitionName 'Automation Operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

在前面的示例中，將"要刪除的使用者的登入識別碼"、"訂閱 ID"、"資源組名稱"和"自動化帳戶名稱"替換為您的帳戶詳細資訊。 在繼續刪除使用者角色指派之前，提示確認時選擇 **"是**"。

### <a name="user-experience-for-automation-operator-role---automation-account"></a>自動化操作員角色的使用者體驗 - 自動化帳戶

當分配給自動化帳戶作用域上的自動化操作員角色的使用者查看他/她所為其分配的自動化帳戶時，使用者只能查看在自動化帳戶中創建的 Runbook、Runbook 作業和計畫的清單。 此使用者無法查看這些專案的定義。 使用者可以啟動、停止、掛起、恢復或計畫 Runbook 作業。 但是，使用者無法訪問其他自動化資源，如配置、混合輔助角色組或 DSC 節點。

![無法存取資源](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)

## <a name="configure-rbac-for-runbooks"></a>為 Runbook 配置 RBAC

Azure 自動化允許您將 RBAC 分配給特定的 Runbook。 為此，運行以下腳本以將使用者添加到特定 Runbook。 自動化帳戶管理員或租戶管理員可以運行此腳本。

```azurepowershell-interactive
$rgName = "<Resource Group Name>" # Resource Group name for the Automation account
$automationAccountName ="<Automation account name>" # Name of the Automation account
$rbName = "<Name of Runbook>" # Name of the runbook
$userId = "<User ObjectId>" # Azure Active Directory (AAD) user's ObjectId from the directory

# Gets the Automation account resource
$aa = Get-AzureRmResource -ResourceGroupName $rgName -ResourceType "Microsoft.Automation/automationAccounts" -ResourceName $automationAccountName

# Get the Runbook resource
$rb = Get-AzureRmResource -ResourceGroupName $rgName -ResourceType "Microsoft.Automation/automationAccounts/runbooks" -ResourceName "$automationAccountName/$rbName"

# The Automation Job Operator role only needs to be run once per user.
New-AzureRmRoleAssignment -ObjectId $userId -RoleDefinitionName "Automation Job Operator" -Scope $aa.ResourceId

# Adds the user to the Automation Runbook Operator role to the Runbook scope
New-AzureRmRoleAssignment -ObjectId $userId -RoleDefinitionName "Automation Runbook Operator" -Scope $rb.ResourceId
```

腳本運行後，讓使用者登錄到 Azure 門戶並查看**所有資源**。 在清單中，使用者可以查看已添加為自動化 Runbook 運算子的運行簿。

![入口網站中的 Runbook RBAC](./media/automation-role-based-access-control/runbook-rbac.png)

### <a name="user-experience-for-automation-operator-role---runbook"></a>自動化操作員角色的使用者體驗 - Runbook

當分配給 Runbook 作用域上的自動化操作員角色的使用者查看分配的 Runbook 時，使用者只能啟動 Runbook 並查看 Runbook 作業。

![只有啟動的存取權](media/automation-role-based-access-control/automation-only-start.png)

## <a name="next-steps"></a>後續步驟

* 有關為 Azure 自動化配置 RBAC 的方法的資訊，請參閱[使用 Azure PowerShell 管理 RBAC。](../role-based-access-control/role-assignments-powershell.md)
* 有關啟動 Runbook 的詳細資訊，請參閱啟動[Runbook](automation-starting-a-runbook.md)。
* 有關 Runbook 類型的資訊，請參閱[Azure 自動化運行簿類型](automation-runbook-types.md)。