---
title: 範本中的輸出
description: 描述如何在 Azure Resource Manager 範本中定義輸出值。
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: ec96b45cdc5ccf488d46c2d8da03caf16d002dfa
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2020
ms.locfileid: "77622848"
---
# <a name="outputs-in-azure-resource-manager-template"></a>Azure Resource Manager 範本中的輸出

本文說明如何在 Azure Resource Manager 範本中定義輸出值。 當您需要從已部署的資源傳回值時，您可以使用輸出。

## <a name="define-output-values"></a>定義輸出值

下列範例示範如何傳回公用 IP 位址的資源識別碼：

```json
"outputs": {
  "resourceID": {
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

## <a name="conditional-output"></a>條件式輸出

在 [輸出] 區段中，您可以有條件地傳回值。 一般而言，當您有[條件地部署](conditional-resource-deployment.md)資源時，您會在輸出中使用條件。 下列範例示範如何根據是否已部署新的公用 IP 位址，有條件地傳回其資源識別碼：

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

如需條件式輸出的簡單範例，請參閱[條件式輸出範本](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json)。

## <a name="dynamic-number-of-outputs"></a>動態的輸出數目

在某些情況下，您不知道建立範本時需要傳回之值的實例數目。 您可以使用**copy**元素來傳回變數數目的值。

```json
"outputs": {
  "storageEndpoints": {
    "type": "array",
    "copy": {
      "count": "[parameters('storageCount')]",
      "input": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]"
    }
  }
}
```

如需詳細資訊，請參閱[Azure Resource Manager 範本中的輸出反復](copy-outputs.md)專案。

## <a name="linked-templates"></a>連結的範本

若要從連結的範本中取出輸出值，請使用父範本中的[reference](template-functions-resource.md#reference)函式。 父範本中的語法為：

```json
"[reference('<deploymentName>').outputs.<propertyName>.value]"
```

從連結的範本取得輸出屬性時，屬性名稱不能包含連字號。

下列範例示範如何藉由從連結的範本中抓取值來設定負載平衡器上的 IP 位址。

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

您無法在`reference`巢狀範本[的輸出區段中使用 ](linked-templates.md#nested-template) 函式。 若要傳回巢狀範本中已部署資源的值，請將巢狀範本轉換成連結範本。

## <a name="get-output-values"></a>取得輸出值

當部署成功時，會在部署結果中自動傳回輸出值。

若要從部署歷程記錄取得輸出值，您可以使用腳本。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
(Get-AzResourceGroupDeployment `
  -ResourceGroupName <resource-group-name> `
  -Name <deployment-name>).Outputs.resourceID.value
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group deployment show \
  -g <resource-group-name> \
  -n <deployment-name> \
  --query properties.outputs.resourceID.value
```

---

## <a name="example-templates"></a>範本的範例

下列範例示範使用輸出的案例。

|[範本]  |描述  |
|---------|---------|
|[複製變數](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | 建立複雜的變數，並輸出那些值。 不會部署任何資源。 |
|[公用 IP 位址](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | 建立公用 IP 位址，並輸出資源識別碼。 |
|[負載平衡器](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | 上述範本的連結。 建立負載平衡器時，在輸出中使用資源識別碼。 |

## <a name="next-steps"></a>後續步驟

* 若要瞭解輸出可用的屬性，請參閱[瞭解 Azure Resource Manager 範本的結構和語法](template-syntax.md)。
