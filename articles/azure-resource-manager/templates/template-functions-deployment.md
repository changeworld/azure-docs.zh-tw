---
title: 範本函式-部署
description: 描述 Azure Resource Manager 範本中用來擷取部署資訊的函式。
ms.topic: conceptual
ms.date: 11/27/2019
ms.openlocfilehash: b241aaf43ee3204c9960d0099ce3c61d4c1a80ee
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2020
ms.locfileid: "77207276"
---
# <a name="deployment-functions-for-azure-resource-manager-templates"></a>Azure Resource Manager 範本的部署函式 

Resource Manager 提供下列函數來取得與目前部署相關的值：

* [部署](#deployment)
* [環境](#environment)
* [parameters](#parameters)
* [變數](#variables)

若要從資源、資源群組或訂用帳戶中取得值，請參閱 [資源函式](template-functions-resource.md)。

## <a name="deployment"></a>部署

`deployment()`

傳回目前部署作業的相關資訊。

### <a name="return-value"></a>傳回值

此函式會傳回部署期間所傳遞的物件。 視部署物件是以連結或內嵌物件形式傳遞，所傳回物件中的屬性將有所不同。 部署物件以內嵌形式傳遞時 (例如使用 Azure PowerShell 中的 **-TemplateFile** 參數指向本機檔案時)，所傳回的物件為下列格式：

```json
{
    "name": "",
    "properties": {
        "template": {
            "$schema": "",
            "contentVersion": "",
            "parameters": {},
            "variables": {},
            "resources": [
            ],
            "outputs": {}
        },
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

部署物件以連結形式傳遞時 (例如使用 **-TemplateUri** 參數指向遠端檔案時)，所傳回的物件為下列格式： 

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
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

當您[部署到 Azure 訂用帳戶](deploy-to-subscription.md)而不是資源群組時，傳回的物件包含 `location` 屬性。 部署本機範本或外部範本時，包含 location 屬性。

### <a name="remarks"></a>備註

您可以上層範本的 URI 作為基礎，使用部署() 連結至另一個範本。

```json
"variables": {  
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"  
}
```  

如果您在入口網站中重新部署部署歷程記錄中的範本，此範本會部署為本機檔案。 `templateLink` 屬性不會在部署函式中傳回。 如果您的範本依賴 `templateLink` 來建構另一個範本的連結，請勿使用入口網站來重新部署。 請改用您原先用來部署範本的命令。

### <a name="example"></a>範例

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/deployment.json)會傳回部署物件︰

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "subscriptionOutput": {
            "value": "[deployment()]",
            "type" : "object"
        }
    }
}
```

上述範例會傳回下列物件︰

```json
{
  "name": "deployment",
  "properties": {
    "template": {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": [],
      "outputs": {
        "subscriptionOutput": {
          "type": "Object",
          "value": "[deployment()]"
        }
      }
    },
    "parameters": {},
    "mode": "Incremental",
    "provisioningState": "Accepted"
  }
}
```

如需使用部署函式的訂用帳戶層級範本，請參閱[訂用帳戶部署函式](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/deploymentsubscription.json)。 它會使用 `az deployment create` 或 `New-AzDeployment` 命令進行部署。

## <a name="environment"></a>Environment

`environment()`

傳回用於部署之 Azure 環境的相關資訊。

### <a name="return-value"></a>傳回值

此函式會傳回目前 Azure 環境的屬性。 下列範例顯示全域 Azure 的屬性。 主權雲端可能會傳回稍微不同的屬性。

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

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "environmentOutput": {
            "value": "[environment()]",
            "type" : "object"
        }
    }
}
```

上述範例會在部署至全域 Azure 時傳回下列物件：

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

```json
"parameters": { 
  "siteName": {
      "type": "string"
  }
},
"resources": [
   {
      "apiVersion": "2016-08-01",
      "name": "[parameters('siteName')]",
      "type": "Microsoft.Web/Sites",
      ...
   }
]
```

### <a name="example"></a>範例

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/parameters.json)顯示 parameters 函式的簡化用法。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringParameter": {
            "type" : "string",
            "defaultValue": "option 1"
        },
        "intParameter": {
            "type": "int",
            "defaultValue": 1
        },
        "objectParameter": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b"}
        },
        "arrayParameter": {
            "type": "array",
            "defaultValue": [1, 2, 3]
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
            "type" : "string"
        },
        "intOutput": {
            "value": "[parameters('intParameter')]",
            "type" : "int"
        },
        "objectOutput": {
            "value": "[parameters('objectParameter')]",
            "type" : "object"
        },
        "arrayOutput": {
            "value": "[parameters('arrayParameter')]",
            "type" : "array"
        },
        "crossOutput": {
            "value": "[parameters('crossParameter')]",
            "type" : "string"
        }
    }
}
```

上述範例中具有預設值的輸出如下：

| 名稱 | 類型 | 值 |
| ---- | ---- | ----- |
| stringOutput | String | 選項 1 |
| intOutput | Int | 1 |
| objectOutput | Object | {"one": "a", "two": "b"} |
| arrayOutput | Array | [1, 2, 3] |
| crossOutput | String | 選項 1 |

如需使用參數的詳細資訊，請參閱[Azure Resource Manager 範本中的參數](template-parameters.md)。

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

### <a name="example"></a>範例

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/variables.json)會傳回不同的變數值。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {
        "var1": "myVariable",
        "var2": [ 1,2,3,4 ],
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
            "type" : "string"
        },
        "exampleOutput2": {
            "value": "[variables('var2')]",
            "type" : "array"
        },
        "exampleOutput3": {
            "value": "[variables('var3')]",
            "type" : "string"
        },
        "exampleOutput4": {
            "value": "[variables('var4')]",
            "type" : "object"
        }
    }
}
```

上述範例中具有預設值的輸出如下：

| 名稱 | 類型 | 值 |
| ---- | ---- | ----- |
| exampleOutput1 | String | myVariable |
| exampleOutput2 | Array | [1, 2, 3, 4] |
| exampleOutput3 | String | myVariable |
| exampleOutput4 |  Object | {"property1": "value1", "property2": "value2"} |

如需使用變數的詳細資訊，請參閱[Azure Resource Manager 範本中的變數](template-variables.md)。

## <a name="next-steps"></a>後續步驟
* 如需有關 Azure Resource Manager 範本中各區段的說明，請參閱[編寫 Azure Resource Manager 範本](template-syntax.md)。
* 若要合併數個範本，請參閱[透過 Azure Resource Manager 使用連結的範本](linked-templates.md)。
* 若要依指定的次數重複建立資源類型，請參閱 [在 Azure 資源管理員中建立資源的多個執行個體](copy-resources.md)。
* 若要了解如何部署已建立的範本，請參閱[使用 Azure Resource Manager 範本部署應用程式](deploy-powershell.md)。

