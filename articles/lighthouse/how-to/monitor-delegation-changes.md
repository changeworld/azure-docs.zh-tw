---
title: 監視您管理租使用者中的委派變更
description: 瞭解如何監視從客戶租使用者到您管理租使用者的委派活動。
ms.date: 08/18/2020
ms.topic: how-to
ms.openlocfilehash: 4d9d8b18634f94c355ea7fc0b5c125d631ec419c
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589736"
---
# <a name="monitor-delegation-changes-in-your-managing-tenant"></a>監視您管理租使用者中的委派變更

作為服務提供者，您可能會想要知道客戶的訂用帳戶或資源群組是透過 [Azure Lighthouse](../overview.md)委派給您的租使用者，或移除先前委派的資源。

在管理租使用者中， [Azure 活動記錄](../../azure-monitor/platform/platform-logs-overview.md) 會追蹤租使用者層級的委派活動。 此記錄的活動包含所有客戶租使用者中新增或移除的委派。

本主題說明在您所有客戶的租使用者 (監視委派活動所需的許可權) 以及執行此動作的最佳作法。 它也包含一個範例腳本，它會顯示查詢和報告此資料的一種方法。

> [!IMPORTANT]
> 所有這些步驟都必須在您的管理租使用者中執行，而不是在任何客戶租使用者中執行。

## <a name="enable-access-to-tenant-level-data"></a>啟用租使用者層級資料的存取

若要存取租使用者層級的活動記錄資料，必須在根範圍 (/) 將「 [監視讀者](../../role-based-access-control/built-in-roles.md#monitoring-reader) 」 Azure 內建角色指派給帳戶。 這項指派必須由具有「全域管理員」角色的使用者執行，並具有更高許可權的存取權。

### <a name="elevate-access-for-a-global-administrator-account"></a>提升全域管理員帳戶的存取權

若要在根範圍指派角色 (/) ，您必須擁有具有更高存取權的全域管理員角色。 只有當您需要進行角色指派，然後在完成時移除時，才應該加入此提高許可權的存取權。

如需新增和移除提高許可權的詳細指示，請參閱提高 [存取權以管理所有 Azure 訂用帳戶和管理群組](../../role-based-access-control/elevate-access-global-admin.md)。

提高存取權之後，您的帳戶將會在 Azure 中的根範圍具有使用者存取系統管理員角色。 此角色指派可讓您查看所有資源，並在目錄中的任何訂用帳戶或管理群組中指派存取權，以及在根範圍中建立角色指派。

### <a name="create-a-new-service-principal-account-to-access-tenant-level-data"></a>建立新的服務主體帳戶以存取租使用者層級的資料

提升存取權之後，您可以將適當的許可權指派給帳戶，讓它可以查詢租使用者層級的活動記錄資料。 此帳戶必須在您管理的租使用者的根範圍中指派「 [監視讀者](../../role-based-access-control/built-in-roles.md#monitoring-reader) 」 Azure 內建角色。

> [!IMPORTANT]
> 在根範圍授與角色指派，表示相同的許可權會套用至租使用者中的每個資源。

由於這是廣泛的存取層級，因此建議您將此角色指派給服務主體帳戶，而不是指派給個別使用者或群組。

 此外，我們建議您採用下列最佳作法：

- [建立新的服務主體帳戶](../../active-directory/develop/howto-create-service-principal-portal.md) ，只用于此函式，而不是將此角色指派給用於其他自動化的現有服務主體。
- 請確定此服務主體沒有任何委派客戶資源的存取權。
- [使用憑證來進行驗證](../../active-directory/develop/howto-create-service-principal-portal.md#upload-a-certificate-or-create-a-secret-for-signing-in) ，並 [將其安全地儲存在 Azure Key Vault 中](../../key-vault/general/best-practices.md)。
- 限制有權代表服務主體具有存取權的使用者。

您可以使用下列其中一種方法來進行根範圍指派。

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

### <a name="remove-elevated-access-for-the-global-administrator-account"></a>移除全域系統管理員帳戶的提升存取權

在您建立服務主體帳戶，並在根範圍指派監視讀取者角色之後，請務必移除全域系統管理員帳戶 [的提高存取權](../../role-based-access-control/elevate-access-global-admin.md#remove-elevated-access) ，因為將不再需要此層級的存取權。

## <a name="query-the-activity-log"></a>查詢活動記錄

當您建立新的服務主體帳戶，並具有管理租使用者根範圍的監視讀取器存取權之後，您就可以使用它來查詢和報告您租使用者中的委派活動。

[此 Azure PowerShell 腳本](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/monitor-delegation-changes) 可用來查詢已新增或移除的委派 (的過去1天的活動和報表，或未成功) 的嘗試。 它會查詢 [租使用者活動記錄](/rest/api/monitor/TenantActivityLogs/List) 資料，然後建立下列值來報告新增或移除的委派：

- **DelegatedResourceId**：委派的訂用帳戶或資源群組的識別碼
- **CustomerTenantId**：客戶租使用者識別碼
- **CustomerSubscriptionId**：已委派的訂用帳戶識別碼，或包含所委派資源群組的訂用帳戶識別碼
- **CustomerDelegationStatus**：委派資源 (成功或失敗的狀態變更) 
- **EventTimeStamp**：記錄委派變更的日期和時間

查詢此資料時，請記住：

- 如果在單一部署中委派多個資源群組，將會針對每個資源群組傳回不同的專案。
- 對先前委派所做的變更 (例如更新許可權結構) 將會記錄為新增的委派。
- 如先前所述，帳戶在根範圍 (/) 必須有「監視讀者」 Azure 內建角色，才能存取此租使用者層級的資料。
- 您可以在自己的工作流程和報告中使用此資料。 例如，您可以使用 [HTTP 資料收集器 API (公開預覽) ](../../azure-monitor/platform/data-collector-api.md) 將資料記錄到 REST API 用戶端 Azure 監視器，然後使用 [動作群組](../../azure-monitor/platform/action-groups.md) 來建立通知或警示。

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Azure Lighthouse: Query Tenant Activity Log for registered/unregistered delegations for the last 1 day

$GetDate = (Get-Date).AddDays((-1))

$dateFormatForQuery = $GetDate.ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ssZ")

# Getting Azure context for the API call
$currentContext = Get-AzContext

# Fetching new token
$azureRmProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = [Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient]::new($azureRmProfile)
$token = $profileClient.AcquireAccessToken($currentContext.Tenant.Id)

$listOperations = @{
    Uri     = "https://management.azure.com/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&`$filter=eventTimestamp ge '$($dateFormatForQuery)'"
    Headers = @{
        Authorization  = "Bearer $($token.AccessToken)"
        'Content-Type' = 'application/json'
    }
    Method  = 'GET'
}
$list = Invoke-RestMethod @listOperations

# First link can be empty - and point to a next link (or potentially multiple pages)
# While you get more data - continue fetching and add result
while($list.nextLink){
    $list2 = Invoke-RestMethod $list.nextLink -Headers $listOperations.Headers -Method Get
    $data+=$list2.value;
    $list.nextLink = $list2.nextlink;
}

$showOperations = $data;

if ($showOperations.operationName.value -eq "Microsoft.Resources/tenants/register/action") {
    $registerOutputs = $showOperations | Where-Object -FilterScript { $_.eventName.value -eq "EndRequest" -and $_.resourceType.value -and $_.operationName.value -eq "Microsoft.Resources/tenants/register/action" }
    foreach ($registerOutput in $registerOutputs) {
        $eventDescription = $registerOutput.description | ConvertFrom-Json;
    $registerOutputdata = [pscustomobject]@{
        Event                    = "An Azure customer has registered delegated resources to your Azure tenant";
        DelegatedResourceId      = $eventDescription.delegationResourceId; 
        CustomerTenantId         = $eventDescription.subscriptionTenantId;
        CustomerSubscriptionId   = $eventDescription.subscriptionId;
        CustomerDelegationStatus = $registerOutput.status.value;
        EventTimeStamp           = $registerOutput.eventTimestamp;
        }
        $registerOutputdata | Format-List
    }
}
if ($showOperations.operationName.value -eq "Microsoft.Resources/tenants/unregister/action") {
    $unregisterOutputs = $showOperations | Where-Object -FilterScript { $_.eventName.value -eq "EndRequest" -and $_.resourceType.value -and $_.operationName.value -eq "Microsoft.Resources/tenants/unregister/action" }
    foreach ($unregisterOutput in $unregisterOutputs) {
        $eventDescription = $registerOutput.description | ConvertFrom-Json;
    $unregisterOutputdata = [pscustomobject]@{
        Event                    = "An Azure customer has unregistered delegated resources from your Azure tenant";
        DelegatedResourceId      = $eventDescription.delegationResourceId;
        CustomerTenantId         = $eventDescription.subscriptionTenantId;
        CustomerSubscriptionId   = $eventDescription.subscriptionId;
        CustomerDelegationStatus = $unregisterOutput.status.value;
        EventTimeStamp           = $unregisterOutput.eventTimestamp;
        }
        $unregisterOutputdata | Format-List
    }
}
else {
    Write-Output "No new delegation events for tenant: $($currentContext.Tenant.TenantId)"
}
```

> [!TIP]
> 雖然我們會在本主題中參考服務提供者和客戶，但 [管理多個](../concepts/enterprise.md) 租使用者的企業可以使用相同的程式。

## <a name="next-steps"></a>後續步驟

- 瞭解如何讓客戶在 [Azure Lighthouse](../concepts/azure-delegated-resource-management.md)上架。
- 瞭解 [Azure 監視器](../../azure-monitor/index.yml) 和 [Azure 活動記錄](../../azure-monitor/platform/platform-logs-overview.md)。
