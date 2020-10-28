---
title: 限域部署中的範本函數
description: 描述如何在限域部署中解析範本函數。 範圍可以是租使用者、管理群組、訂用帳戶和資源群組。
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: cb02a3dc808604a80fd9943138c1cd0d8648904e
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92681481"
---
# <a name="arm-template-functions-in-deployment-scopes"></a>部署範圍中的 ARM 範本函式

使用 Azure Resource Manager 範本 (ARM 範本) ，您可以部署至資源群組、訂用帳戶、管理群組或租使用者。 一般而言， [ARM 範本](template-functions.md) 函式在所有範圍中的運作都相同。 本文說明某些函式因範圍而存在的差異。

## <a name="supported-functions"></a>支援的函數

部署至不同的範圍時，有一些重要的考慮：

* 資源群組部署 **支援** [ResourceGroup ( # B1](template-functions-resource.md#resourcegroup)函數。
* 資源群組和訂用帳戶部署 **支援** [ ( # B1](template-functions-resource.md#subscription)函數的訂用帳戶。
* 所有範圍都 **支援**[參考 ( # B1](template-functions-resource.md#reference)和 [list ( # B3](template-functions-resource.md#list)函數。
* 使用 [resourceId ( # B1 ](template-functions-resource.md#resourceid) 來取得資源群組中所部署資源的識別碼。

  ```json
  "subnet": {
    "id": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworkName'), parameters('subnet1Name'))]"
  }
  ```

* 使用 [subscriptionResourceId ( # B1 ](template-functions-resource.md#subscriptionresourceid) 函數取得在訂用帳戶中部署之資源的識別碼。

  例如，若要取得部署至訂用帳戶之原則定義的資源識別碼，請使用：

  ```json
  "roleDefinitionId": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
  ```

* 針對實作為管理群組延伸的資源，請使用 [extensionResourceId ( # B1 ](template-functions-resource.md#extensionresourceid) 函數。 部署至管理群組的自訂原則定義是管理群組的延伸。

  若要在管理群組層級取得自訂原則定義的資源識別碼，請使用：
  
  ```json
  "policyDefinitionId": "[extensionResourceId(variables('mgScope'), 'Microsoft.Authorization/policyDefinitions', parameters('policyDefinitionID'))]"
  ```

* 使用 [tenantResourceId](template-functions-resource.md#tenantresourceid) 函式來取得租使用者中所部署資源的識別碼。 內建原則定義是租使用者層級的資源。 在管理群組層級指派內建原則時，請使用 tenantResourceId 函數。

  若要取得內建原則定義的資源識別碼，請使用：
  
  ```json
  "policyDefinitionId": "[tenantResourceId('Microsoft.Authorization/policyDefinitions', parameters('policyDefinitionID'))]"
  ```

## <a name="function-resolution-in-scopes"></a>範圍中的函數解析

當您部署至一個以上的範圍時， [resourceGroup ( # B1 ](template-functions-resource.md#resourcegroup) 和 [訂用帳戶 ( # B3 ](template-functions-resource.md#subscription) 函式會根據您指定範本的方式，以不同的方式進行解析。 當您連結至外部範本時，函式一律會解析為該範本的範圍。 當您將範本巢狀嵌入父代範本中時，請使用 `expressionEvaluationOptions` 屬性來指定函式要解析為父代範本或巢狀範本的資源群組和訂用帳戶。 屬性設定為 `inner`，會解析成巢狀範本的範圍。 屬性設定為 `outer`，會解析成父代範本的範圍。

下表顯示函式會解析成父代或內嵌的資源群組和訂用帳戶。

| 範本類型 | 影響範圍 | 解決方案 |
| ------------- | ----- | ---------- |
| 巢狀        | 外部 (預設值) | 父代資源群組 |
| 巢狀        | inner | 子資源群組 |
| 連結        | N/A   | 子資源群組 |

以下[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json)示範：

* 具有預設 (外部) 範圍的巢狀範本
* 具有內部範圍的巢狀範本
* 連結的範本

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crossresourcegroupproperties.json":::

若要測試上述範本並查看結果，請使用 PowerShell 或 Azure CLI。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name parentGroup -Location southcentralus
New-AzResourceGroup -Name inlineGroup -Location southcentralus
New-AzResourceGroup -Name linkedGroup -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

前述範例的輸出為：

```output
 Name             Type                       Value
 ===============  =========================  ==========
 parentRG         String                     Parent resource group is parentGroup
 defaultScopeRG   String                     Default scope resource group is parentGroup
 innerScopeRG     String                     Inner scope resource group is inlineGroup
 linkedRG         String                     Linked resource group is linkedgroup
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az deployment group create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

前述範例的輸出為：

```output
"outputs": {
  "defaultScopeRG": {
    "type": "String",
    "value": "Default scope resource group is parentGroup"
  },
  "innerScopeRG": {
    "type": "String",
    "value": "Inner scope resource group is inlineGroup"
  },
  "linkedRG": {
    "type": "String",
    "value": "Linked resource group is linkedGroup"
  },
  "parentRG": {
    "type": "String",
    "value": "Parent resource group is parentGroup"
  }
},
```

---

## <a name="next-steps"></a>後續步驟

* 若要了解如何在您的範本中定義參數，請參閱[了解 Azure Resource Manager 範本的結構和語法](template-syntax.md)。
* 如需解決常見部署錯誤的秘訣，請參閱[使用 Azure Resource Manager 針對常見的 Azure 部署錯誤進行疑難排解](common-deployment-errors.md)。
* 如需部署需要 SAS 權杖之範本的詳細資訊，請參閱[使用 SAS 權杖部署私人範本](secure-template-with-sas-token.md)。
