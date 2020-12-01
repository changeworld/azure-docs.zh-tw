---
title: 範本中的輸出
description: 說明如何)  (ARM 範本中定義 Azure Resource Manager 範本的輸出值。
ms.topic: conceptual
ms.date: 11/24/2020
ms.openlocfilehash: 6a375dfbc767bcbbfd8ec6b7f8cb9e942e275582
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353522"
---
# <a name="outputs-in-arm-templates"></a>ARM 範本中的輸出

本文說明如何在 Azure Resource Manager 範本中定義輸出值 (ARM 範本) 。 當您需要從已部署的資源傳回值時，您可以使用輸出。

每個輸出值的格式都必須符合其中一種 [資料類型](template-syntax.md#data-types)。

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

在 [輸出] 區段中，您可以有條件地傳回值。 一般來說，當您有 [條件地部署](conditional-resource-deployment.md) 資源時，會在輸出中使用條件。 下列範例示範如何根據是否部署新的 IP 位址，有條件地傳回公用 IP 位址的資源識別碼：

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

如需條件式輸出的簡單範例，請參閱 [條件式輸出範本](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json)。

## <a name="dynamic-number-of-outputs"></a>動態輸出數目

在某些情況下，您不知道建立範本時需要傳回值的實例數目。 您可以使用 **copy** 元素傳回變數的值數目。

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

如需詳細資訊，請參閱 [Azure Resource Manager 範本中的輸出反復](copy-outputs.md)專案。

## <a name="linked-templates"></a>連結的範本

若要從連結的範本中取出輸出值，請使用父範本中的 [參考](template-functions-resource.md#reference) 函式。 父範本中的語法為：

```json
"[reference('<deploymentName>').outputs.<propertyName>.value]"
```

從連結的範本取得輸出屬性時，屬性名稱不能包含連字號。

下列範例顯示如何從連結的範本中取出值，以設定負載平衡器上的 IP 位址。

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

您無法在[巢狀範本](linked-templates.md#nested-template)的輸出區段中使用 `reference` 函式。 若要傳回巢狀範本中已部署資源的值，請將巢狀範本轉換成連結範本。

## <a name="get-output-values"></a>取得輸出值

當部署成功時，系統會自動在部署的結果中傳回輸出值。

若要從部署歷程記錄取得輸出值，您可以使用腳本。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

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

下列範例示範使用輸出的案例。

|範本  |說明  |
|---------|---------|
|[複製變數](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | 建立複雜的變數，並輸出那些值。 不會部署任何資源。 |
|[公用 IP 位址](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | 建立公用 IP 位址，並輸出資源識別碼。 |
|[負載平衡器](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | 上述範本的連結。 建立負載平衡器時，在輸出中使用資源識別碼。 |

## <a name="next-steps"></a>後續步驟

* 若要瞭解輸出的可用屬性，請參閱 [瞭解 ARM 範本的結構和語法](template-syntax.md)。
