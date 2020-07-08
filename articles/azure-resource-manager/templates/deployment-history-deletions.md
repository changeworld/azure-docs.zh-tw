---
title: 部署歷程記錄刪除
description: 描述 Azure Resource Manager 如何自動從部署歷程記錄中刪除部署。 當歷程記錄接近超過800的限制時，就會刪除部署。
ms.topic: conceptual
ms.date: 07/06/2020
ms.openlocfilehash: 70730ce814ebc689d9672952bad7c3dd39b5a7f1
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/06/2020
ms.locfileid: "85981651"
---
# <a name="automatic-deletions-from-deployment-history"></a>從部署歷程記錄自動刪除

每次部署範本時，部署的相關資訊都會寫入至部署歷程記錄。 在其部署歷程記錄中，每個資源群組都限制為800個部署。

Azure Resource Manager 很快就會在您接近限制時，自動從歷程記錄中刪除部署。 自動刪除是過去行為的變更。 之前，您必須手動從部署歷程記錄中刪除部署，以避免收到錯誤。 **這項功能尚未新增至 Azure。如果您想要退出，我們會通知您這項即將進行的變更。**

> [!NOTE]
> 從歷程記錄中刪除部署，並不會影響任何已部署的資源。
>
> 如果您在資源群組上有[CanNotDelete 鎖定](../management/lock-resources.md)，就無法刪除該資源群組的部署。 您必須移除鎖定，才能利用部署歷程記錄中的自動刪除。

## <a name="when-deployments-are-deleted"></a>當部署已刪除時

當您到達790部署時，會從部署歷程記錄中刪除部署。 Azure Resource Manager 會刪除一小組最舊的部署，以清除空間供未來部署之用。 您的歷程記錄會維持不變。 通常會先刪除最舊的部署。

:::image type="content" border="false" source="./media/deployment-history-deletions/deployment-history.svg" alt-text="從部署歷程記錄刪除":::

除了部署之外，您也會在執行「[假設](template-deploy-what-if.md)」作業或驗證部署時觸發刪除作業。

當您提供部署與歷程記錄中的相同名稱時，您會在歷程記錄中重設其位置。 部署會移至歷程記錄中最新的位置。 當您在錯誤發生後[回復至該部署](rollback-on-error.md)時，也會重設部署的位置。

> [!NOTE]
> 如果您的資源群組已達到800限制，則您的下一個部署會失敗併發生錯誤。 自動刪除進程會立即啟動。 您可以在短暫的等候後再次嘗試部署。

## <a name="opt-out-of-automatic-deletions"></a>選擇不自動刪除

您可以選擇不要從歷程記錄自動刪除。 **只有當您想要自行管理部署歷程記錄時，才使用此選項。** 記錄中的800部署限制仍會強制執行。 如果您超過800部署，您將會收到錯誤，而且您的部署將會失敗。

若要停用自動刪除，請註冊 `Microsoft.Resources/DisableDeploymentGrooming` 功能旗標。 當您註冊功能旗標時，您會選擇不自動刪除整個 Azure 訂用帳戶。 您無法只退出宣告特定的資源群組。 若要重新啟用自動刪除，請取消註冊功能旗標。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

針對 PowerShell，請使用[AzProviderFeature](/powershell/module/az.resources/Register-AzProviderFeature)。

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

若要查看您的訂用帳戶目前的狀態，請使用：

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

若要重新啟用自動刪除，請使用 Azure REST API 或 Azure CLI。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

針對 Azure CLI，請使用[az feature register](/cli/azure/feature#az-feature-register)。

```azurecli-interactive
az feature register --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

若要查看您的訂用帳戶目前的狀態，請使用：

```azurecli-interactive
az feature show --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

若要重新啟用自動刪除，請使用[az feature 取消註冊](/cli/azure/feature#az-feature-unregister)。

```azurecli-interactive
az feature unregister --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

# <a name="rest"></a>[REST](#tab/rest)

如 REST API，請使用[功能-Register](/rest/api/resources/features/register)。

```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/register?api-version=2015-12-01
```

若要查看您的訂用帳戶目前的狀態，請使用：

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/register?api-version=2015-12-01
```

若要重新啟用自動刪除，請使用[功能-取消註冊](/rest/api/resources/features/unregister)

```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/unregister?api-version=2015-12-01
```

---

## <a name="next-steps"></a>後續步驟

* 若要瞭解如何查看部署歷程記錄，請參閱[使用 Azure Resource Manager 查看部署歷程記錄](deployment-history.md)。
