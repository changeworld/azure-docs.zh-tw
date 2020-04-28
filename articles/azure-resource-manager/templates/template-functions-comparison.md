---
title: 範本函數-比較
description: 描述 Azure Resource Manager 範本中用來比較值的函式。
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: a9b7b32475695e5222b87c8fe75e8982f34ebb21
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82192326"
---
# <a name="comparison-functions-for-arm-templates"></a>ARM 範本的比較函式

Resource Manager 提供了數個函式，可在您的 Azure Resource Manager （ARM）範本中進行比較。

* [equals](#equals)
* [greater](#greater)
* [greaterOrEquals](#greaterorequals)
* [低](#less)
* [lessOrEquals](#lessorequals)

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

## <a name="next-steps"></a>後續步驟

* 如需 Azure Resource Manager 範本中各區段的說明，請參閱[瞭解 ARM 範本的結構和語法](template-syntax.md)。
