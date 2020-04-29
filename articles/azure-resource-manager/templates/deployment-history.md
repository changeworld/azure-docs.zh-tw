---
title: 部署歷程記錄
description: 說明如何使用入口網站、PowerShell、Azure CLI 及 REST API 來檢視 Azure Resource Manager 部署作業。
tags: top-support-issue
ms.topic: conceptual
ms.date: 11/26/2019
ms.openlocfilehash: b0f196f86bed05094b04bfc20c7cef2248a91c65
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79460291"
---
# <a name="view-deployment-history-with-azure-resource-manager"></a>使用 Azure Resource Manager 來查看部署歷程記錄

Azure Resource Manager 可讓您查看部署歷程記錄，並檢查過去部署中的特定作業。 您可以查看已部署的資源，並取得任何錯誤的相關資訊。

如需解決特定部署錯誤的說明，請參閱 [針對使用 Azure Resource Manager 將資源部署至 Azure 時常見的錯誤進行疑難排解](common-deployment-errors.md)。

## <a name="get-deployments-and-correlation-id"></a>取得部署和相互關聯識別碼

您可以透過 [Azure 入口網站]、[PowerShell]、[Azure CLI] 或 [REST API] 來查看部署的詳細資料。 每個部署都有相互關聯識別碼，可用來追蹤相關事件。 當您使用技術支援來疑難排解部署時，這會很有説明。

# <a name="portal"></a>[入口網站](#tab/azure-portal)

1. 選取您想要檢查的資源群組。

1. 選取 [**部署**] 底下的連結。

   ![選取部署歷程記錄](./media/deployment-history/select-deployment-history.png)

1. 從部署歷程記錄中選取其中一個部署。

   ![選取部署](./media/deployment-history/select-details.png)

1. 隨即會顯示部署的摘要，包括相互關聯識別碼。

    ![部署摘要](./media/deployment-history/show-correlation-id.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要列出資源群組的所有部署，請使用[new-azresourcegroupdeployment](/powershell/module/az.resources/Get-AzResourceGroupDeployment)命令。

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup
```

若要從資源群組取得特定部署，請新增**DeploymentName**參數。

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment
```

若要取得相互關連識別碼，請使用：

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment).CorrelationId
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要列出資源群組的部署，請使用[az deployment group list](/cli/azure/group/deployment?view=azure-cli-latest#az-deployment-group-list)。

```azurecli-interactive
az deployment group list --resource-group ExampleGroup
```

若要取得特定部署，請使用[az deployment group show](/cli/azure/group/deployment?view=azure-cli-latest#az-deployment-group-show)。

```azurecli-interactive
az deployment group show --resource-group ExampleGroup --name ExampleDeployment
```

若要取得相互關連識別碼，請使用：

```azurecli-interactive
az deployment group show --resource-group ExampleGroup --name ExampleDeployment --query properties.correlationId
```

# <a name="http"></a>[HTTP](#tab/http)

若要列出資源群組的部署，請使用下列操作。 如需在要求中使用的最新 API 版本號碼，請參閱[部署-依資源群組列出](/rest/api/resources/deployments/listbyresourcegroup)。

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/?api-version={api-version}
```

以取得特定部署。 請使用下列操作。 如需在要求中使用的最新 API 版本號碼，請參閱[部署-取得](/rest/api/resources/deployments/get)。

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
```

回應包含相互關聯識別碼。

```json
{
 ...
 "properties": {
   "mode": "Incremental",
   "provisioningState": "Failed",
   "timestamp": "2019-11-26T14:18:36.4518358Z",
   "duration": "PT26.2091817S",
   "correlationId": "47ff4228-bf2e-4ee5-a008-0b07da681230",
   ...
 }
}
```

---

## <a name="get-deployment-operations-and-error-message"></a>取得部署作業和錯誤訊息

每個部署都可以包含多個作業。 若要查看部署的更多詳細資料，請參閱部署作業。 當部署失敗時，部署作業會包含一則錯誤訊息。

# <a name="portal"></a>[入口網站](#tab/azure-portal)

1. 在部署的 [摘要] 上，選取 [作業**詳細資料**]。

    ![選取部署作業](./media/deployment-history/get-operation-details.png)

1. 您會看到該部署步驟的詳細資料。 發生錯誤時，詳細資料會包含錯誤訊息。

    ![顯示作業詳細資料](./media/deployment-history/see-operation-details.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要查看部署至資源群組的部署作業，請使用[AzResourceGroupDeploymentOperation](/powershell/module/az.resources/get-azdeploymentoperation)命令。

```azurepowershell-interactive
Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy
```

若要查看失敗的作業，請以**失敗**狀態篩選作業。

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy).Properties | Where-Object ProvisioningState -eq Failed
```

若要取得失敗作業的狀態訊息，請使用下列命令：

```azurepowershell-interactive
((Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy ).Properties | Where-Object ProvisioningState -eq Failed).StatusMessage.error
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要查看部署至資源群組的部署作業，請使用[az deployment group operation list](/cli/azure/group/deployment/operation?view=azure-cli-latest#az-deployment-group-operation-list)命令。

```azurecli-interactive
az deployment group operation list --resource-group ExampleGroup --name ExampleDeployment
```

若要查看失敗的作業，請以**失敗**狀態篩選作業。

```azurecli-interactive
az deployment group operation list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed']"
```

若要取得失敗作業的狀態訊息，請使用下列命令：

```azurecli-interactive
az deployment group operation list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed'].properties.statusMessage.error"
```

# <a name="http"></a>[HTTP](#tab/http)

若要取得部署作業，請使用下列操作。 如需在要求中使用的最新 API 版本號碼，請參閱[部署作業-清單](/rest/api/resources/deploymentoperations/list)。

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
```

回應包含錯誤訊息。

```json
{
  "value": [
    {
      "id": "/subscriptions/xxxx/resourceGroups/examplegroup/providers/Microsoft.Resources/deployments/exampledeploy/operations/13EFD9907103D640",
      "operationId": "13EFD9907103D640",
      "properties": {
        "provisioningOperation": "Create",
        "provisioningState": "Failed",
        "timestamp": "2019-11-26T14:18:36.3177613Z",
        "duration": "PT21.0580179S",
        "trackingId": "9d3cdac4-54f8-486c-94bd-10c20867b8bc",
        "serviceRequestId": "01a9d0fe-896b-4c94-a30f-60b70a8f1ad9",
        "statusCode": "BadRequest",
        "statusMessage": {
          "error": {
            "code": "InvalidAccountType",
            "message": "The AccountType Standard_LRS1 is invalid. For more information, see - https://aka.ms/storageaccountskus"
          }
        },
        "targetResource": {
          "id": "/subscriptions/xxxx/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/storageq2czadzfgizc2",
          "resourceType": "Microsoft.Storage/storageAccounts",
          "resourceName": "storageq2czadzfgizc2"
        }
      }
    },
    ...
  ]
}
```

---

## <a name="next-steps"></a>後續步驟

* 如需解決特定部署錯誤的說明，請參閱 [針對使用 Azure Resource Manager 將資源部署至 Azure 時常見的錯誤進行疑難排解](common-deployment-errors.md)。
* 若要了解如何使用活動記錄來監視其他類型的動作，請參閱[檢視活動記錄以管理 Azure 資源](../management/view-activity-logs.md)。
* 若要在執行之前驗證您的部署，請參閱 [使用 Azure Resource Manager 範本部署資源群組](deploy-powershell.md)。

