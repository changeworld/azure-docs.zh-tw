---
title: 大規模地將 Azure 原則部署至委派的訂用帳戶
description: 了解 Azure 委派資源管理如何讓您跨多個租用戶部署原則定義和原則指派。
ms.date: 11/8/2019
ms.topic: conceptual
ms.openlocfilehash: 3fe7e48c56e9a5af93e9642ee16c50cfbce34f9e
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81481829"
---
# <a name="deploy-azure-policy-to-delegated-subscriptions-at-scale"></a>大規模地將 Azure 原則部署至委派的訂用帳戶

身為服務提供者，您可能已將多個客戶租用戶上架以進行 Azure 委派資源管理。 [Azure Lighthouse](../overview.md) 可讓服務提供者一次在多個租用戶之間執行大規模作業，讓管理工作更有效率。

本主題說明如何使用 [Azure 原則](../../governance/policy/index.yml)，使用 PowerShell 命令在多個租用戶之間部署原則定義和原則指派。 在此範例中，原則定義可確保只允許 HTTPS 流量來保護儲存體帳戶。

## <a name="use-azure-resource-graph-to-query-across-customer-tenants"></a>使用 Azure Resource Graph 在客戶租用戶之間進行查詢

您可以使用 [Azure Resource Graph](../../governance/resource-graph/index.yml)，在您所管理客戶租用戶中的所有訂用帳戶之間進行查詢。 在此示例中,我們將標識這些訂閱中當前不需要 HTTPS 流量的任何存儲帳戶。  

```powershell
$MspTenant = "insert your managing tenantId here"

$subs = Get-AzSubscription

$ManagedSubscriptions = Search-AzGraph -Query "ResourceContainers | where type == 'microsoft.resources/subscriptions' | where tenantId != '$($mspTenant)' | project name, subscriptionId, tenantId" -subscription $subs.subscriptionId

Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | project name, location, subscriptionId, tenantId, properties.supportsHttpsTrafficOnly" -subscription $ManagedSubscriptions.subscriptionId | convertto-json
```

## <a name="deploy-a-policy-across-multiple-customer-tenants"></a>在多個客戶租用戶之間部署原則

下列範例顯示如何使用 [Azure Resource Manager 範本](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/policy-enforce-https-storage/enforceHttpsStorage.json)，在多個客戶租用戶中的委派訂用帳戶之間，部署原則定義和原則指派。 此策略定義要求所有儲存帳戶使用 HTTPS 流量,防止創建不符合的任何新存儲帳戶,並在未將設置設置為不合規的情況下將現有存儲帳戶標記為不合規。

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

部署 Azure 資源管理器樣本後,可以通過嘗試在其中一個委派訂閱中創建啟用**HSTrafficOnly**設定為**false**的儲存帳戶來確認策略定義已成功應用。 因為原則指派，所以您應該無法建立此儲存體帳戶。  

```powershell
New-AzStorageAccount -ResourceGroupName (New-AzResourceGroup -name policy-test -Location eastus -Force).ResourceGroupName `
                     -Name (get-random) `
                     -Location eastus `
                     -EnableHttpsTrafficOnly $false `
                     -SkuName Standard_LRS `
                     -Verbose                  
```

## <a name="clean-up-resources"></a>清除資源

完成後,刪除部署創建的策略定義和分配。

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
