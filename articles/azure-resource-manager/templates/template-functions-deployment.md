---
title: 範本函式-部署
description: 描述在 Azure Resource Manager 範本中使用的函式 (ARM 範本) 取得部署資訊。
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: 438afc947b07ac7425de365a2d63c427cf53e2ff
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943467"
---
# <a name="deployment-functions-for-arm-templates"></a>ARM 範本的部署功能

Resource Manager 提供下列函式來取得與目前部署 Azure Resource Manager 範本 (ARM 範本) 相關的值：

* [部署](#deployment)
* [environment (環境)](#environment)
* [parameters](#parameters)
* [變數](#variables)

若要從資源、資源群組或訂用帳戶中取得值，請參閱 [資源函式](template-functions-resource.md)。

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="deployment"></a>部署

`deployment()`

傳回目前部署作業的相關資訊。

### <a name="return-value"></a>傳回值

此函式會傳回部署期間所傳遞的物件。 傳回之物件中的屬性會根據您是否為：

* 部署範本或範本規格。
* 部署本機檔案的範本，或部署可透過 URI 存取之遠端檔案的範本。
* 部署至資源群組，或部署至其中一個其他範圍 ([Azure 訂](deploy-to-subscription.md)用帳戶、 [管理群組](deploy-to-management-group.md)或 [租](deploy-to-tenant.md) 使用者) 。

將本機範本部署至資源群組時：此函數會傳回下列格式：

```json
{
  "name": "",
  "properties": {
    "template": {
      "$schema": "",
      "contentVersion": "",
      "parameters": {},
      "variables": {},
      "resources": [],
      "outputs": {}
    },
    "templateHash": "",
    "parameters": {},
    "mode": "",
    "provisioningState": ""
  }
}
```

將遠端範本部署至資源群組時：此函數會傳回下列格式：

```json
{
  "name": "",
  "properties": {
    "templateLink": {
      "uri": ""
    },
    "template": {
      "$schema": "",
      "contentVersion": "",
      "parameters": {},
      "variables": {},
      "resources": [],
      "outputs": {}
    },
    "templateHash": "",
    "parameters": {},
    "mode": "",
    "provisioningState": ""
  }
}
```

將範本規格部署至資源群組時：此函數會傳回下列格式：

```json
{
  "name": "",
  "properties": {
    "templateLink": {
      "id": ""
    },
    "template": {
      "$schema": "",
      "contentVersion": "",
      "parameters": {},
      "variables": {},
      "resources": [],
      "outputs": {}
    },
    "templateHash": "",
    "parameters": {},
    "mode": "",
    "provisioningState": ""
  }
}
```

當您部署至 Azure 訂用帳戶、管理群組或租使用者時，傳回物件會包含 `location` 屬性。 部署本機範本或外部範本時，包含 location 屬性。 其格式為：

```json
{
  "name": "",
  "location": "",
  "properties": {
    "template": {
      "$schema": "",
      "contentVersion": "",
      "resources": [],
      "outputs": {}
    },
    "templateHash": "",
    "parameters": {},
    "mode": "",
    "provisioningState": ""
  }
}
```

### <a name="remarks"></a>備註

您可以上層範本的 URI 作為基礎，使用部署() 連結至另一個範本。

# <a name="json"></a>[JSON](#tab/json)

```json
"variables": {
  "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var sharedTemplateUrl = uri(deployment().prperties.templateLink.uri, 'shared-resources.json')
```

---

如果您在入口網站中重新部署部署歷程記錄中的範本，此範本會部署為本機檔案。 `templateLink` 屬性不會在部署函式中傳回。 如果您的範本依賴 `templateLink` 來建構另一個範本的連結，請勿使用入口網站來重新部署。 請改用您原先用來部署範本的命令。

### <a name="example"></a>範例

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/deployment.json)會傳回部署物件︰

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "deploymentOutput": {
      "type": "object",
      "value": "[deployment()]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output deploymentOutput object = deployment()
```

---

上述範例會傳回下列物件︰

```json
{
  "name": "deployment",
  "properties": {
    "template": {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": [],
      "outputs": {
        "deploymentOutput": {
          "type": "Object",
          "value": "[deployment()]"
        }
      }
    },
    "templateHash": "13135986259522608210",
    "parameters": {},
    "mode": "Incremental",
    "provisioningState": "Accepted"
  }
}
```

## <a name="environment"></a>環境

`environment()`

傳回用於部署之 Azure 環境的相關資訊。

### <a name="return-value"></a>傳回值

此函數會傳回目前 Azure 環境的屬性。 下列範例顯示全域 Azure 的屬性。 主權雲端可能會傳回稍微不同的屬性。

```json
{
  "name": "",
  "gallery": "",
  "graph": "",
  "portal": "",
  "graphAudience": "",
  "activeDirectoryDataLake": "",
  "batch": "",
  "media": "",
  "sqlManagement": "",
  "vmImageAliasDoc": "",
  "resourceManager": "",
  "authentication": {
    "loginEndpoint": "",
    "audiences": [
      "",
      ""
    ],
    "tenant": "",
    "identityProvider": ""
  },
  "suffixes": {
    "acrLoginServer": "",
    "azureDatalakeAnalyticsCatalogAndJob": "",
    "azureDatalakeStoreFileSystem": "",
    "azureFrontDoorEndpointSuffix": "",
    "keyvaultDns": "",
    "sqlServerHostname": "",
    "storage": ""
  }
}
```

### <a name="example"></a>範例

下列範例範本會傳回環境物件。

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "environmentOutput": {
      "type": "object",
      "value": "[environment()]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output environmentOutput object = environment()
```

---

上述範例會在部署到全域 Azure 時傳回下列物件：

```json
{
  "name": "AzureCloud",
  "gallery": "https://gallery.azure.com/",
  "graph": "https://graph.windows.net/",
  "portal": "https://portal.azure.com",
  "graphAudience": "https://graph.windows.net/",
  "activeDirectoryDataLake": "https://datalake.azure.net/",
  "batch": "https://batch.core.windows.net/",
  "media": "https://rest.media.azure.net",
  "sqlManagement": "https://management.core.windows.net:8443/",
  "vmImageAliasDoc": "https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json",
  "resourceManager": "https://management.azure.com/",
  "authentication": {
    "loginEndpoint": "https://login.windows.net/",
    "audiences": [
      "https://management.core.windows.net/",
      "https://management.azure.com/"
    ],
    "tenant": "common",
    "identityProvider": "AAD"
  },
  "suffixes": {
    "acrLoginServer": ".azurecr.io",
    "azureDatalakeAnalyticsCatalogAndJob": "azuredatalakeanalytics.net",
    "azureDatalakeStoreFileSystem": "azuredatalakestore.net",
    "azureFrontDoorEndpointSuffix": "azurefd.net",
    "keyvaultDns": ".vault.azure.net",
    "sqlServerHostname": ".database.windows.net",
    "storage": "core.windows.net"
  }
}
```

## <a name="parameters"></a>參數

`parameters(parameterName)`

傳回參數值。 指定的參數名稱必須定義於範本的 parameters 區段中。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| parameterName |是 |字串 |要傳回的參數名稱。 |

### <a name="return-value"></a>傳回值

指定參數的值。

### <a name="remarks"></a>備註

一般而言，您可以使用參數來設定資源的值。 下列範例會將網站的名稱設定為部署期間所傳入的參數值。

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "siteName": {
    "type": "string"
  }
}, "resources": [
  {
    "type": "Microsoft.Web/Sites",
    "apiVersion": "2016-08-01",
    "name": "[parameters('siteName')]",
    ...
  }
]
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param siteName string

resource mySite 'Microsoft.Web/Sites@2016-08-01' = {
  name: siteName
  ...
}
```

---

### <a name="example"></a>範例

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/parameters.json)顯示 parameters 函式的簡化用法。

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stringParameter": {
      "type": "string",
      "defaultValue": "option 1"
    },
    "intParameter": {
      "type": "int",
      "defaultValue": 1
    },
    "objectParameter": {
      "type": "object",
      "defaultValue": {
        "one": "a",
        "two": "b"
      }
    },
    "arrayParameter": {
      "type": "array",
      "defaultValue": [ 1, 2, 3 ]
    },
    "crossParameter": {
      "type": "string",
      "defaultValue": "[parameters('stringParameter')]"
    }
  },
  "variables": {},
  "resources": [],
  "outputs": {
    "stringOutput": {
      "value": "[parameters('stringParameter')]",
      "type": "string"
    },
    "intOutput": {
      "value": "[parameters('intParameter')]",
      "type": "int"
    },
    "objectOutput": {
      "value": "[parameters('objectParameter')]",
      "type": "object"
    },
    "arrayOutput": {
      "value": "[parameters('arrayParameter')]",
      "type": "array"
    },
    "crossOutput": {
      "value": "[parameters('crossParameter')]",
      "type": "string"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param stringParameter string = 'option 1'
param intParameter int = 1
param objectParameter object = {
  'one': 'a'
  'two': 'b'
}
param arrayParameter array = [
  1
  2
  3
]
param crossParameter string = stringParameter

output stringOutput string = stringParameter
output intOutput int = intParameter
output objectOutput object = objectParameter
output arrayOutput array = arrayParameter
output crossOutput string = crossParameter
```

---

上述範例中具有預設值的輸出如下：

| 名稱 | 類型 | 值 |
| ---- | ---- | ----- |
| stringOutput | String | 選項 1 |
| intOutput | Int | 1 |
| objectOutput | Object | {"one": "a", "two": "b"} |
| arrayOutput | Array | [1, 2, 3] |
| crossOutput | String | 選項 1 |

如需使用參數的詳細資訊，請參閱 [ARM 範本中的參數](template-parameters.md)。

## <a name="variables"></a>variables

`variables(variableName)`

傳回變數的值。 指定的變數名稱必須定義於範本的 variables 區段中。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| variableName |是 |String |要傳回的變數名稱。 |

### <a name="return-value"></a>傳回值

指定變數的值。

### <a name="remarks"></a>備註

一般而言，您可以使用變數將範本簡化，方法是僅建構一次複雜的值。 下列範例會建構儲存體帳戶的唯一名稱。

# <a name="json"></a>[JSON](#tab/json)

```json
"variables": {
  "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageName')]",
    ...
  },
  {
    "type": "Microsoft.Compute/virtualMachines",
    "dependsOn": [
      "[variables('storageName')]"
    ],
    ...
  }
],

```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var storageName = concat('storage', uniqueString(resourceGroup().id))

resource myStorage 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageName
  ...
}

resource myVm 'Microsoft.Compute/virtualMachines@2020-06-01' = {
  ...
}
```

---

### <a name="example"></a>範例

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/variables.json)會傳回不同的變數值。

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {
    "var1": "myVariable",
    "var2": [ 1, 2, 3, 4 ],
    "var3": "[ variables('var1') ]",
    "var4": {
      "property1": "value1",
      "property2": "value2"
    }
  },
  "resources": [],
  "outputs": {
    "exampleOutput1": {
      "value": "[variables('var1')]",
      "type": "string"
    },
    "exampleOutput2": {
      "value": "[variables('var2')]",
      "type": "array"
    },
    "exampleOutput3": {
      "value": "[variables('var3')]",
      "type": "string"
    },
    "exampleOutput4": {
      "value": "[variables('var4')]",
      "type": "object"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var var1 = 'myVariable'
var var2 = [
  1
  2
  3
  4
]
var var3 = var1
var var4 = {
  'property1': 'value1'
  'property2': 'value2'
}

output exampleOutput1 string = var1
output exampleOutput2 array = var2
output exampleOutput3 string = var3
output exampleOutput4 object = var4
```

---

上述範例中具有預設值的輸出如下：

| 名稱 | 類型 | 值 |
| ---- | ---- | ----- |
| exampleOutput1 | String | myVariable |
| exampleOutput2 | Array | [1, 2, 3, 4] |
| exampleOutput3 | String | myVariable |
| exampleOutput4 |  Object | {"property1": "value1", "property2": "value2"} |

如需使用變數的詳細資訊，請參閱 [ARM 範本中的變數](template-variables.md)。

## <a name="next-steps"></a>後續步驟

* 如需 ARM 範本中各區段的說明，請參閱 [瞭解 arm 範本的結構和語法](template-syntax.md)。
