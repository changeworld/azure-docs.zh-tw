---
title: 在 Azure 監視器中管理 Log Analytics 工作區 | Microsoft Docs
description: 您可以使用資源、工作區或表級版權管理對 Azure 監視器中日誌分析工作區中存儲的資料的訪問。 本文詳細介紹了如何完成。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/22/2019
ms.openlocfilehash: 1e559309b8e8d9768ca2f79dabfb01ec6086a961
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348712"
---
# <a name="manage-access-to-log-data-and-workspaces-in-azure-monitor"></a>管理對 Azure 監視器中日誌資料和工作區的訪問

Azure 監視器將[日誌](data-platform-logs.md)資料存儲在日誌分析工作區中。 工作區是包含資料和配置資訊的容器。 要管理對日誌資料的訪問，請執行與工作區相關的各種管理工作。

本文介紹如何管理對日誌的訪問和管理包含日誌的工作區，包括如何授予對以下項的存取權限： 

* 使用工作區許可權的工作區。
* 需要訪問使用基於 Azure 角色的存取控制 （RBAC） 從特定資源記錄資料的使用者。
* 需要訪問使用 Azure RBAC 在工作區中特定表中記錄資料的使用者。

## <a name="configure-access-control-mode"></a>配置存取控制模式

可以從 Azure 門戶或使用 Azure PowerShell 查看工作區配置[的存取控制模式](design-logs-deployment.md)。  您可以使用以下支援的方法之一更改此設置：

* Azure 入口網站

* Azure PowerShell

* Azure Resource Manager 範本

### <a name="from-the-azure-portal"></a>從 Azure 入口網站

您可以在**日誌分析工作區**功能表中的工作區**的"概述"** 頁上查看當前工作區存取控制模式。

![查看工作區存取控制模式](media/manage-access/view-access-control-mode.png)

1. 登錄到 中的[https://portal.azure.com](https://portal.azure.com)Azure 門戶。
1. 在 Azure 門戶中，選擇工作區>日誌分析工作區。

可以從工作區的"**屬性"** 頁更改此設置。 如果您沒有配置工作區的許可權，則更改設置將被禁用。

![更改工作區訪問模式](media/manage-access/change-access-control-mode.png)

### <a name="using-powershell"></a>使用 PowerShell

使用以下命令檢查訂閱中所有工作區的存取控制模式：

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {$_.Name + ": " + $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions}
```

輸出應該如以下所示：

```
DefaultWorkspace38917: True
DefaultWorkspace21532: False
```

的值`False`表示工作區配置了工作區上下文訪問模式。  的值`True`表示工作區配置了資源上下文訪問模式。

> [!NOTE]
> 如果返回的工作區沒有布林值且為空，則這也與`False`值的結果匹配。
>

使用以下腳本將特定工作區的存取控制模式設置為資源上下文許可權：

```powershell
$WSName = "my-workspace"
$Workspace = Get-AzResource -Name $WSName -ExpandProperties
if ($Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null)
    { $Workspace.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else
    { $Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $Workspace.ResourceId -Properties $Workspace.Properties -Force
```

使用以下腳本為資源上下文許可權訂閱中的所有工作區設置存取控制模式：

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {
if ($_.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null)
    { $_.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else
    { $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $_.ResourceId -Properties $_.Properties -Force
}
```

### <a name="using-a-resource-manager-template"></a>使用資源管理器範本

要在 Azure 資源管理器範本中配置訪問模式，請將工作區上的**啟用"登錄使用唯一資源許可權"** 功能標誌設置為以下值之一。

* **false**：將工作區設置為工作區上下文許可權。 如果未設置標誌，則此設置。
* **為**：將工作區設置為資源上下文許可權。

## <a name="manage-access-using-workspace-permissions"></a>使用工作區版權管理存取權限

每個工作區可以有多個相關聯的帳戶，而每個帳戶可以存取多個工作區。 訪問使用基於[Azure 的角色訪問](../../role-based-access-control/role-assignments-portal.md)進行管理。

下列活動也需要 Azure 權限︰

|動作 |所需的 Azure 權限 |注意 |
|-------|-------------------------|------|
| 添加和刪除監視解決方案 | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | 必須在資源群組或訂用帳戶層級授與這些權限。 |
| 變更定價層 | `Microsoft.OperationalInsights/workspaces/*/write` | |
| 檢視 [備份]** 和 [Site Recovery]** 解決方案圖格中的資料 | 系統管理員/共同管理員 | 存取使用傳統部署模型所部署的資源 |
| 在 Azure 入口網站中建立工作區 | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||
| 查看工作區基本屬性並在門戶中輸入工作區邊欄選項卡 | `Microsoft.OperationalInsights/workspaces/read` ||
| 使用任何介面查詢日誌 | `Microsoft.OperationalInsights/workspaces/query/read` ||
| 使用查詢訪問所有日誌類型 | `Microsoft.OperationalInsights/workspaces/query/*/read` ||
| 訪問特定日誌表 | `Microsoft.OperationalInsights/workspaces/query/<table_name>/read` ||
| 讀取工作區鍵以允許將日誌發送到此工作區 | `Microsoft.OperationalInsights/workspaces/sharedKeys/action` ||

## <a name="manage-access-using-azure-permissions"></a>使用 Azure 版權管理訪問

若要使用 Azure 權限授與 Log Analytics 工作區的存取權，請遵循[使用角色指派來管理 Azure 訂用帳戶資源的存取權](../../role-based-access-control/role-assignments-portal.md)中的步驟。 例如自訂角色，請參閱[自訂角色示例](#custom-role-examples)

Azure 有兩個內建的 Log Analytics 工作區角色：

* Log Analytics 讀者
* Log Analytics 參與者

*Log Analytics 讀者*角色的成員可以：

* 檢視及搜尋所有監視資料
* 檢視監視設定，包括檢視所有 Azure 資源上檢視的 Azure 診斷組態。

Log Analytics 讀者角色包含下列 Azure 動作：

| 類型    | 權限 | 描述 |
| ------- | ---------- | ----------- |
| 動作 | `*/read`   | 檢視所有 Azure 資源和資源組態的能力。 包括檢視： <br> 虛擬機器擴充功能 <br> 在資源上設定 Azure 診斷 <br> 所有資源的所有屬性和設置。 <br> 對於工作區，它允許完全不受限制地讀取工作區設置和對資料執行查詢的許可權。 請參閱上面的更精細的選項。 |
| 動作 | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | 已棄用，無需將其分配給使用者。 |
| 動作 | `Microsoft.OperationalInsights/workspaces/search/action` | 已棄用，無需將其分配給使用者。 |
| 動作 | `Microsoft.Support/*` | 開啟支援案例的能力 |
|不是動作 | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | 防止讀取在使用資料收集 API 和安裝代理程式時所需的工作區金鑰。 這可防止使用者將新資源新增至工作區 |

*Log Analytics 參與者*角色的成員可以：

* 包括*日誌分析讀取器角色*的擁有權限，允許使用者讀取所有監視資料
* 創建和配置自動化帳戶
* 新增及移除管理解決方案

    > [!NOTE]
    > 若要成功執行後面兩個動作，就必須在資源群組或訂用帳戶層級授與此權限。

* 讀取存儲帳戶金鑰
* 配置 Azure 存儲中的日誌集合
* 編輯 Azure 資源的監視設定，包括
  * 將 VM 擴充功能新增至 VM
  * 在所有 Azure 資源上設定 Azure 診斷

> [!NOTE]
> 您可以使用將虛擬機器擴充功能新增至虛擬機器的功能，取得虛擬機器的完整控制權。

Log Analytics 參與者角色包含下列 Azure 動作：

| 權限 | 描述 |
| ---------- | ----------- |
| `*/read`     | 檢視所有資源和資源組態的能力。 包括檢視： <br> 虛擬機器擴充功能 <br> 在資源上設定 Azure 診斷 <br> 所有資源的所有屬性和設置。 <br> 對於工作區，它允許完全不受限制地讀取工作區設置和對資料執行查詢的許可權。 請參閱上面的更精細的選項。 |
| `Microsoft.Automation/automationAccounts/*` | 建立及設定 Azure 自動化帳戶的能力，包括新增和編輯 Runbook |
| `Microsoft.ClassicCompute/virtualMachines/extensions/*` <br> `Microsoft.Compute/virtualMachines/extensions/*` | 新增、更新及移除虛擬機器擴充功能，包括 Microsoft Monitoring Agent 擴充功能和 OMS Agent for Linux 擴充功能 |
| `Microsoft.ClassicStorage/storageAccounts/listKeys/action` <br> `Microsoft.Storage/storageAccounts/listKeys/action` | 檢視儲存體帳戶金鑰。 需具備此權限，才能設定 Log Analytics 以讀取 Azure Blob 儲存體帳戶中的記錄 |
| `Microsoft.Insights/alertRules/*` | 新增、更新和移除警示規則 |
| `Microsoft.Insights/diagnosticSettings/*` | 在 Azure 資源上新增、更新和移除診斷設定 |
| `Microsoft.OperationalInsights/*` | 添加、更新和刪除日誌分析工作區的配置。 要編輯工作區高級設置，使用者需要`Microsoft.OperationalInsights/workspaces/write`。 |
| `Microsoft.OperationsManagement/*` | 新增及移除管理解決方案 |
| `Microsoft.Resources/deployments/*` | 建立及刪除部署。 需具備此權限，才能新增及移除解決方案、工作區和自動化帳戶 |
| `Microsoft.Resources/subscriptions/resourcegroups/deployments/*` | 建立及刪除部署。 需具備此權限，才能新增及移除解決方案、工作區和自動化帳戶 |

若要新增及移除某個使用者角色的使用者，必須具備 `Microsoft.Authorization/*/Delete` 和 `Microsoft.Authorization/*/Write` 權限。

使用下列角色，賦予使用者不同範圍的存取權：

* 訂用帳戶 - 存取訂用帳戶中的所有工作區
* 資源群組 - 存取資源群組中的所有工作區
* 資源 - 僅存取指定的工作區

我們建議在資源級別（工作區）執行分配，以確保準確的存取控制。 使用[自訂角色](../../role-based-access-control/custom-roles.md)建立具備所需特定權限的角色。

### <a name="resource-permissions"></a>資源許可權

當使用者使用資源上下文訪問從工作區查詢日誌時，他們將對資源具有以下許可權：

| 權限 | 描述 |
| ---------- | ----------- |
| `Microsoft.Insights/logs/<tableName>/read`<br><br>範例：<br>`Microsoft.Insights/logs/*/read`<br>`Microsoft.Insights/logs/Heartbeat/read` | 能夠查看資源的所有日誌資料。  |
| `Microsoft.Insights/diagnosticSettings/write` | 能夠配置診斷設置以允許為此資源設置日誌。 |

`/read`許可權通常_\*_ 從包含_\*/read 或_許可權（如內置[讀取器](../../role-based-access-control/built-in-roles.md#reader)和[參與者](../../role-based-access-control/built-in-roles.md#contributor)角色）的角色授予。 包含特定操作或專用內置角色的自訂角色可能不包括此許可權。

如果要為不同的表創建不同的存取控制，請參閱定義下面的[每個表存取控制](#table-level-rbac)。

## <a name="custom-role-examples"></a>自訂角色示例

1. 要授予使用者對其資源記錄資料的存取權限，請執行以下操作：

    * 將工作區存取控制模式配置為**使用工作區或資源許可權**

    * 向使用者`*/read`授予`Microsoft.Insights/logs/*/read`其資源的許可權。 如果已在工作區上為其分配了[日誌分析讀取器](../../role-based-access-control/built-in-roles.md#reader)角色，則就足夠了。

2. 要授予使用者對其資源記錄資料的存取權限，並配置其資源以將日誌發送到工作區，請執行以下操作：

    * 將工作區存取控制模式配置為**使用工作區或資源許可權**

    * 在工作區上授予使用者以下許可權：`Microsoft.OperationalInsights/workspaces/read`和`Microsoft.OperationalInsights/workspaces/sharedKeys/action`。 使用這些許可權，使用者無法執行任何工作區級別的查詢。 它們只能枚舉工作區，並將其用作診斷設置或代理配置的目標。

    * 向使用者授予其資源的以下許可權：`Microsoft.Insights/logs/*/read`和`Microsoft.Insights/diagnosticSettings/write`。 如果已為其分配[了日誌分析參與者](../../role-based-access-control/built-in-roles.md#contributor)角色、分配了 Reader 角色或授予`*/read`此資源的許可權，則就足夠了。

3. 要授予使用者訪問其資源中記錄資料而而無需讀取安全事件和發送資料，請執行以下操作：

    * 將工作區存取控制模式配置為**使用工作區或資源許可權**

    * 向使用者授予其資源的以下許可權： `Microsoft.Insights/logs/*/read`。

    * 添加以下非操作以阻止使用者讀取安全事件種類： `Microsoft.Insights/logs/SecurityEvent/read`。 非操作應與提供讀取權限 （`Microsoft.Insights/logs/*/read`的操作 ） 的操作處於相同的自訂角色。 如果使用者從分配給此資源或訂閱或資源組的另一個角色中固有的讀取操作，則他們將能夠讀取所有日誌類型。 如果他們繼承`*/read`存在，例如，使用讀取器或參與者角色，則也是如此。

4. 要授予使用者從其資源中記錄資料的許可權並讀取所有 Azure AD 登錄並讀取工作區中的更新管理解決方案日誌資料，請執行以下操作：

    * 將工作區存取控制模式配置為**使用工作區或資源許可權**

    * 在工作區上授予使用者以下許可權： 

        * `Microsoft.OperationalInsights/workspaces/read`• 必需，以便使用可以枚舉工作區並在 Azure 門戶中打開工作區邊欄選項卡
        * `Microsoft.OperationalInsights/workspaces/query/read`• 每個可以執行查詢的使用者都需要
        * `Microsoft.OperationalInsights/workspaces/query/SigninLogs/read`• 能夠讀取 Azure AD 登錄日誌
        * `Microsoft.OperationalInsights/workspaces/query/Update/read`• 能夠讀取更新管理解決方案日誌
        * `Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read`• 能夠讀取更新管理解決方案日誌
        * `Microsoft.OperationalInsights/workspaces/query/UpdateSummary/read`• 能夠讀取更新管理日誌
        * `Microsoft.OperationalInsights/workspaces/query/Heartbeat/read`• 需要能夠使用更新管理解決方案
        * `Microsoft.OperationalInsights/workspaces/query/ComputerGroup/read`• 需要能夠使用更新管理解決方案

    * 向使用者授予其資源的以下許可權： `*/read`、 分配給 Reader 角色`Microsoft.Insights/logs/*/read`或 。 

## <a name="table-level-rbac"></a>表級 RBAC

**表級 RBAC**允許您定義對日誌分析工作區中的資料的更精細控制以及其他許可權。 此控制項允許您定義只有一組特定使用者才能訪問的特定資料類型。

使用[Azure 自訂角色](../../role-based-access-control/custom-roles.md)實現表存取控制，以授予對工作區中特定[表](../log-query/logs-structure.md)的存取權限。 這些角色應用於工作區上下文或資源上下文[存取控制模式](design-logs-deployment.md#access-control-mode)的工作區，而不考慮使用者[的訪問模式](design-logs-deployment.md#access-mode)。

創建具有以下操作的[自訂角色](../../role-based-access-control/custom-roles.md)，以定義對表訪問控制項的訪問。

* 要授予對表的存取權限，請將其包括在角色定義的 **"操作"** 部分中。 要從允許**的操作**中減去存取權限，請將其包括在 **"不操作"** 部分中。
* 使用 Microsoft.操作見解/工作區/查詢/* 指定所有表。

例如，要創建具有訪問_活動訊號_和_AzureActivity_表的角色，請使用以下操作創建自訂角色：

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/Heartbeat/read",
    "Microsoft.OperationalInsights/workspaces/query/AzureActivity/read"
  ],
```

要創建僅訪問_安全基線_表的角色，請使用以下操作創建自訂角色：

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read"
],
```

### <a name="custom-logs"></a>自訂的記錄

 自訂日誌是從資料來源（如自訂日誌和 HTTP 資料收集器 API）創建。 識別日誌類型的最簡單方法是檢查[日誌架構中的自訂日誌](../log-query/get-started-portal.md#understand-the-schema)下列出的表。

 您當前無法授予對單個自訂日誌的存取權限，但可以授予對所有自訂日誌的存取權限。 要創建具有對所有自訂日誌存取權限的角色，請使用以下操作創建自訂角色：

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read"
],
```

### <a name="considerations"></a>考量

* 如果使用者被授予具有包含_\*/read_操作的標準讀取器或參與者角色的全域讀取權限，它將覆蓋每個表的存取控制，並授予他們對所有日誌資料的許可權。
* 如果使用者被授予每個表存取權限，但沒有其他許可權，他們將能夠從 API 訪問日誌資料，但不能從 Azure 門戶訪問日誌資料。 要提供來自 Azure 門戶的訪問，請使用日誌分析讀取器作為其基本角色。
* 訂閱的管理員將有權訪問所有資料類型，而不考慮任何其他許可權設置。
* 對於每表訪問控制項，工作區擁有者被視為任何其他使用者。
* 我們建議將角色指派給安全性群組而不是單個使用者，以減少分配數。 這還將説明您使用現有的組管理工具來配置和驗證訪問。

## <a name="next-steps"></a>後續步驟

* 請參閱 [Log Analytics 代理程式概觀](../../azure-monitor/platform/log-analytics-agent.md)，從您資料中心或其他雲端環境中的電腦收集資料。

* 請參閱[收集有關 Azure 虛擬機器的資料](../../azure-monitor/learn/quick-collect-azurevm.md)以配置從 Azure VM 進行資料收集。
