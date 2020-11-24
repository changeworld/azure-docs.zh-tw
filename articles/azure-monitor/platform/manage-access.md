---
title: 在 Azure 監視器中管理 Log Analytics 工作區 | Microsoft Docs
description: 您可以使用資源、工作區或資料表層級許可權，管理 Azure 監視器中 Log Analytics 工作區中儲存之資料的存取權。 本文將詳細說明如何完成。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/10/2019
ms.openlocfilehash: 79e5b1ddde0ff5f0d09dc1c20e3b20ec4de3d925
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95536671"
---
# <a name="manage-access-to-log-data-and-workspaces-in-azure-monitor"></a>管理 Azure 監視器中記錄資料和工作區的存取

Azure 監視器會將 [記錄](data-platform-logs.md) 資料儲存在 log Analytics 工作區中。 工作區是包含資料和組態資訊的容器。 若要記錄管理資料的存取權，您可以執行與工作區相關的各種系統管理工作。

本文說明如何記錄管理的存取權，以及管理包含這些記錄的工作區，包括如何授與存取權給： 

* 使用工作區許可權的工作區。
* 需要使用 Azure 角色型存取控制從特定資源存取記錄資料的使用者 (Azure RBAC) -也稱為 [資源內容](design-logs-deployment.md#access-mode)
* 需要使用 Azure RBAC 來存取工作區中特定資料表之記錄資料的使用者。

若要瞭解有關 Azure RBAC 和存取策略的記錄概念，請閱讀 [設計您的 Azure 監視器記錄部署](design-logs-deployment.md)

## <a name="configure-access-control-mode"></a>設定存取控制模式

您可以從 Azure 入口網站或使用 Azure PowerShell 來查看工作區上設定的 [存取控制模式](design-logs-deployment.md) 。  您可以使用下列其中一種支援的方法來變更此設定：

* Azure 入口網站

* Azure PowerShell

* Azure Resource Manager 範本

### <a name="from-the-azure-portal"></a>從 Azure 入口網站

您可以在 [ **Log Analytics 工作區**] 功能表中，于工作區的 [**總覽**] 頁面上，查看目前的工作區存取控制模式。

![查看工作區存取控制模式](media/manage-access/view-access-control-mode.png)

1. 登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。
1. 在 Azure 入口網站中，選取 > 您工作區的 Log Analytics 工作區。

您可以從工作區的 [ **屬性** ] 頁面變更此設定。 如果您沒有設定工作區的許可權，將會停用變更設定。

![變更工作區存取模式](media/manage-access/change-access-control-mode.png)

### <a name="using-powershell"></a>使用 PowerShell

使用下列命令來檢查訂用帳戶中所有工作區的存取控制模式：

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {$_.Name + ": " + $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions}
```

輸出應該如以下所示：

```
DefaultWorkspace38917: True
DefaultWorkspace21532: False
```

的值 `False` 表示工作區是使用工作區內容存取模式進行設定。  的值 `True` 表示工作區是使用資源內容存取模式進行設定。

> [!NOTE]
> 如果在沒有布林值的情況下傳回工作區，而且是空白的，這也會與值的結果相符 `False` 。
>

使用下列腳本，將特定工作區的存取控制模式設定為資源內容許可權：

```powershell
$WSName = "my-workspace"
$Workspace = Get-AzResource -Name $WSName -ExpandProperties
if ($Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null)
    { $Workspace.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else
    { $Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $Workspace.ResourceId -Properties $Workspace.Properties -Force
```

使用下列腳本，將訂用帳戶中所有工作區的存取控制模式設定為資源內容許可權：

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {
if ($_.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null)
    { $_.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else
    { $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $_.ResourceId -Properties $_.Properties -Force
}
```

### <a name="using-a-resource-manager-template"></a>使用 Resource Manager 範本

若要在 Azure Resource Manager 範本中設定存取模式，請將工作區上的 **enableLogAccessUsingOnlyResourcePermissions** 功能旗標設定為下列其中一個值。

* **false**：將工作區設定為工作區內容許可權。 如果未設定旗標，這是預設設定。
* **true**：將工作區設定為資源內容許可權。

## <a name="manage-access-using-workspace-permissions"></a>使用工作區版權管理存取權

每個工作區可以有多個相關聯的帳戶，而每個帳戶可以存取多個工作區。 使用 [azure 角色型存取控制 (AZURE RBAC) ](../../role-based-access-control/role-assignments-portal.md)來管理存取權。

下列活動也需要 Azure 權限︰

|動作 |所需的 Azure 權限 |備註 |
|-------|-------------------------|------|
| 新增和移除監視解決方案 | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | 必須在資源群組或訂用帳戶層級授與這些權限。 |
| 變更定價層 | `Microsoft.OperationalInsights/workspaces/*/write` | |
| 檢視 [備份] 和 [Site Recovery] 解決方案圖格中的資料 | 系統管理員/共同管理員 | 存取使用傳統部署模型所部署的資源 |
| 在 Azure 入口網站中建立工作區 | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||
| 在入口網站中查看工作區基本屬性並輸入工作區分頁 | `Microsoft.OperationalInsights/workspaces/read` ||
| 使用任何介面查詢記錄 | `Microsoft.OperationalInsights/workspaces/query/read` ||
| 使用查詢來存取所有記錄類型 | `Microsoft.OperationalInsights/workspaces/query/*/read` ||
| 存取特定的記錄資料表 | `Microsoft.OperationalInsights/workspaces/query/<table_name>/read` ||
| 讀取工作區金鑰以允許將記錄傳送至此工作區 | `Microsoft.OperationalInsights/workspaces/sharedKeys/action` ||

## <a name="manage-access-using-azure-permissions"></a>使用 Azure 版權管理存取權

若要使用 Azure 權限授與 Log Analytics 工作區的存取權，請遵循[使用角色指派來管理 Azure 訂用帳戶資源的存取權](../../role-based-access-control/role-assignments-portal.md)中的步驟。 如需自訂角色的範例，請參閱 [範例自訂角色](#custom-role-examples)

Azure 有兩個內建的 Log Analytics 工作區角色：

* Log Analytics 讀者
* Log Analytics 參與者

*Log Analytics 讀者* 角色的成員可以：

* 檢視及搜尋所有監視資料
* 檢視監視設定，包括檢視所有 Azure 資源上檢視的 Azure 診斷組態。

Log Analytics 讀者角色包含下列 Azure 動作：

| 類型    | 權限 | 描述 |
| ------- | ---------- | ----------- |
| 動作 | `*/read`   | 檢視所有 Azure 資源和資源組態的能力。 包括檢視： <br> 虛擬機器擴充功能 <br> 在資源上設定 Azure 診斷 <br> 所有資源的所有屬性和設定。 <br> 針對工作區，它允許完全不受限制的許可權，以讀取工作區設定並對資料執行查詢。 查看更細微的選項。 |
| 動作 | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | 已淘汰，不需要將它們指派給使用者。 |
| 動作 | `Microsoft.OperationalInsights/workspaces/search/action` | 已淘汰，不需要將它們指派給使用者。 |
| 動作 | `Microsoft.Support/*` | 開啟支援案例的能力 |
|不是動作 | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | 防止讀取在使用資料收集 API 和安裝代理程式時所需的工作區金鑰。 這可防止使用者將新資源新增至工作區 |

*Log Analytics 參與者* 角色的成員可以：

* 包含 *Log Analytics 讀者角色* 的擁有權限，可讓使用者讀取所有監視資料
* 建立和設定自動化帳戶
* 新增及移除管理解決方案

    > [!NOTE]
    > 若要成功執行後面兩個動作，就必須在資源群組或訂用帳戶層級授與此權限。

* 讀取儲存體帳戶金鑰
* 從 Azure 儲存體設定記錄收集
* 編輯 Azure 資源的監視設定，包括
  * 將 VM 擴充功能新增至 VM
  * 在所有 Azure 資源上設定 Azure 診斷

> [!NOTE]
> 您可以使用將虛擬機器擴充功能新增至虛擬機器的功能，取得虛擬機器的完整控制權。

Log Analytics 參與者角色包含下列 Azure 動作：

| 權限 | 說明 |
| ---------- | ----------- |
| `*/read`     | 檢視所有資源和資源組態的能力。 包括檢視： <br> 虛擬機器擴充功能 <br> 在資源上設定 Azure 診斷 <br> 所有資源的所有屬性和設定。 <br> 針對工作區，它允許完全不受限制的許可權，以讀取工作區設定並對資料執行查詢。 查看更細微的選項。 |
| `Microsoft.Automation/automationAccounts/*` | 建立及設定 Azure 自動化帳戶的能力，包括新增和編輯 Runbook |
| `Microsoft.ClassicCompute/virtualMachines/extensions/*` <br> `Microsoft.Compute/virtualMachines/extensions/*` | 新增、更新及移除虛擬機器擴充功能，包括 Microsoft Monitoring Agent 擴充功能和 OMS Agent for Linux 擴充功能 |
| `Microsoft.ClassicStorage/storageAccounts/listKeys/action` <br> `Microsoft.Storage/storageAccounts/listKeys/action` | 檢視儲存體帳戶金鑰。 需具備此權限，才能設定 Log Analytics 以讀取 Azure Blob 儲存體帳戶中的記錄 |
| `Microsoft.Insights/alertRules/*` | 新增、更新和移除警示規則 |
| `Microsoft.Insights/diagnosticSettings/*` | 在 Azure 資源上新增、更新和移除診斷設定 |
| `Microsoft.OperationalInsights/*` | 新增、更新和移除 Log Analytics 工作區的設定。 若要編輯工作區 advanced 設定，使用者需求 `Microsoft.OperationalInsights/workspaces/write` 。 |
| `Microsoft.OperationsManagement/*` | 新增及移除管理解決方案 |
| `Microsoft.Resources/deployments/*` | 建立及刪除部署。 需具備此權限，才能新增及移除解決方案、工作區和自動化帳戶 |
| `Microsoft.Resources/subscriptions/resourcegroups/deployments/*` | 建立及刪除部署。 需具備此權限，才能新增及移除解決方案、工作區和自動化帳戶 |

若要新增及移除某個使用者角色的使用者，必須具備 `Microsoft.Authorization/*/Delete` 和 `Microsoft.Authorization/*/Write` 權限。

使用下列角色，賦予使用者不同範圍的存取權：

* 訂用帳戶 - 存取訂用帳戶中的所有工作區
* 資源群組 - 存取資源群組中的所有工作區
* 資源 - 僅存取指定的工作區

建議您在資源層級 (工作區) 執行指派，以確保正確的存取控制。 使用[自訂角色](../../role-based-access-control/custom-roles.md)建立具備所需特定權限的角色。

### <a name="resource-permissions"></a>資源許可權

當使用者使用資源內容存取從工作區查詢記錄時，這些資源會在資源上擁有下列許可權：

| 權限 | 說明 |
| ---------- | ----------- |
| `Microsoft.Insights/logs/<tableName>/read`<br><br>範例：<br>`Microsoft.Insights/logs/*/read`<br>`Microsoft.Insights/logs/Heartbeat/read` | 能夠查看資源的所有記錄資料。  |
| `Microsoft.Insights/diagnosticSettings/write` | 能夠設定診斷設定以允許設定此資源的記錄。 |

`/read`許可權通常是從包含 _\* /read 或_ 許可權的角色 _\*_ （包括內建的 [讀取](../../role-based-access-control/built-in-roles.md#reader)者和 [參與者](../../role-based-access-control/built-in-roles.md#contributor)角色）授與。 包含特定動作或專用內建角色的自訂角色可能不包含此許可權。

如果您想要為不同的資料表建立不同的存取控制，請參閱以下 [定義每個資料表的存取控制](#table-level-azure-rbac) 。

## <a name="custom-role-examples"></a>自訂角色範例

1. 若要授與使用者從其資源記錄資料的存取權，請執行下列動作：

    * 設定工作區存取控制模式以 **使用工作區或資源許可權**

    * 授 `*/read` 與使用者或 `Microsoft.Insights/logs/*/read` 許可權給其資源。 如果他們已獲指派工作區的 [Log Analytics 讀者](../../role-based-access-control/built-in-roles.md#reader) 角色，就已足夠。

2. 若要授與使用者從其資源記錄資料的存取權，並設定其資源以將記錄傳送至工作區，請執行下列動作：

    * 設定工作區存取控制模式以 **使用工作區或資源許可權**

    * 授與使用者工作區的下列許可權： `Microsoft.OperationalInsights/workspaces/read` 和 `Microsoft.OperationalInsights/workspaces/sharedKeys/action` 。 使用這些許可權，使用者將無法執行任何工作區層級的查詢。 它們只能列舉工作區，並使用它做為診斷設定或代理程式設定的目的地。

    * 授與使用者下列資源的許可權： `Microsoft.Insights/logs/*/read` 和 `Microsoft.Insights/diagnosticSettings/write` 。 如果他們已獲指派「 [Log Analytics 參與者](../../role-based-access-control/built-in-roles.md#contributor) 」角色、指派「讀者」角色或授與 `*/read` 此資源的許可權，就已足夠。

3. 若要授與使用者從其資源記錄資料的存取權，而不能讀取安全性事件和傳送資料，請執行下列動作：

    * 設定工作區存取控制模式以 **使用工作區或資源許可權**

    * 授與使用者下列資源的許可權： `Microsoft.Insights/logs/*/read` 。

    * 新增下列 NonAction，以封鎖使用者讀取 SecurityEvent 類型： `Microsoft.Insights/logs/SecurityEvent/read` 。 NonAction 必須與提供讀取權限 () 的自訂角色相同 `Microsoft.Insights/logs/*/read` 。 如果使用者從另一個指派給此資源或訂用帳戶或資源群組的角色繼承讀取動作，他們就能夠讀取所有記錄類型。 如果它們繼承 `*/read` （例如，具有讀取者或參與者角色），也是如此。

4. 若要授與使用者從其資源記錄資料的存取權，並讀取所有 Azure AD 登入，並從工作區讀取更新管理的解決方案記錄資料，請執行下列動作：

    * 設定工作區存取控制模式以 **使用工作區或資源許可權**

    * 授與使用者工作區的下列許可權： 

        * `Microsoft.OperationalInsights/workspaces/read` -必要，讓使用者可以列舉工作區，並開啟 Azure 入口網站中的工作區分頁
        * `Microsoft.OperationalInsights/workspaces/query/read` –每個可執行查詢的使用者都需要
        * `Microsoft.OperationalInsights/workspaces/query/SigninLogs/read` –能夠讀取 Azure AD 登入記錄
        * `Microsoft.OperationalInsights/workspaces/query/Update/read` –能夠讀取更新管理的解決方案記錄
        * `Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read` –能夠讀取更新管理的解決方案記錄
        * `Microsoft.OperationalInsights/workspaces/query/UpdateSummary/read` –能夠讀取更新管理記錄檔
        * `Microsoft.OperationalInsights/workspaces/query/Heartbeat/read` –必須能夠使用更新管理的解決方案
        * `Microsoft.OperationalInsights/workspaces/query/ComputerGroup/read` –必須能夠使用更新管理的解決方案

    * 授與使用者下列資源的許可權： `*/read` 、指派給讀者角色或 `Microsoft.Insights/logs/*/read` 。 

## <a name="table-level-azure-rbac"></a>資料表層級 Azure RBAC

**資料表層級 AZURE RBAC** 除了其他許可權之外，還可讓您對 Log Analytics 工作區中的資料定義更細微的控制。 此控制項可讓您定義只有一組特定的使用者才能存取的特定資料類型。

您可以使用 [Azure 自訂角色](../../role-based-access-control/custom-roles.md) 來執行資料表存取控制，以將存取權授與工作區中的特定 [資料表](./data-platform-logs.md) 。 無論使用者的[存取模式](design-logs-deployment.md#access-mode)為何，這些角色都會套用至具有工作區內容或資源內容[存取控制模式](design-logs-deployment.md#access-control-mode)的工作區。

建立具有下列動作的 [自訂角色](../../role-based-access-control/custom-roles.md) ，以定義資料表存取控制的存取權。

* 若要授與資料表的存取權，請將它包含在角色定義的 [ **動作** ] 區段中。 若要從允許的 **動作** 中減去存取權，請將它包含在 **NotActions** 區段中。
* 使用 Microsoft. OperationalInsights/workspace/query/* 來指定所有資料表。

例如，若要建立可存取「 _心跳_ 」和「 _AzureActivity_ 資料表」的角色，請使用下列動作來建立自訂角色：

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/Heartbeat/read",
    "Microsoft.OperationalInsights/workspaces/query/AzureActivity/read"
  ],
```

若要建立只能存取 _SecurityBaseline_ 資料表的角色，請使用下列動作來建立自訂角色：

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read"
],
```
上述範例會定義允許的資料表清單。 當使用者可以存取所有資料表，但 _SecurityAlert_ 資料表時，此範例會顯示封鎖的清單定義：

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/*/read"
],
"notActions":  [
    "Microsoft.OperationalInsights/workspaces/query/SecurityAlert/read"
],
```

### <a name="custom-logs"></a>自訂的記錄

 自訂記錄是從資料來源建立，例如自訂記錄和 HTTP 資料收集器 API。 識別記錄類型最簡單的方式，就是檢查 [記錄架構中 [自訂記錄](../log-query/get-started-portal.md#understand-the-schema)檔] 底下列出的資料表。

 您無法將存取權授與個別的自訂記錄，但您可以將存取權授與所有自訂記錄。 若要建立可存取所有自訂記錄的角色，請使用下列動作來建立自訂角色：

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read"
],
```
管理自訂記錄存取的另一種方法是將它們指派給 Azure 資源，並使用資源內容範例來管理存取權。 若要使用此方法，您必須在資料透過[HTTP 資料收集器 API](data-collector-api.md)內嵌至 Log Analytics 時，于[AzureResourceId](data-collector-api.md#request-headers)標頭中指定資源識別碼來包含資源識別碼。 資源識別碼必須有效，且已套用存取規則。 內嵌記錄之後，具有資源讀取存取權的使用者可以存取這些記錄檔，如下所述。

有時自訂記錄來自未直接與特定資源相關聯的來源。 在此情況下，請建立資源群組，以管理這些記錄的存取權。 資源群組不會產生任何費用，但會提供您有效的資源識別碼來控制對自訂記錄的存取。 例如，如果特定防火牆正在傳送自訂記錄檔，請建立名為 "MyFireWallLogs" 的資源群組，並確定 API 要求包含 "MyFireWallLogs" 的資源識別碼。 然後，防火牆記錄檔記錄只可供被授與 MyFireWallLogs 存取權的使用者或具有完整工作區存取權的使用者存取。          

### <a name="considerations"></a>考量

* 如果使用者被授與包含 _\* /read_ 動作之標準讀取者或參與者角色的全域讀取權限，它會覆寫每個資料表的存取控制，並授與他們所有記錄資料的存取權。
* 如果使用者被授與每個資料表的存取權，但沒有其他許可權，他們就能夠從 API 存取記錄資料，而不是從 Azure 入口網站。 若要提供 Azure 入口網站的存取權，請使用 Log Analytics 讀取器作為其基礎角色。
* 不論任何其他許可權設定為何，訂用帳戶的系統管理員和擁有者都可以存取所有資料類型。
* 工作區擁有者會被視為每個資料表存取控制的任何其他使用者。
* 建議您將角色指派給安全性群組，而不是個別使用者，以減少指派數目。 這也可協助您使用現有的群組管理工具來設定及驗證存取權。

## <a name="next-steps"></a>後續步驟

* 請參閱 [Log Analytics 代理程式概觀](./log-analytics-agent.md)，從您資料中心或其他雲端環境中的電腦收集資料。

* 請參閱 [收集 azure 虛擬機器的相關資料](../learn/quick-collect-azurevm.md) ，以設定來自 azure vm 的資料收集。