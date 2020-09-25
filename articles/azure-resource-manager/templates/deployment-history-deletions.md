---
title: 部署歷程記錄刪除
description: 描述 Azure Resource Manager 如何自動從部署歷程記錄中刪除部署。 當歷程記錄接近超過800的限制時，就會刪除部署。
ms.topic: conceptual
ms.date: 09/15/2020
ms.openlocfilehash: 0c5d972eea9bc9cf2bf8716b26cd0e07d0a07b82
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91284728"
---
# <a name="automatic-deletions-from-deployment-history"></a>從部署歷程記錄自動刪除

每次部署範本時，部署的相關資訊都會寫入部署歷程記錄。 在部署歷程記錄中，每個資源群組的限制為800個部署。

當您接近限制時，Azure Resource Manager 會自動從您的歷程記錄中刪除部署。 自動刪除是過去行為的變更。 先前，您必須手動從部署歷程記錄中刪除部署，以避免發生錯誤。 這是在2020年8月6日實行的變更。

**資源群組部署支援自動刪除。目前， [訂](deploy-to-subscription.md)用帳戶、 [管理群組](deploy-to-management-group.md)和 [租](deploy-to-tenant.md) 使用者部署的歷程記錄中的部署不會自動刪除。**

> [!NOTE]
> 從歷程記錄中刪除部署並不會影響已部署的任何資源。

## <a name="when-deployments-are-deleted"></a>刪除部署時

當您到達775或更多部署時，會從您的歷程記錄中刪除部署。 Azure Resource Manager 會在歷程記錄降至750之前刪除部署。 系統一律會先刪除最舊的部署。

:::image type="content" border="false" source="./media/deployment-history-deletions/deployment-history.svg" alt-text="從部署歷程記錄中刪除":::

> [!NOTE]
> 起始數位 (775) ，而 (750) 的結束數位可能會變更。
>
> 如果您的資源群組已達到800限制，則下一個部署將會失敗並出現錯誤。 自動刪除進程會立即開始。 短暫的等候之後，您可以再次嘗試部署。

除了部署之外，您也會在執行「 [假設](template-deploy-what-if.md) 」作業或驗證部署時觸發刪除作業。

當您為部署指定與歷程記錄中的名稱相同的名稱時，您會重設其在歷程記錄中的位置。 部署會移至歷程記錄中最近的位置。 當您在發生錯誤之後復原 [至該部署](rollback-on-error.md) 時，您也會重設部署的位置。

## <a name="remove-locks-that-block-deletions"></a>移除封鎖刪除的鎖定

如果您在資源群組上有 [CanNotDelete 鎖定](../management/lock-resources.md) ，則無法刪除該資源群組的部署。 您必須移除鎖定，才能利用部署歷程記錄中的自動刪除。

若要使用 PowerShell 來刪除鎖定，請執行下列命令：

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName lockedRG).LockId
Remove-AzResourceLock -LockId $lockId
```

若要使用 Azure CLI 刪除鎖定，請執行下列命令：

```azurecli-interactive
lockid=$(az lock show --resource-group lockedRG --name deleteLock --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="opt-out-of-automatic-deletions"></a>退出自動刪除

您可以退出宣告歷程記錄的自動刪除。 **只有當您想要自行管理部署歷程記錄時，才能使用此選項。** 在歷程記錄中，仍會強制執行800部署的限制。 如果您超過800部署，將會收到錯誤訊息，而且您的部署將會失敗。

若要停用自動刪除，請註冊 `Microsoft.Resources/DisableDeploymentGrooming` 功能旗標。 當您註冊功能旗標時，您會選擇不自動刪除整個 Azure 訂用帳戶。 您無法只退出宣告特定的資源群組。 若要重新啟用自動刪除，請取消註冊功能旗標。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

針對 PowerShell，請使用 [AzProviderFeature](/powershell/module/az.resources/Register-AzProviderFeature)。

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

若要查看您的訂用帳戶目前的狀態，請使用：

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

若要重新啟用自動刪除，請使用 Azure REST API 或 Azure CLI。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

針對 Azure CLI，請使用 [az feature register](/cli/azure/feature#az-feature-register)。

```azurecli-interactive
az feature register --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

若要查看您的訂用帳戶目前的狀態，請使用：

```azurecli-interactive
az feature show --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

若要重新啟用自動刪除，請使用 [az feature 取消註冊](/cli/azure/feature#az-feature-unregister)。

```azurecli-interactive
az feature unregister --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

# <a name="rest"></a>[REST](#tab/rest)

針對 REST API，請使用 [功能-註冊](/rest/api/resources/features/register)。

```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/register?api-version=2015-12-01
```

若要查看您的訂用帳戶目前的狀態，請使用：

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/register?api-version=2015-12-01
```

若要重新啟用自動刪除，請使用 [功能-取消註冊](/rest/api/resources/features/unregister)

```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/unregister?api-version=2015-12-01
```

---

## <a name="next-steps"></a>後續步驟

* 若要瞭解如何查看部署歷程記錄，請參閱 [使用 Azure Resource Manager 來查看部署歷程記錄](deployment-history.md)。
