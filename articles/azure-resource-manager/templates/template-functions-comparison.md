---
title: 範本函數-比較
description: 描述 Azure Resource Manager 範本中用來比較值的函式。
ms.topic: conceptual
ms.date: 09/05/2017
ms.openlocfilehash: 67cb1c1f92694ca217e99fb6528fb1d00f2cfcf6
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2020
ms.locfileid: "77207304"
---
# <a name="comparison-functions-for-azure-resource-manager-templates"></a>Azure Resource Manager 範本的比較函式

Resource Manager 提供了幾個用來在範本中進行比較的函式。

* [equals](#equals)
* [greater](#greater)
* [greaterOrEquals](#greaterorequals)
* [less](#less)
* [lessOrEquals](#lessorequals)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="equals"></a>equals
`equals(arg1, arg2)`

檢查兩個值是否彼此相等。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |整數、字串、陣列或物件 |要檢查是否相等的第一個值。 |
| arg2 |是 |整數、字串、陣列或物件 |要檢查是否相等的第二個值。 |

### <a name="return-value"></a>傳回值

如果值相等則傳回 **True**，否則會傳回 **False**。

### <a name="remarks"></a>備註

equals 函式通常會搭配 `condition` 元素，用來測試是否已部署資源。

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

### <a name="example"></a>範例

範本[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/equals.json)會檢查不同類型的值是否相等。 所有預設值都會傳回 True。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 1
        },
        "firstString": {
            "type": "string",
            "defaultValue": "a"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        },
        "firstArray": {
            "type": "array",
            "defaultValue": ["a", "b"]
        },
        "secondArray": {
            "type": "array",
            "defaultValue": ["a", "b"]
        },
        "firstObject": {
            "type": "object",
            "defaultValue": {"a": "b"}
        },
        "secondObject": {
            "type": "object",
            "defaultValue": {"a": "b"}
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[equals(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[equals(parameters('firstString'), parameters('secondString'))]"
        },
        "checkArrays": {
            "type": "bool",
            "value": "[equals(parameters('firstArray'), parameters('secondArray'))]"
        },
        "checkObjects": {
            "type": "bool",
            "value": "[equals(parameters('firstObject'), parameters('secondObject'))]"
        }
    }
}
```

上述範例中具有預設值的輸出如下：

| 名稱 | 類型 | 值 |
| ---- | ---- | ----- |
| checkInts | Bool | True |
| checkStrings | Bool | True |
| checkArrays | Bool | True |
| checkObjects | Bool | True |

若要使用 Azure CLI 部署此範例範本，請使用：

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/equals.json
```

若要使用 PowerShell 部署此範例範本，請使用：

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/equals.json 
```

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json)使用 [not](template-functions-logical.md#not) 搭配 **equals**。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    ],
    "outputs": {
        "checkNotEquals": {
            "type": "bool",
            "value": "[not(equals(1, 2))]"
        }
    }
}
```

前述範例的輸出為：

| 名稱 | 類型 | 值 |
| ---- | ---- | ----- |
| checkNotEquals | Bool | True |

若要使用 Azure CLI 部署此範例範本，請使用：

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/not-equals.json
```

若要使用 PowerShell 部署此範例範本，請使用：

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/not-equals.json 
```

## <a name="greater"></a>greater
`greater(arg1, arg2)`

檢查第一個值是否大於第二個值。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |整數或字串 |用於大於比較的第一個值。 |
| arg2 |是 |整數或字串 |用於大於比較的第二個值。 |

### <a name="return-value"></a>傳回值

如果第一個值大於第二個值則傳回 **True**，否則傳回 **False**。

### <a name="example"></a>範例

下列[範本範例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greater.json)會檢查某個值是否大於另一個值。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[greater(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[greater(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

上述範例中具有預設值的輸出如下：

| 名稱 | 類型 | 值 |
| ---- | ---- | ----- |
| checkInts | Bool | False |
| checkStrings | Bool | True |

若要使用 Azure CLI 部署此範例範本，請使用：

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greater.json
```

若要使用 PowerShell 部署此範例範本，請使用：

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greater.json 
```

## <a name="greaterorequals"></a>greaterOrEquals
`greaterOrEquals(arg1, arg2)`

檢查第一個值是否大於或等於第二個值。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |整數或字串 |用於大於或等於比較的第一個值。 |
| arg2 |是 |整數或字串 |用於大於或等於比較的第二個值。 |

### <a name="return-value"></a>傳回值

如果第一個值大於或等於第二個值則傳回 **True**，否則傳回 **False**。

### <a name="example"></a>範例

下列[範本範例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greaterorequals.json)會檢查某個值是否大於或等於另一個值。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[greaterOrEquals(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[greaterOrEquals(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

上述範例中具有預設值的輸出如下：

| 名稱 | 類型 | 值 |
| ---- | ---- | ----- |
| checkInts | Bool | False |
| checkStrings | Bool | True |

若要使用 Azure CLI 部署此範例範本，請使用：

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greaterorequals.json
```

若要使用 PowerShell 部署此範例範本，請使用：

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greaterorequals.json 
```

## <a name="less"></a>less
`less(arg1, arg2)`

檢查第一個值是否小於第二個值。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |整數或字串 |用於小於比較的第一個值。 |
| arg2 |是 |整數或字串 |用於小於比較的第二個值。 |

### <a name="return-value"></a>傳回值

如果第一個值小於第二個值則傳回 **True**，否則傳回 **False**。

### <a name="example"></a>範例

下列[範本範例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/less.json)會檢查某個值是否大於另一個值。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[less(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[less(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

上述範例中具有預設值的輸出如下：

| 名稱 | 類型 | 值 |
| ---- | ---- | ----- |
| checkInts | Bool | True |
| checkStrings | Bool | False |

若要使用 Azure CLI 部署此範例範本，請使用：

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/less.json
```

若要使用 PowerShell 部署此範例範本，請使用：

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/less.json 
```

## <a name="lessorequals"></a>lessOrEquals
`lessOrEquals(arg1, arg2)`

檢查第一個值是否小於或等於第二個值。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |整數或字串 |用於小於或等於比較的第一個值。 |
| arg2 |是 |整數或字串 |用於小於或等於比較的第二個值。 |

### <a name="return-value"></a>傳回值

如果第一個值小於或等於第二個值則傳回 **True**，否則傳回 **False**。

### <a name="example"></a>範例

下列[範本範例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/lessorequals.json)會檢查某個值是否大於或等於另一個值。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[lessOrEquals(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[lessOrEquals(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

上述範例中具有預設值的輸出如下：

| 名稱 | 類型 | 值 |
| ---- | ---- | ----- |
| checkInts | Bool | True |
| checkStrings | Bool | False |

若要使用 Azure CLI 部署此範例範本，請使用：

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/lessorequals.json
```

若要使用 PowerShell 部署此範例範本，請使用：

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/lessorequals.json 
```

## <a name="next-steps"></a>後續步驟
* 如需有關 Azure Resource Manager 範本中各區段的說明，請參閱[編寫 Azure Resource Manager 範本](template-syntax.md)。
* 若要合併多個範本，請參閱[透過 Azure Resource Manager 使用連結的範本](linked-templates.md)。
* 若要依指定的次數重複建立資源類型，請參閱 [在 Azure 資源管理員中建立資源的多個執行個體](copy-resources.md)。
* 若要了解如何部署已建立的範本，請參閱[使用 Azure Resource Manager 範本部署應用程式](deploy-powershell.md)。

