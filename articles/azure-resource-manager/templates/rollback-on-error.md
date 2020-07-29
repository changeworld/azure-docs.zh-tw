---
title: 發生錯誤時回復至部署成功
description: 指定失敗的部署應回復至成功部署。
ms.topic: conceptual
ms.date: 10/04/2019
ms.openlocfilehash: 206c794996f58a4c5b6982c551ae50128ed4f5eb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "79460138"
---
# <a name="rollback-on-error-to-successful-deployment"></a>發生錯誤時回復至部署成功

當部署失敗時，您可以從部署記錄自動重新部署先前成功的部署。 如果您的基礎結構部署有已知的良好狀態，而且想要還原為此狀態，此功能就很有用。 有一些警告和限制：

- 重新部署的執行方式與先前使用相同參數執行的完全相同。 您無法變更參數。
- 先前的部署是使用[完整模式](./deployment-modes.md#complete-mode)來執行。 先前部署中未包含的任何資源都會刪除，而且任何資源設定都會設為先前的狀態。 請確定您完全瞭解[部署模式](./deployment-modes.md)。
- 重新部署只會影響資源，任何資料變更都不會受到影響。
- 您只能將此功能用於資源群組部署，而不能使用訂用帳戶或管理群組層級部署。 如需訂用帳戶層級部署的詳細資訊，請參閱在訂用帳戶[層級建立資源群組和資源](./deploy-to-subscription.md)。
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
