---
title: 超出部署配額
description: 描述如何解決資源組歷史記錄中部署超過 800 個的錯誤。
ms.topic: troubleshooting
ms.date: 10/04/2019
ms.openlocfilehash: 919cd9a3482401cd47516e2677b0bf58387488b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80245084"
---
# <a name="resolve-error-when-deployment-count-exceeds-800"></a>當部署計數超過 800 時解決錯誤

每個資源組在其部署歷史記錄中限制為 800 個部署。 本文介紹了部署失敗時收到的錯誤，因為它將超過允許的 800 個部署。 要解決此錯誤，請從資源組歷史記錄中刪除部署。 從歷史記錄中刪除部署不會影響已部署的任何資源。

## <a name="symptom"></a>徵狀

在部署期間，您會收到一個錯誤，指出當前部署將超過 800 個部署的配額。

## <a name="solution"></a>解決方法

### <a name="azure-cli"></a>Azure CLI

使用[az 部署組刪除](/cli/azure/group/deployment)命令從歷史記錄中刪除部署。

```azurecli-interactive
az deployment group delete --resource-group exampleGroup --name deploymentName
```

要刪除超過五天的所有部署，請使用：

```azurecli-interactive
startdate=$(date +%F -d "-5days")
deployments=$(az deployment group list --resource-group exampleGroup --query "[?properties.timestamp<'$startdate'].name" --output tsv)

for deployment in $deployments
do
  az deployment group delete --resource-group exampleGroup --name $deployment
done
```

可以使用以下命令獲取部署歷史記錄中的當前計數：

```azurecli-interactive
az deployment group list --resource-group exampleGroup --query "length(@)"
```

### <a name="azure-powershell"></a>Azure PowerShell

使用["刪除-AzResourceGroup 部署"](/powershell/module/az.resources/remove-azresourcegroupdeployment)命令從歷史記錄中刪除部署。

```azurepowershell-interactive
Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name deploymentName
```

要刪除超過五天的所有部署，請使用：

```azurepowershell-interactive
$deployments = Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup | Where-Object Timestamp -lt ((Get-Date).AddDays(-5))

foreach ($deployment in $deployments) {
  Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name $deployment.DeploymentName
}
```

可以使用以下命令獲取部署歷史記錄中的當前計數：

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup).Count
```

## <a name="third-party-solutions"></a>協力廠商解決方案

以下外部解決方案可解決特定方案：

* [Azure 邏輯應用和 PowerShell 解決方案](https://devkimchi.com/2018/05/30/managing-excessive-arm-deployment-histories-with-logic-apps/)
* [阿茲德沃普斯擴展](https://github.com/christianwaha/AzureDevOpsExtensionCleanRG)
