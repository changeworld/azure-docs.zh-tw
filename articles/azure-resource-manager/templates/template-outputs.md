---
title: 範本中的輸出
description: 介紹如何在 Azure 資源管理器範本中定義輸出值。
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: 203bfc66e9515ef14a5fe1315ef5b9ee07075041
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460019"
---
# <a name="outputs-in-azure-resource-manager-template"></a>Azure 資源管理器範本中的輸出

本文介紹如何在 Azure 資源管理器範本中定義輸出值。 當您需要從已部署的資源傳回值時，可以使用輸出。

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

## <a name="conditional-output"></a>條件輸出

在輸出部分中，可以有條件地傳回值。 通常，在[有條件地部署](conditional-resource-deployment.md)資源時，在輸出中使用條件。 下面的示例演示如何根據是否部署了新 IP 位址有條件地返回公共 IP 位址的資源識別碼：

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

有關條件輸出的簡單示例，請參閱[條件輸出範本](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json)。

## <a name="dynamic-number-of-outputs"></a>動態輸出數

在某些情況下，您不知道創建範本時需要返回的值的實例數。 可以使用**複製**元素返回可變值數。

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

有關詳細資訊，請參閱[Azure 資源管理器範本中的輸出反覆運算](copy-outputs.md)。

## <a name="linked-templates"></a>連結的範本

若要從連結範本檢索輸出值，請使用父範本中的[引用](template-functions-resource.md#reference)函數。 父範本中的語法是：

```json
"[reference('<deploymentName>').outputs.<propertyName>.value]"
```

從連結的範本取得輸出屬性時，屬性名稱不能包含連字號。

下面的示例演示如何通過從連結範本檢索值來設置負載等化器上的 IP 位址。

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

您無法在[巢狀範本](linked-templates.md#nested-template)的輸出區段中使用 `reference` 函式。 若要傳回巢狀範本中已部署資源的值，請將巢狀範本轉換成連結範本。

## <a name="get-output-values"></a>獲取輸出值

部署成功後，輸出值將自動返回到部署結果中。

要從部署歷史記錄中獲取輸出值，可以使用腳本。

# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)

```azurepowershell-interactive
(Get-AzResourceGroupDeployment `
  -ResourceGroupName <resource-group-name> `
  -Name <deployment-name>).Outputs.resourceID.value
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az deployment group show \
  -g <resource-group-name> \
  -n <deployment-name> \
  --query properties.outputs.resourceID.value
```

---

## <a name="example-templates"></a>範本的範例

以下示例演示了使用輸出的方案。

|[範本]  |描述  |
|---------|---------|
|[複製變數](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | 建立複雜的變數，並輸出那些值。 不會部署任何資源。 |
|[公共 IP 位址](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | 建立公用 IP 位址，並輸出資源識別碼。 |
|[負載等化器](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | 上述範本的連結。 建立負載平衡器時，在輸出中使用資源識別碼。 |

## <a name="next-steps"></a>後續步驟

* 要瞭解輸出的可用屬性，請參閱[瞭解 Azure 資源管理器範本的結構和語法](template-syntax.md)。
