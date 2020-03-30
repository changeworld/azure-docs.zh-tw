---
title: 回滾錯誤以成功部署
description: 指定失敗的部署應回滾到成功部署。
ms.topic: conceptual
ms.date: 10/04/2019
ms.openlocfilehash: 206c794996f58a4c5b6982c551ae50128ed4f5eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460138"
---
# <a name="rollback-on-error-to-successful-deployment"></a>將錯誤回滾到成功部署

當部署失敗時，您可以從部署記錄自動重新部署先前成功的部署。 如果基礎結構部署具有已知良好的狀態，並且想要恢復到此狀態，此功能非常有用。 有許多注意事項和限制：

- 重新部署的運行方式與以前使用相同的參數運行完全相同。 不能更改參數。
- 以前的部署使用[完整模式](./deployment-modes.md#complete-mode)運行。 刪除上一個部署中未包括的任何資源，並將任何資源配置設置為其以前的狀態。 請確保您完全理解[部署模式](./deployment-modes.md)。
- 重新部署僅影響資源，任何資料更改都會受到影響。
- 只能將此功能用於資源組部署，而不能將訂閱或管理組級別部署使用。 有關訂閱級別部署的詳細資訊，請參閱[在訂閱級別創建資源組和資源](./deploy-to-subscription.md)。
- 您只可以使用此選項搭配根層級部署。 從巢狀範本部署不適用於重新部署。

若要使用這個選項，您的部署必須有唯一的名稱，以便在歷程記錄中進行識別。 如果您沒有唯一的名稱，則目前失敗的部署可能會覆寫歷程記錄中先前成功的部署。

## <a name="powershell"></a>PowerShell

若要重新部署最後一個成功的部署，請新增 `-RollbackToLastDeployment` 參數作為旗標。

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollbackToLastDeployment
```

若要重新部署特定部署，請使用 `-RollBackDeploymentName` 參數，並提供部署的名稱。 指定的部署必須成功。

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollBackDeploymentName ExampleDeployment01
```

## <a name="azure-cli"></a>Azure CLI

若要重新部署最後一個成功的部署，請新增 `--rollback-on-error` 參數作為旗標。

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error
```

若要重新部署特定部署，請使用 `--rollback-on-error` 參數，並提供部署的名稱。 指定的部署必須成功。

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment02 \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error ExampleDeployment01
```

## <a name="rest-api"></a>REST API

如果目前的部署失敗，若要重新部署最後一個成功的部署，請使用：

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "LastSuccessful",
    }
  }
}
```

如果目前的部署失敗，若要重新部署特定部署，請使用：

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "SpecificDeployment",
      "deploymentName": "<deploymentname>"
    }
  }
}
```

指定的部署必須成功。

## <a name="next-steps"></a>後續步驟

- 若要安全地在多個區域推出您的服務，請參閱 [Azure 部署管理員](deployment-manager-overview.md)。
- 若要指定如何處理存在於資源群組中、但尚未定義於範本中的資源，請參閱 [Azure Resource Manager 部署模式](deployment-modes.md)。
- 若要了解如何在您的範本中定義參數，請參閱[了解 Azure Resource Manager 範本的結構和語法](template-syntax.md)。
- 如需部署需要 SAS 權杖之範本的詳細資訊，請參閱[使用 SAS 權杖部署私人範本](secure-template-with-sas-token.md)。
