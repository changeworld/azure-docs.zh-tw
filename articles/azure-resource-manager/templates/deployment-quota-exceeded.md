---
title: 已超過部署配額
description: 描述如何解決資源群組歷程記錄中有超過800個部署的錯誤。
ms.topic: troubleshooting
ms.date: 08/07/2020
ms.openlocfilehash: 8996d7817eea2f8daf44fbc9b4416c884b05940f
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "87987047"
---
# <a name="resolve-error-when-deployment-count-exceeds-800"></a>解決部署計數超過800的錯誤

在其部署歷程記錄中，每個資源群組都限制為800個部署。 本文說明當部署失敗時所收到的錯誤，因為它會超過允許的800部署。 若要解決此錯誤，請從資源群組歷程記錄中刪除部署。 從歷程記錄中刪除部署，並不會影響任何已部署的資源。

Azure Resource Manager 會在您接近限制時，自動從您的歷程記錄中刪除部署。 您可能還是會因為下列其中一個原因而看到此錯誤：

1. 您在資源群組上有 CanNotDelete 鎖定，以防止從部署歷程記錄中刪除。
1. 您已選擇不自動刪除。
1. 您有大量的部署同時執行，而自動刪除的處理速度不夠快，無法減少總次數。

如需移除鎖定或選擇自動刪除的相關資訊，請參閱[從部署歷程記錄自動刪除](deployment-history-deletions.md)。

本文說明如何以手動方式從歷程記錄中刪除部署。

## <a name="symptom"></a>徵狀

在部署期間，您會收到錯誤，指出目前的部署將會超過800部署的配額。

## <a name="solution"></a>解決方法

### <a name="azure-cli"></a>Azure CLI

使用[az deployment group delete](/cli/azure/group/deployment)命令，從歷程記錄中刪除部署。

```azurecli-interactive
az deployment group delete --resource-group exampleGroup --name deploymentName
```

若要刪除5天之前的所有部署，請使用：

```azurecli-interactive
startdate=$(date +%F -d "-5days")
deployments=$(az deployment group list --resource-group exampleGroup --query "[?properties.timestamp<'$startdate'].name" --output tsv)

for deployment in $deployments
do
  az deployment group delete --resource-group exampleGroup --name $deployment
done
```

您可以使用下列命令取得部署歷程記錄中的目前計數：

```azurecli-interactive
az deployment group list --resource-group exampleGroup --query "length(@)"
```

### <a name="azure-powershell"></a>Azure PowerShell

使用[new-azresourcegroupdeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment)命令，從歷程記錄中刪除部署。

```azurepowershell-interactive
Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name deploymentName
```

若要刪除5天之前的所有部署，請使用：

```azurepowershell-interactive
$deployments = Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup | Where-Object Timestamp -lt ((Get-Date).AddDays(-5))

foreach ($deployment in $deployments) {
  Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name $deployment.DeploymentName
}
```

您可以使用下列命令取得部署歷程記錄中的目前計數：

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup).Count
```

## <a name="third-party-solutions"></a>協力廠商解決方案

下列外部解決方案會解決特定案例：

* [Azure Logic Apps 和 PowerShell 解決方案](https://devkimchi.com/2018/05/30/managing-excessive-arm-deployment-histories-with-logic-apps/)
* [AzDevOps 延伸模組](https://github.com/christianwaha/AzureDevOpsExtensionCleanRG)
