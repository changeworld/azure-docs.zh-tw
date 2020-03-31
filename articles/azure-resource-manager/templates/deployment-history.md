---
title: 部署歷程記錄
description: 說明如何使用入口網站、PowerShell、Azure CLI 及 REST API 來檢視 Azure Resource Manager 部署作業。
tags: top-support-issue
ms.topic: conceptual
ms.date: 11/26/2019
ms.openlocfilehash: b0f196f86bed05094b04bfc20c7cef2248a91c65
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460291"
---
# <a name="view-deployment-history-with-azure-resource-manager"></a>使用 Azure 資源管理器查看部署歷史記錄

Azure 資源管理器使您能夠查看部署歷史記錄並檢查過去部署中的特定操作。 您可以看到已部署的資源，並獲取有關任何錯誤的資訊。

如需解決特定部署錯誤的說明，請參閱 [針對使用 Azure Resource Manager 將資源部署至 Azure 時常見的錯誤進行疑難排解](common-deployment-errors.md)。

## <a name="get-deployments-and-correlation-id"></a>獲取部署和相關 ID

您可以通過 Azure 門戶、PowerShell、Azure CLI 或 REST API 查看有關部署的詳細資訊。 每個部署都有一個相關 ID，用於跟蹤相關事件。 當使用技術支援來排除部署故障時，這非常有用。

# <a name="portal"></a>[入口網站](#tab/azure-portal)

1. 選擇要檢查的資源組。

1. 選擇"**部署**"下的連結。

   ![選擇部署歷史記錄](./media/deployment-history/select-deployment-history.png)

1. 從部署歷史記錄中選擇一個部署。

   ![選取部署](./media/deployment-history/select-details.png)

1. 將顯示部署的摘要，包括相關 ID。

    ![部署摘要](./media/deployment-history/show-correlation-id.png)

# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)

要列出資源組的所有部署，請使用[獲取-AzResourceGroup 部署](/powershell/module/az.resources/Get-AzResourceGroupDeployment)命令。

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup
```

要從資源組獲取特定部署，添加 **"部署名稱"** 參數。

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment
```

若要取得相互關連識別碼，請使用：

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment).CorrelationId
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

要列出資源組的部署，請使用 az[部署組清單](/cli/azure/group/deployment?view=azure-cli-latest#az-deployment-group-list)。

```azurecli-interactive
az deployment group list --resource-group ExampleGroup
```

要獲取特定部署，請使用[az 部署組顯示](/cli/azure/group/deployment?view=azure-cli-latest#az-deployment-group-show)。

```azurecli-interactive
az deployment group show --resource-group ExampleGroup --name ExampleDeployment
```

若要取得相互關連識別碼，請使用：

```azurecli-interactive
az deployment group show --resource-group ExampleGroup --name ExampleDeployment --query properties.correlationId
```

# <a name="http"></a>[HTTP](#tab/http)

要列出資源組的部署，請使用以下操作。 有關在請求中使用的最新 API 版本號，請參閱[部署 - 按資源組列出](/rest/api/resources/deployments/listbyresourcegroup)。

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/?api-version={api-version}
```

獲取特定部署。 使用以下操作。 有關請求中要使用的最新 API 版本號，請參閱[部署 - 獲取](/rest/api/resources/deployments/get)。

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
```

回應包括相關 ID。

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

## <a name="get-deployment-operations-and-error-message"></a>獲取部署操作和錯誤訊息

每個部署可以包括多個操作。 要查看有關部署的更多詳細資訊，請查看部署操作。 當部署失敗時，部署操作包含一條錯誤訊息。

# <a name="portal"></a>[入口網站](#tab/azure-portal)

1. 在部署的摘要上，選擇 **"操作詳細資訊**"。

    ![選擇部署操作](./media/deployment-history/get-operation-details.png)

1. 您將看到部署步驟的詳細資訊。 發生錯誤時，詳細資訊包括錯誤訊息。

    ![顯示操作詳細資訊](./media/deployment-history/see-operation-details.png)

# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)

要查看要部署到資源組的部署操作，請使用[獲取-AzResourceGroup 部署操作操作命令](/powershell/module/az.resources/get-azdeploymentoperation)。

```azurepowershell-interactive
Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy
```

要查看失敗的操作，篩選具有 **"失敗"** 狀態的操作。

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy).Properties | Where-Object ProvisioningState -eq Failed
```

要獲取失敗操作的狀態訊息，請使用以下命令：

```azurepowershell-interactive
((Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy ).Properties | Where-Object ProvisioningState -eq Failed).StatusMessage.error
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

要查看部署到資源組的部署操作，請使用[az 部署組動作清單](/cli/azure/group/deployment/operation?view=azure-cli-latest#az-deployment-group-operation-list)命令。

```azurecli-interactive
az deployment group operation list --resource-group ExampleGroup --name ExampleDeployment
```

要查看失敗的操作，篩選具有 **"失敗"** 狀態的操作。

```azurecli-interactive
az deployment group operation list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed']"
```

要獲取失敗操作的狀態訊息，請使用以下命令：

```azurecli-interactive
az deployment group operation list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed'].properties.statusMessage.error"
```

# <a name="http"></a>[HTTP](#tab/http)

要獲取部署操作，請使用以下操作。 有關在請求中使用的最新 API 版本號，請參閱[部署操作 - 清單](/rest/api/resources/deploymentoperations/list)。

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
```

回應包含一條錯誤訊息。

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

