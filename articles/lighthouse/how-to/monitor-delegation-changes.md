---
title: 監視管理租戶中的委派更改
description: 瞭解如何監視從客戶租戶到管理租戶的委派活動。
ms.date: 03/16/2020
ms.topic: conceptual
ms.openlocfilehash: 99aa05cb73326e441c0473855c27dc71212cf415
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79478223"
---
# <a name="monitor-delegation-changes-in-your-managing-tenant"></a>監視管理租戶中的委派更改

作為服務提供者，您可能希望瞭解何時通過[Azure 委派的資源管理](../concepts/azure-delegated-resource-management.md)將客戶訂閱或資源組委派給租戶，或者刪除以前委派的資源。

在管理租戶中[，Azure 活動日誌](../../azure-monitor/platform/platform-logs-overview.md)跟蹤租戶級別的委派活動。 此記錄的活動包括所有客戶租戶添加或刪除的任何授權。

本主題介紹監視向租戶（跨所有客戶）委派活動所需的許可權，以及執行此操作的最佳做法。 它還包括一個示例腳本，該腳本顯示了一種查詢和報告此資料的方法。

> [!IMPORTANT]
> 所有這些步驟都必須在管理租戶中執行，而不是在任何客戶租戶中執行。

## <a name="enable-access-to-tenant-level-data"></a>啟用對租戶級資料的訪問

要訪問租戶級活動日誌資料，必須在根作用域 （/） 中為帳戶分配[監視讀取器](../../role-based-access-control/built-in-roles.md#monitoring-reader)的內置角色。 此分配必須由具有具有其他提升存取權限的全域管理員角色的使用者執行。

### <a name="elevate-access-for-a-global-administrator-account"></a>提升全域管理員帳戶的存取權限

要在根作用域 （/） 中分配角色，您需要具有具有提升存取權限的全域管理員角色。 僅當需要分配角色時，才應添加此提升的訪問，然後在完成角色指派時刪除。

有關添加和刪除提升的詳細說明，請參閱[提升存取權限以管理所有 Azure 訂閱和管理組](../../role-based-access-control/elevate-access-global-admin.md)。

提升存取權限後，帳戶將在 Azure 中具有"使用者訪問管理員"角色。該角色位於根作用域中。 此角色指派允許您查看目錄中任何訂閱或管理組中的所有資源並分配存取權限，以及在根作用域中分配角色。 

### <a name="create-a-new-service-principal-account-to-access-tenant-level-data"></a>創建新的服務主體帳戶以訪問租戶級資料

提升存取權限後，可以將適當的許可權分配給帳戶，以便它可以查詢租戶級別的活動日誌資料。 此帳戶需要將[監視讀取器](../../role-based-access-control/built-in-roles.md#monitoring-reader)內置角色指派給管理租戶的根作用域。

> [!IMPORTANT]
> 在根作用域中授予角色指派意味著相同的許可權將應用於租戶中的每個資源。

由於這是廣泛的存取層級，我們建議您將此角色指派給服務主體帳戶，而不是將角色指派給單個使用者或組。 此外，我們建議以下最佳實踐：

- [創建新的服務主體帳戶](../../active-directory/develop/howto-create-service-principal-portal.md)僅用於此函數，而不是將此角色指派給用於其他自動化的現有服務主體。
- 確保此服務主體無法訪問任何委派的客戶資源。
- [使用證書對其進行身份驗證](../../active-directory/develop/howto-create-service-principal-portal.md#certificates-and-secrets)[並將其安全地存儲在 Azure 金鑰保存庫中](../../key-vault/key-vault-best-practices.md)。
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

創建服務主體帳戶並在根作用域中分配監視讀取器角色後，請確保刪除全域管理員帳戶的[提升存取權限](../../role-based-access-control/elevate-access-global-admin.md#remove-elevated-access)，因為不再需要此級別的訪問。

## <a name="query-the-activity-log"></a>查詢活動日誌

創建具有監視讀取器存取權限的新服務主體帳戶後，可以使用該帳戶查詢和報告租戶中的委派活動。 

下面的示例使用 Azure PowerShell 查詢過去 1 天的活動，並報告任何添加或刪除的代表團（或未成功的嘗試）。 它查詢[租戶活動日誌](https://docs.microsoft.com/rest/api/monitor/TenantActivityLogs/List)資料，然後構造以下值以報告添加或刪除的代表團：

- **委派資源 Id：** 委派的訂閱或資源組的 ID
- **客戶租戶 ID**：客戶租戶 ID
- **客戶訂閱 ID**：已委派或包含委派的資源組的訂閱 ID
- **客戶委派狀態**：委派資源的狀態更改（成功或失敗）
- **事件時間戳記**：記錄委派更改的日期和時間

查詢此資料時，請記住：

- 如果在單個部署中委派了多個資源組，則將為每個資源組返回單獨的條目。
- 對以前的委派所做的更改（如更新許可權結構）將作為附加委派記錄。
- 如上所述，帳戶必須在根作用域 （/） 具有監視讀取器內置角色才能訪問此租戶級別資料。
- 您可以在自己的工作流和報告中使用此資料。 例如，可以使用[HTTP 資料收集器 API（公共預覽）](../../azure-monitor/platform/data-collector-api.md)從 REST API 用戶端將資料記錄到 Azure 監視器，然後使用[操作組](../../azure-monitor/platform/action-groups.md)創建通知或警報。

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

- 瞭解如何將客戶添加到 Azure[委派的資源管理](../concepts/azure-delegated-resource-management.md)。
- 瞭解[Azure 監視器](../../azure-monitor/index.yml)和[Azure 活動日誌](../../azure-monitor/platform/platform-logs-overview.md)。
