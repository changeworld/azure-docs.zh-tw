---
title: 範本函式-物件
description: 描述在 Azure Resource Manager 範本中用來處理物件的函數。
ms.topic: conceptual
ms.date: 10/12/2020
ms.openlocfilehash: 632e92bb798a5e8469079ef4693b7f321617f88c
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91977879"
---
# <a name="object-functions-for-arm-templates"></a>ARM 範本的物件函式

Resource Manager 提供數個函式來處理 Azure Resource Manager (ARM) 範本中的物件。

* [contains](#contains)
* [createObject](#createobject)
* [empty](#empty)
* [intersection](#intersection)
* [json](#json)
* [length](#length) (長度)
* [null](#null)
* [union](#union)

## <a name="contains"></a>contains

`contains(container, itemToFind)`

檢查陣列中是否包含值、物件中是否包含索引鍵，或字串中是否包含子字串。 字串比較會區分大小寫。 不過，測試時，如果物件包含索引鍵，比較便不區分大小寫。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| 容器 |是 |陣列、物件或字串 |其中包含要尋找之值的值。 |
| itemToFind |是 |字串或整數 |要尋找的值。 |

### <a name="return-value"></a>傳回值

找到項目則傳回 **True**，否則會傳回 **False**。

### <a name="example"></a>範例

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/contains.json)顯示如何使用不同類型的 contains：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToTest": {
            "type": "string",
            "defaultValue": "OneTwoThree"
        },
        "objectToTest": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c"}
        },
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "stringTrue": {
            "type": "bool",
            "value": "[contains(parameters('stringToTest'), 'e')]"
        },
        "stringFalse": {
            "type": "bool",
            "value": "[contains(parameters('stringToTest'), 'z')]"
        },
        "objectTrue": {
            "type": "bool",
            "value": "[contains(parameters('objectToTest'), 'one')]"
        },
        "objectFalse": {
            "type": "bool",
            "value": "[contains(parameters('objectToTest'), 'a')]"
        },
        "arrayTrue": {
            "type": "bool",
            "value": "[contains(parameters('arrayToTest'), 'three')]"
        },
        "arrayFalse": {
            "type": "bool",
            "value": "[contains(parameters('arrayToTest'), 'four')]"
        }
    }
}
```

上述範例中具有預設值的輸出如下：

| 名稱 | 類型 | 值 |
| ---- | ---- | ----- |
| stringTrue | Bool | True |
| stringFalse | Bool | False |
| objectTrue | Bool | True |
| objectFalse | Bool | False |
| arrayTrue | Bool | True |
| arrayFalse | Bool | False |

## <a name="createobject"></a>createObject

`createObject(key1, value1, key2, value2, ...)`

從索引鍵和值建立物件。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| key1 |否 |字串 |索引鍵名稱。 |
| value1 |否 |int、boolean、string、object 或 array |索引鍵的值。 |
| 其他金鑰 |否 |字串 |索引鍵的其他名稱。 |
| 其他值 |否 |int、boolean、string、object 或 array |索引鍵的其他值。 |

函數只接受偶數的參數。 每個金鑰都必須有相符的值。

### <a name="return-value"></a>傳回值

具有每個索引鍵和值配對的物件。

### <a name="example"></a>範例

下列範例會從不同類型的值建立物件。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    ],
    "outputs": {
        "newObject": {
            "type": "object",
            "value": "[createObject('intProp', 1, 'stringProp', 'abc', 'boolProp', true(), 'arrayProp', createArray('a', 'b', 'c'), 'objectProp', createObject('key1', 'value1'))]"
        }
    }
}
```

上述範例中具有預設值的輸出是以下列值命名的物件 `newObject` ：

```json
{
  "intProp": 1,
  "stringProp": "abc",
  "boolProp": true,
  "arrayProp": ["a", "b", "c"],
  "objectProp": {"key1": "value1"}
}
```

## <a name="empty"></a>empty

`empty(itemToTest)`

判斷陣列、物件或字串是否空白。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| itemToTest |是 |陣列、物件或字串 |要檢查它是否為空的值。 |

### <a name="return-value"></a>傳回值

如果值空白則傳回 **True**，否則會傳回 **False**。

### <a name="example"></a>範例

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/empty.json)會檢查陣列、物件和字串是否空白。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testArray": {
            "type": "array",
            "defaultValue": []
        },
        "testObject": {
            "type": "object",
            "defaultValue": {}
        },
        "testString": {
            "type": "string",
            "defaultValue": ""
        }
    },
    "resources": [
    ],
    "outputs": {
        "arrayEmpty": {
            "type": "bool",
            "value": "[empty(parameters('testArray'))]"
        },
        "objectEmpty": {
            "type": "bool",
            "value": "[empty(parameters('testObject'))]"
        },
        "stringEmpty": {
            "type": "bool",
            "value": "[empty(parameters('testString'))]"
        }
    }
}
```

上述範例中具有預設值的輸出如下：

| 名稱 | 類型 | 值 |
| ---- | ---- | ----- |
| arrayEmpty | Bool | True |
| objectEmpty | Bool | True |
| stringEmpty | Bool | True |

## <a name="intersection"></a>交集

`intersection(arg1, arg2, arg3, ...)`

從參數中傳回具有共同元素的單一陣列或物件。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |陣列或物件 |要用來尋找共同元素的第一個值。 |
| arg2 |是 |陣列或物件 |要用來尋找共同元素的第二個值。 |
| 其他引數 |否 |陣列或物件 |要用來尋找共同元素的其他值。 |

### <a name="return-value"></a>傳回值

具有共同元素的陣列或物件。

### <a name="example"></a>範例

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/intersection.json)顯示如何搭配使用 intersection 與陣列和物件︰

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstObject": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c"}
        },
        "secondObject": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "z", "three": "c"}
        },
        "firstArray": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        },
        "secondArray": {
            "type": "array",
            "defaultValue": ["two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "objectOutput": {
            "type": "object",
            "value": "[intersection(parameters('firstObject'), parameters('secondObject'))]"
        },
        "arrayOutput": {
            "type": "array",
            "value": "[intersection(parameters('firstArray'), parameters('secondArray'))]"
        }
    }
}
```

上述範例中具有預設值的輸出如下：

| 名稱 | 類型 | 值 |
| ---- | ---- | ----- |
| objectOutput | Object | {"one": "a", "three": "c"} |
| arrayOutput | Array | ["two", "three"] |

## <a name="json"></a>json

`json(arg1)`

將有效的 JSON 字串轉換成 JSON 資料類型。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |字串 |要轉換為 JSON 的值。 字串必須是格式正確的 JSON 字串。 |

### <a name="return-value"></a>傳回值

指定之字串的 JSON 資料類型，如果指定 **null** 則為空值。

### <a name="remarks"></a>備註

如果您需要在 JSON 物件中包含參數值或變數，請使用 [concat](template-functions-string.md#concat) 函式來建立傳遞給函式的字串。

您也可以使用 [null ( # B1 ](#null) 來取得 null 值。

### <a name="example"></a>範例

下列 [範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/json.json) 示範如何使用 json 函數。 請注意，您可以針對空白物件傳遞 **null** 。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "jsonEmptyObject": {
            "type": "string",
            "defaultValue": "null"
        },
        "jsonObject": {
            "type": "string",
            "defaultValue": "{\"a\": \"b\"}"
        },
        "jsonString": {
            "type": "string",
            "defaultValue": "\"test\""
        },
        "jsonBoolean": {
            "type": "string",
            "defaultValue": "true"
        },
        "jsonInt": {
            "type": "string",
            "defaultValue": "3"
        },
        "jsonArray": {
            "type": "string",
            "defaultValue": "[[1,2,3 ]"
        },
        "concatValue": {
            "type": "string",
            "defaultValue": "demo value"
        }
    },
    "resources": [
    ],
    "outputs": {
        "emptyObjectOutput": {
            "type": "bool",
            "value": "[empty(json(parameters('jsonEmptyObject')))]"
        },
        "objectOutput": {
            "type": "object",
            "value": "[json(parameters('jsonObject'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[json(parameters('jsonString'))]"
        },
        "booleanOutput": {
            "type": "bool",
            "value": "[json(parameters('jsonBoolean'))]"
        },
        "intOutput": {
            "type": "int",
            "value": "[json(parameters('jsonInt'))]"
        },
        "arrayOutput": {
            "type": "array",
            "value": "[json(parameters('jsonArray'))]"
        },
        "concatObjectOutput": {
            "type": "object",
            "value": "[json(concat('{\"a\": \"', parameters('concatValue'), '\"}'))]"
        }
    }
}
```

上述範例中具有預設值的輸出如下：

| 名稱 | 類型 | 值 |
| ---- | ---- | ----- |
| emptyObjectOutput | 布林值 | True |
| objectOutput | Object | {"a": "b"} |
| stringOutput | String | test |
| booleanOutput | 布林值 | True |
| intOutput | 整數 | 3 |
| arrayOutput | Array | [ 1, 2, 3 ] |
| concatObjectOutput | Object | {"a"： "示範值"} |

## <a name="length"></a>長度

`length(arg1)`

傳回陣列中的元素數目、字串中的字元或物件中的根層級屬性。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |陣列、字串或物件 |用來取得元素數目的陣列、用來取得字元數的字串，或用來取得根層級屬性數目的物件。 |

### <a name="return-value"></a>傳回值

整數。

### <a name="example"></a>範例

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/length.json)顯示如何搭配使用 length 與陣列和字串：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [
                "one",
                "two",
                "three"
            ]
        },
        "stringToTest": {
            "type": "string",
            "defaultValue": "One Two Three"
        },
        "objectToTest": {
            "type": "object",
            "defaultValue": {
                "propA": "one",
                "propB": "two",
                "propC": "three",
                "propD": {
                    "propD-1": "sub",
                    "propD-2": "sub"
                }
            }
        }
    },
    "resources": [],
    "outputs": {
        "arrayLength": {
            "type": "int",
            "value": "[length(parameters('arrayToTest'))]"
        },
        "stringLength": {
            "type": "int",
            "value": "[length(parameters('stringToTest'))]"
        },
        "objectLength": {
            "type": "int",
            "value": "[length(parameters('objectToTest'))]"
        }
    }
}
```

上述範例中具有預設值的輸出如下：

| 名稱 | 類型 | 值 |
| ---- | ---- | ----- |
| arrayLength | Int | 3 |
| stringLength | Int | 13 |
| objectLength | Int | 4 |

## <a name="null"></a>null

`null()`

傳回 null。

### <a name="parameters"></a>參數

Null 函數不接受任何參數。

### <a name="return-value"></a>傳回值

一律為 null 的值。

### <a name="example"></a>範例

下列範例會使用 null 函數。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "emptyOutput": {
            "type": "bool",
            "value": "[empty(null())]"
        },
    }
}
```

前述範例的輸出為：

| 名稱 | 類型 | 值 |
| ---- | ---- | ----- |
| emptyOutput | Bool | True |

## <a name="union"></a>union

`union(arg1, arg2, arg3, ...)`

從參數中傳回具有所有元素的單一陣列或物件。 重複的值或索引鍵只會納入一次。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |陣列或物件 |用來聯結元素的第一個值。 |
| arg2 |是 |陣列或物件 |用來聯結元素的第二個值。 |
| 其他引數 |否 |陣列或物件 |用來聯結元素的其他值。 |

### <a name="return-value"></a>傳回值

陣列或物件。

### <a name="example"></a>範例

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/union.json)顯示如何搭配使用 union 與陣列和物件︰

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstObject": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c1"}
        },
        "secondObject": {
            "type": "object",
            "defaultValue": {"three": "c2", "four": "d", "five": "e"}
        },
        "firstArray": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        },
        "secondArray": {
            "type": "array",
            "defaultValue": ["three", "four"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "objectOutput": {
            "type": "object",
            "value": "[union(parameters('firstObject'), parameters('secondObject'))]"
        },
        "arrayOutput": {
            "type": "array",
            "value": "[union(parameters('firstArray'), parameters('secondArray'))]"
        }
    }
}
```

上述範例中具有預設值的輸出如下：

| 名稱 | 類型 | 值 |
| ---- | ---- | ----- |
| objectOutput | Object | {"one": "a", "two": "b", "three": "c2", "four": "d", "five": "e"} |
| arrayOutput | Array | ["one", "two", "three", "four"] |

## <a name="next-steps"></a>後續步驟

* 如需 Azure Resource Manager 範本中各區段的說明，請參閱 [瞭解 ARM 範本的結構和語法](template-syntax.md)。
