---
title: 監視管理租戶中的委派變更
description: 瞭解如何監視從客戶租戶到管理租戶的委派活動。
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: 322580cc3d0246f7a34e28cdae94da57fda070b5
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985145"
---
# <a name="monitor-delegation-changes-in-your-managing-tenant"></a>監視管理租戶中的委派變更

作為服務提供者,您可能希望瞭解何時通過[Azure 委派的資源管理](../concepts/azure-delegated-resource-management.md)將客戶訂閱或資源組委派給租戶,或者刪除以前委派的資源。

在管理租戶中[,Azure 活動日誌](../../azure-monitor/platform/platform-logs-overview.md)跟蹤租戶級別的委派活動。 此記錄的活動包括所有客戶租戶添加或刪除的任何授權。

本主題介紹監視向租戶(跨所有客戶)委派活動所需的許可權,以及執行此操作的最佳做法。 它還包括一個示例腳本,該腳本顯示了一種查詢和報告此數據的方法。

> [!IMPORTANT]
> 所有這些步驟都必須在管理租戶中執行,而不是在任何客戶租戶中執行。

## <a name="enable-access-to-tenant-level-data"></a>開啟對租戶的資料存取

要訪問租戶級活動日誌數據,必須在根作用域 (/) 中為帳戶分配[監視讀取器](../../role-based-access-control/built-in-roles.md#monitoring-reader)的內置角色。 此分配必須由具有具有其他提升訪問許可權的全域管理員角色的用戶執行。

### <a name="elevate-access-for-a-global-administrator-account"></a>提升全域管理員帳戶的訪問權限

要在根作用域 (/) 中分配角色,您需要具有具有提升訪問許可權的全域管理員角色。 僅當需要分配角色時,才應添加此提升的訪問,然後在完成角色分配時刪除。

有關新增和刪除提升的詳細說明,請參閱[提升存取權限以管理所有 Azure 訂閱和管理群組](../../role-based-access-control/elevate-access-global-admin.md)。

提升訪問許可權后,帳戶將在 Azure 中具有「使用者存取管理員」角色。該角色位於根作用域中。 此角色分配允許您查看目錄中任何訂閱或管理組中的所有資源並分配訪問許可權,以及在根作用域中分配角色。 

### <a name="create-a-new-service-principal-account-to-access-tenant-level-data"></a>建立新的服務主體帳戶以存取租戶級資料

提升訪問許可權后,可以將適當的許可權分配給帳戶,以便它可以查詢租戶級別的活動日誌數據。 此帳戶需要將[監視讀取器](../../role-based-access-control/built-in-roles.md#monitoring-reader)內建角色分配給管理租戶的根作用域。

> [!IMPORTANT]
> 在根作用域中授予角色分配意味著相同的許可權將應用於租戶中的每個資源。

由於這是廣泛的訪問級別,我們建議您將此角色分配給服務主體帳戶,而不是將角色分配給單個使用者或組。 此外,我們建議以下最佳實踐:

- [創建新的服務主體帳戶](../../active-directory/develop/howto-create-service-principal-portal.md)僅用於此函數,而不是將此角色分配給用於其他自動化的現有服務主體。
- 確保此服務主體無法訪問任何委派的客戶資源。
- [使用憑證對其進行身份驗證](../../active-directory/develop/howto-create-service-principal-portal.md#certificates-and-secrets)[並將其安全地儲存在 Azure 金鑰保管庫中](../../key-vault/key-vault-best-practices.md)。
- 限制有權訪問代表服務主體行事的使用者。

使用以下方法之一進行根作用域分配。

#### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

New-AzRoleAssignment -SignInName <yourLoginName> -Scope "/" -RoleDefinitionName "Monitoring Reader"  -ApplicationId $servicePrincipal.ApplicationId 
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az role assignment create --assignee 00000000-0000-0000-0000-000000000000 --role "Monitoring Reader" --scope "/"
```

### <a name="remove-elevated-access-for-the-global-administrator-account"></a>刪除全域管理員帳戶的提升存取權限

創建服務主體帳戶並在根作用域中分配監視讀取器角色后,請確保刪除全域管理員帳戶的[提升訪問許可權](../../role-based-access-control/elevate-access-global-admin.md#remove-elevated-access),因為不再需要此級別的訪問。

## <a name="query-the-activity-log"></a>查詢活動記錄

創建具有監視讀取器訪問許可權的新服務主體帳戶後,可以使用該帳戶查詢和報告租戶中的委派活動。 

[此 Azure PowerShell 文稿](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/monitor-delegation-changes)可用於查詢過去 1 天的活動,並報告任何添加或刪除的代表團(或未成功的嘗試)。 它查詢[租戶活動日誌](https://docs.microsoft.com/rest/api/monitor/TenantActivityLogs/List)數據,然後構造以下值以報告添加或刪除的代表團:

- **委派資源 Id:** 委派的訂閱或資源群組的 ID
- **客戶租戶 ID**:客戶租戶 ID
- **客戶訂閱 ID**:已委派或包含委派的資源組的訂閱 ID
- **客戶委派狀態**:委派資源的狀態更改(成功或失敗)
- **事件時間戳**:記錄委派變更的日期和時間

查詢此資料時,請記住:

- 如果在單個部署中委派了多個資源組,則將為每個資源組返回單獨的條目。
- 對以前的委派所做的更改(如更新許可權結構)將作為附加委派記錄。
- 如上所述,帳戶必須在根作用域 (/) 具有監視讀取器內置角色才能訪問此租戶級別數據。
- 您可以在自己的工作流和報告中使用此數據。 例如,可以使用[HTTP 資料收集器 API(公共預覽)](../../azure-monitor/platform/data-collector-api.md)從 REST API 用戶端將數據記錄到 Azure 監視器,然後使用[操作組](../../azure-monitor/platform/action-groups.md)創建通知或警報。

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Azure Lighthouse: Query Tenant Activity Log for registered/unregistered delegations for the past 1 day

$GetDate = (Get-Date).AddDays((-1))

$dateFormatForQuery = $GetDate.ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ssZ")

# Getting Azure context for the API call
$currentContext = Get-AzContext

# Fetching new token
$azureRmProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = [Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient]::new($azureRmProfile)
$token = $profileClient.AcquireAccessToken($currentContext.Tenant.Id)

$listOperations = @{
    Uri = "https://management.azure.com/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&`$filter=eventTimestamp ge '$($dateFormatForQuery)'"
    Headers = @{
        Authorization = "Bearer $($token.AccessToken)"
        'Content-Type' = 'application/json'
    }
    Method = 'GET'
}
$list = Invoke-RestMethod @listOperations
$showOperations = $list.value

if ($showOperations.operationName.value -eq "Microsoft.Resources/tenants/register/action")
{
    $registerOutputs  = $showOperations | Where-Object -FilterScript {$_.eventName.value -eq "EndRequest" -and $_.resourceType.value -and $_.operationName.value -eq "Microsoft.Resources/tenants/register/action"}
    foreach ($registerOutput in $registerOutputs)
    {
    $registerOutputdata = [pscustomobject]@{
        Event = "An Azure customer has delegated resources to your tenant";
        DelegatedResourceId = $registerOutput.description |%{$_.split('"')[11]};
        CustomerTenantId = $registerOutput.description |%{$_.split('"')[7]};
        CustomerSubscriptionId = $registerOutput.subscriptionId;
        CustomerDelegationStatus = $registerOutput.status.value;
        EventTimeStamp = $registerOutput.eventTimestamp;
        }
        $registerOutputdata | Format-List
    }
}
if ($showOperations.operationName.value -eq "Microsoft.Resources/tenants/unregister/action") 
{
    $unregisterOutputs  = $showOperations | Where-Object -FilterScript {$_.eventName.value -eq "EndRequest" -and $_.resourceType.value -and $_.operationName.value -eq "Microsoft.Resources/tenants/unregister/action"}
    foreach ($unregisterOutput in $unregisterOutputs)
    {
    $unregisterOutputdata = [pscustomobject]@{
        Event = "An Azure customer has removed delegated resources from your tenant";
        DelegatedResourceId = $unregisterOutput.description |%{$_.split('"')[11]};
        CustomerTenantId = $unregisterOutput.description |%{$_.split('"')[7]};
        CustomerSubscriptionId = $unregisterOutput.subscriptionId;
        CustomerDelegationStatus = $unregisterOutput.status.value;
        EventTimeStamp = $unregisterOutput.eventTimestamp;
        }
        $unregisterOutputdata | Format-List
    }
}
else 
{
    Write-Output "No new delegation changes."
}   


```

## <a name="next-steps"></a>後續步驟

- 瞭解如何將客戶加入 Azure[委派的資源管理](../concepts/azure-delegated-resource-management.md)。
- 瞭解[Azure 監視器](../../azure-monitor/index.yml)與[Azure 活動紀錄](../../azure-monitor/platform/platform-logs-overview.md)。
