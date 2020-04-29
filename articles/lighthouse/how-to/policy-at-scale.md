---
title: 大規模地將 Azure 原則部署至委派的訂用帳戶
description: 了解 Azure 委派資源管理如何讓您跨多個租用戶部署原則定義和原則指派。
ms.date: 11/8/2019
ms.topic: conceptual
ms.openlocfilehash: 3fe7e48c56e9a5af93e9642ee16c50cfbce34f9e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81481829"
---
# <a name="deploy-azure-policy-to-delegated-subscriptions-at-scale"></a>大規模地將 Azure 原則部署至委派的訂用帳戶

身為服務提供者，您可能已將多個客戶租用戶上架以進行 Azure 委派資源管理。 [Azure Lighthouse](../overview.md) 可讓服務提供者一次在多個租用戶之間執行大規模作業，讓管理工作更有效率。

本主題說明如何使用 [Azure 原則](../../governance/policy/index.yml)，使用 PowerShell 命令在多個租用戶之間部署原則定義和原則指派。 在此範例中，原則定義可確保只允許 HTTPS 流量來保護儲存體帳戶。

## <a name="use-azure-resource-graph-to-query-across-customer-tenants"></a>使用 Azure Resource Graph 在客戶租用戶之間進行查詢

您可以使用 [Azure Resource Graph](../../governance/resource-graph/index.yml)，在您所管理客戶租用戶中的所有訂用帳戶之間進行查詢。 在此範例中，我們將識別這些訂用帳戶中目前不需要 HTTPS 流量的任何儲存體帳戶。  

```powershell
$MspTenant = "insert your managing tenantId here"

$subs = Get-AzSubscription

$ManagedSubscriptions = Search-AzGraph -Query "ResourceContainers | where type == 'microsoft.resources/subscriptions' | where tenantId != '$($mspTenant)' | project name, subscriptionId, tenantId" -subscription $subs.subscriptionId

Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | project name, location, subscriptionId, tenantId, properties.supportsHttpsTrafficOnly" -subscription $ManagedSubscriptions.subscriptionId | convertto-json
```

## <a name="deploy-a-policy-across-multiple-customer-tenants"></a>在多個客戶租用戶之間部署原則

下列範例顯示如何使用 [Azure Resource Manager 範本](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/policy-enforce-https-storage/enforceHttpsStorage.json)，在多個客戶租用戶中的委派訂用帳戶之間，部署原則定義和原則指派。 此原則定義會要求所有儲存體帳戶都使用 HTTPS 流量，以防止在沒有設定為不符合規範的情況下，建立不符合並標示現有儲存體帳戶的任何新儲存體帳戶。

```powershell
Write-Output "In total, there are $($ManagedSubscriptions.Count) delegated customer subscriptions to be managed"

foreach ($ManagedSub in $ManagedSubscriptions)
{
    Select-AzSubscription -SubscriptionId $ManagedSub.subscriptionId

    New-AzSubscriptionDeployment -Name mgmt `
                     -Location eastus `
                     -TemplateUri "https://raw.githubusercontent.com/Azure/Azure-Lighthouse-samples/master/templates/policy-enforce-https-storage/enforceHttpsStorage.json" `
                     -AsJob
}
```

## <a name="validate-the-policy-deployment"></a>驗證原則部署

部署 Azure Resource Manager 範本之後，您可以嘗試在其中一個委派的訂用帳戶中建立**enableHTTPstrafficonly 屬性**設定為**false**的儲存體帳戶，以確認已成功套用原則定義。 因為原則指派，所以您應該無法建立此儲存體帳戶。  

```powershell
New-AzStorageAccount -ResourceGroupName (New-AzResourceGroup -name policy-test -Location eastus -Force).ResourceGroupName `
                     -Name (get-random) `
                     -Location eastus `
                     -EnableHttpsTrafficOnly $false `
                     -SkuName Standard_LRS `
                     -Verbose                  
```

## <a name="clean-up-resources"></a>清除資源

當您完成時，請移除部署所建立的原則定義和指派。

```powershell
foreach ($ManagedSub in $ManagedSubscriptions)
{
    select-azsubscription -subscriptionId $ManagedSub.subscriptionId

    Remove-AzSubscriptionDeployment -Name mgmt -AsJob

    $Assignment = Get-AzPolicyAssignment | where-object {$_.Name -like "enforce-https-storage-assignment"}

    if ([string]::IsNullOrEmpty($Assignment))
    {
        Write-Output "Nothing to clean up - we're done"
    }
    else
    {

    Remove-AzPolicyAssignment -Name 'enforce-https-storage-assignment' -Scope "/subscriptions/$($ManagedSub.subscriptionId)" -Verbose

    Write-Output "Deployment has been deleted - we're done"
    }
}
```

## <a name="next-steps"></a>後續步驟

- 了解 [Azure 原則](../../governance/policy/index.yml)。
- 了解[跨租用戶管理體驗](../concepts/cross-tenant-management-experience.md)。
