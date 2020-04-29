---
title: 範本函式-字串
description: 描述 Azure Resource Manager 範本中用來使用字串的函式。
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: c0517375b273384f263e8ba421995d4afb6c193b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80982409"
---
# <a name="string-functions-for-arm-templates"></a>ARM 範本的字串函式

Resource Manager 提供下列函式，以便在您的 Azure Resource Manager （ARM）範本中使用字串：

* [base64](#base64)
* [base64ToJson](#base64tojson)
* [base64ToString](#base64tostring)
* [concat](#concat)
* [包含](#contains)
* [dataUri](#datauri)
* [dataUriToString](#datauritostring)
* [empty](#empty)
* [endsWith](#endswith)
* [頭](#first)
* [format](#format)
* [guid](#guid)
* [indexOf](#indexof)
* [次](#last)
* [lastIndexOf](#lastindexof)
* [length](#length)
* [newGuid](#newguid)
* [padLeft](#padleft)
* [replace](#replace)
* [過](#skip)
* [平分](#split)
* [startsWith](#startswith)
* [string](#string)
* [子字串](#substring)
* [採取](#take)
* [toLower](#tolower)
* [toUpper](#toupper)
* [物](#trim)
* [uniqueString](#uniquestring)
* [uri](#uri)
* [uriComponent](#uricomponent)
* [uriComponentToString](#uricomponenttostring)

## <a name="base64"></a>base64

`base64(inputString)`

傳回輸入字串的 base64 表示法。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| inputString |是 |字串 |要以 base64 表示法傳回的值。 |

### <a name="return-value"></a>傳回值

字串，包含 base64 表示法。

### <a name="examples"></a>範例

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json)顯示如何使用 base64 函式。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringData": {
            "type": "string",
            "defaultValue": "one, two, three"
        },
        "jsonFormattedData": {
            "type": "string",
            "defaultValue": "{'one': 'a', 'two': 'b'}"
        }
    },
    "variables": {
        "base64String": "[base64(parameters('stringData'))]",
        "base64Object": "[base64(parameters('jsonFormattedData'))]"
    },
    "resources": [
    ],
    "outputs": {
        "base64Output": {
            "type": "string",
            "value": "[variables('base64String')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[base64ToString(variables('base64String'))]"
        },
        "toJsonOutput": {
            "type": "object",
            "value": "[base64ToJson(variables('base64Object'))]"
        }
    }
}
```

上述範例中具有預設值的輸出如下：

| 名稱 | 類型 | 值 |
| ---- | ---- | ----- |
| base64Output | 字串 | b25lLCB0d28sIHRocmVl |
| toStringOutput | 字串 | one, two, three |
| toJsonOutput | Object | {"one": "a", "two": "b"} |

## <a name="base64tojson"></a>base64ToJson

`base64tojson`

將 base64 表示法轉換為 JSON 物件。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| base64Value |是 |字串 |要轉換為 JSON 物件的 base64 表示法。 |

### <a name="return-value"></a>傳回值

JSON 物件。

### <a name="examples"></a>範例

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json)會使用 base64ToJson 函式來轉換 base64 值︰

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringData": {
            "type": "string",
            "defaultValue": "one, two, three"
        },
        "jsonFormattedData": {
            "type": "string",
            "defaultValue": "{'one': 'a', 'two': 'b'}"
        }
    },
    "variables": {
        "base64String": "[base64(parameters('stringData'))]",
        "base64Object": "[base64(parameters('jsonFormattedData'))]"
    },
    "resources": [
    ],
    "outputs": {
        "base64Output": {
            "type": "string",
            "value": "[variables('base64String')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[base64ToString(variables('base64String'))]"
        },
        "toJsonOutput": {
            "type": "object",
            "value": "[base64ToJson(variables('base64Object'))]"
        }
    }
}
```

上述範例中具有預設值的輸出如下：

| 名稱 | 類型 | 值 |
| ---- | ---- | ----- |
| base64Output | 字串 | b25lLCB0d28sIHRocmVl |
| toStringOutput | 字串 | one, two, three |
| toJsonOutput | Object | {"one": "a", "two": "b"} |

## <a name="base64tostring"></a>base64ToString

`base64ToString(base64Value)`

將 base64 表示法轉換為字串。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| base64Value |是 |字串 |要轉換為字串的 base64 表示法。 |

### <a name="return-value"></a>傳回值

轉換後之 base64 值的字串。

### <a name="examples"></a>範例

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json)會使用 base64ToString 函式來轉換 base64 值︰

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringData": {
            "type": "string",
            "defaultValue": "one, two, three"
        },
        "jsonFormattedData": {
            "type": "string",
            "defaultValue": "{'one': 'a', 'two': 'b'}"
        }
    },
    "variables": {
        "base64String": "[base64(parameters('stringData'))]",
        "base64Object": "[base64(parameters('jsonFormattedData'))]"
    },
    "resources": [
    ],
    "outputs": {
        "base64Output": {
            "type": "string",
            "value": "[variables('base64String')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[base64ToString(variables('base64String'))]"
        },
        "toJsonOutput": {
            "type": "object",
            "value": "[base64ToJson(variables('base64Object'))]"
        }
    }
}
```

上述範例中具有預設值的輸出如下：

| 名稱 | 類型 | 值 |
| ---- | ---- | ----- |
| base64Output | 字串 | b25lLCB0d28sIHRocmVl |
| toStringOutput | 字串 | one, two, three |
| toJsonOutput | Object | {"one": "a", "two": "b"} |

## <a name="concat"></a>concat

`concat (arg1, arg2, arg3, ...)`

結合多個字串值並傳回串連字串，或結合多個陣列並傳回串連陣列。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |字串或陣列 |要串連的第一個字串或陣列。 |
| 其他引數 |否 |字串或陣列 |以串連順序排列的其他字串或陣列。 |

此函式可以接受任意數目的引數，並且可針對參數接受字串或陣列。 不過，您無法同時為參數提供陣列和字串。 字串只會與其他字串串連。

### <a name="return-value"></a>傳回值

串連值的字串或陣列。

### <a name="examples"></a>範例

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-string.json)顯示如何結合兩個字串值並傳回串連字串。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "prefix": {
            "type": "string",
            "defaultValue": "prefix"
        }
    },
    "resources": [],
    "outputs": {
        "concatOutput": {
            "value": "[concat(parameters('prefix'), '-', uniqueString(resourceGroup().id))]",
            "type" : "string"
        }
    }
}
```

上述範例中具有預設值的輸出如下：

| 名稱 | 類型 | 值 |
| ---- | ---- | ----- |
| concatOutput | 字串 | prefix-5yj4yjf5mbg72 |

下一個[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-array.json)顯示如何結合兩個陣列。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": { 
        "firstArray": { 
            "type": "array", 
            "defaultValue": [ 
                "1-1", 
                "1-2", 
                "1-3" 
            ] 
        },
        "secondArray": {
            "type": "array", 
            "defaultValue": [ 
                "2-1", 
                "2-2",
                "2-3" 
            ] 
        }
    },
    "resources": [
    ],
    "outputs": {
        "return": {
            "type": "array",
            "value": "[concat(parameters('firstArray'), parameters('secondArray'))]"
        }
    }
}
```

上述範例中具有預設值的輸出如下：

| 名稱 | 類型 | 值 |
| ---- | ---- | ----- |
| return | Array | ["1-1", "1-2", "1-3", "2-1", "2-2", "2-3"] |

## <a name="contains"></a>contains

`contains (container, itemToFind)`

檢查陣列中是否包含值、物件中是否包含索引鍵，或字串中是否包含子字串。 字串比較會區分大小寫。 不過，測試時，如果物件包含索引鍵，比較便不區分大小寫。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| 容器 |是 |陣列、物件或字串 |其中包含要尋找之值的值。 |
| itemToFind |是 |字串或整數 |要尋找的值。 |

### <a name="return-value"></a>傳回值

找到項目則傳回 **True**，否則會傳回 **False**。

### <a name="examples"></a>範例

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/contains.json)顯示如何使用不同類型的 contains：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

## <a name="datauri"></a>dataUri

`dataUri(stringToConvert)`

將值轉換為資料 URI。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| stringToConvert |是 |字串 |要轉換為資料 URI 的值。 |

### <a name="return-value"></a>傳回值

格式化為資料 URI 的字串。

### <a name="examples"></a>範例

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json)會將值轉換為資料 URI，並將資料 URI 轉換為字串︰

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToTest": {
            "type": "string",
            "defaultValue": "Hello"
        },
        "dataFormattedString": {
            "type": "string",
            "defaultValue": "data:;base64,SGVsbG8sIFdvcmxkIQ=="
        }
    },
    "resources": [],
    "outputs": {
        "dataUriOutput": {
            "value": "[dataUri(parameters('stringToTest'))]",
            "type" : "string"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[dataUriToString(parameters('dataFormattedString'))]"
        }
    }
}
```

上述範例中具有預設值的輸出如下：

| 名稱 | 類型 | 值 |
| ---- | ---- | ----- |
| dataUriOutput | 字串 | data:text/plain;charset=utf8;base64,SGVsbG8= |
| toStringOutput | 字串 | Hello, World! |

## <a name="datauritostring"></a>dataUriToString

`dataUriToString(dataUriToConvert)`

將資料 URI 格式化值轉換為字串。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| dataUriToConvert |是 |字串 |要轉換的資料 URI 值。 |

### <a name="return-value"></a>傳回值

字串，包含已轉換的值。

### <a name="examples"></a>範例

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json)會將值轉換為資料 URI，並將資料 URI 轉換為字串︰

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToTest": {
            "type": "string",
            "defaultValue": "Hello"
        },
        "dataFormattedString": {
            "type": "string",
            "defaultValue": "data:;base64,SGVsbG8sIFdvcmxkIQ=="
        }
    },
    "resources": [],
    "outputs": {
        "dataUriOutput": {
            "value": "[dataUri(parameters('stringToTest'))]",
            "type" : "string"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[dataUriToString(parameters('dataFormattedString'))]"
        }
    }
}
```

上述範例中具有預設值的輸出如下：

| 名稱 | 類型 | 值 |
| ---- | ---- | ----- |
| dataUriOutput | 字串 | data:text/plain;charset=utf8;base64,SGVsbG8= |
| toStringOutput | 字串 | Hello, World! |

## <a name="empty"></a>empty

`empty(itemToTest)`

判斷陣列、物件或字串是否空白。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| itemToTest |是 |陣列、物件或字串 |要檢查其是否為空白的值。 |

### <a name="return-value"></a>傳回值

如果值空白則傳回 **True**，否則會傳回 **False**。

### <a name="examples"></a>範例

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/empty.json)會檢查陣列、物件和字串是否空白。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

## <a name="endswith"></a>endsWith

`endsWith(stringToSearch, stringToFind)`

判斷字串結尾是否為值。 此比較不區分大小寫。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| stringToSearch |是 |字串 |其中包含要尋找之項目的值。 |
| stringToFind |是 |字串 |要尋找的值。 |

### <a name="return-value"></a>傳回值

如果最後一個字元或字串字元與該值相符，則傳回 **True**；否則會傳回 **False**。

### <a name="examples"></a>範例

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json)顯示如何使用 startsWith 和 endsWith 函式：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "startsTrue": {
            "value": "[startsWith('abcdef', 'ab')]",
            "type" : "bool"
        },
        "startsCapTrue": {
            "value": "[startsWith('abcdef', 'A')]",
            "type" : "bool"
        },
        "startsFalse": {
            "value": "[startsWith('abcdef', 'e')]",
            "type" : "bool"
        },
        "endsTrue": {
            "value": "[endsWith('abcdef', 'ef')]",
            "type" : "bool"
        },
        "endsCapTrue": {
            "value": "[endsWith('abcdef', 'F')]",
            "type" : "bool"
        },
        "endsFalse": {
            "value": "[endsWith('abcdef', 'e')]",
            "type" : "bool"
        }
    }
}
```

上述範例中具有預設值的輸出如下：

| 名稱 | 類型 | 值 |
| ---- | ---- | ----- |
| startsTrue | Bool | True |
| startsCapTrue | Bool | True |
| startsFalse | Bool | False |
| endsTrue | Bool | True |
| endsCapTrue | Bool | True |
| endsFalse | Bool | False |

## <a name="first"></a>first

`first(arg1)`

傳回字串的第一個字元或陣列的第一個元素。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |陣列或字串 |要擷取其第一個元素或字元的值。 |

### <a name="return-value"></a>傳回值

陣列中第一個字元的字串或第一個元素的類型 (字串、整數、陣列或物件)。

### <a name="examples"></a>範例

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/first.json)顯示如何搭配使用 first 函式與陣列和字串。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "arrayOutput": {
            "type": "string",
            "value": "[first(parameters('arrayToTest'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[first('One Two Three')]"
        }
    }
}
```

上述範例中具有預設值的輸出如下：

| 名稱 | 類型 | 值 |
| ---- | ---- | ----- |
| arrayOutput | 字串 | one |
| stringOutput | 字串 | O |

## <a name="format"></a>format

`format(formatString, arg1, arg2, ...)`

從輸入值建立格式化的字串。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| formatString | 是 | 字串 | 複合格式字串。 |
| arg1 | 是 | 字串、整數或布林值 | 要包含在格式化字串中的值。 |
| 其他引數 | 否 | 字串、整數或布林值 | 要包含在格式化字串中的其他值。 |

### <a name="remarks"></a>備註

使用此函式來格式化範本中的字串。 它會使用與 .NET 中的[System.string 格式](/dotnet/api/system.string.format)相同的格式選項。

### <a name="examples"></a>範例

下列範例範本顯示如何使用 format 函數。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "greeting": {
            "type": "string",
            "defaultValue": "Hello"
        },
        "name": {
            "type": "string",
            "defaultValue": "User"
        },
        "numberToFormat": {
            "type": "int",
            "defaultValue": 8175133
        }
    },
    "resources": [
    ],
    "outputs": {
        "formatTest": {
            "type": "string",
            "value": "[format('{0}, {1}. Formatted number: {2:N0}', parameters('greeting'), parameters('name'), parameters('numberToFormat'))]"
        }
    }
}
```

上述範例中具有預設值的輸出如下：

| 名稱 | 類型 | 值 |
| ---- | ---- | ----- |
| formatTest | 字串 | Hello，User。 格式化的數位：8175133 |

## <a name="guid"></a>guid

`guid(baseString, ...)`

建立一個值，格式為根據提供作為參數之值的全域唯一識別碼。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| baseString |是 |字串 |雜湊函式中用來建立 GUID 的值。 |
| 視需要，也會使用其他參數 |否 |字串 |您可以視需要新增多個字串，來建立指定唯一性層級的值。 |

### <a name="remarks"></a>備註

當您需要建立格式為全域唯一識別碼的值時，此函式很有幫助。 您提供限制結果唯一性範圍的參數值。 您可以指定名稱對於訂用帳戶、資源群組或部署是否唯一。

傳回的值不是隨機字串，而是參數上雜湊函數的結果。 傳回的值為 36 個字元長。 這不是全域唯一的。 若要建立不是以參數的雜湊值為基礎的新 GUID，請使用[newGuid](#newguid)函數。

下列範例顯示如何使用 guid 來建立常用層級的唯一值。

在訂用帳戶範圍內是唯一

```json
"[guid(subscription().subscriptionId)]"
```

在資源群組範圍內是唯一

```json
"[guid(resourceGroup().id)]"
```

在資源群組的部署範圍內是唯一

```json
"[guid(resourceGroup().id, deployment().name)]"
```

### <a name="return-value"></a>傳回值

字串，包含 36 個字元，格式為全域唯一識別碼。

### <a name="examples"></a>範例

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/guid.json)會從 guid 傳回結果：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [],
    "outputs": {
        "guidPerSubscription": {
            "value": "[guid(subscription().subscriptionId)]",
            "type": "string"
        },
        "guidPerResourceGroup": {
            "value": "[guid(resourceGroup().id)]",
            "type": "string"
        },
        "guidPerDeployment": {
            "value": "[guid(resourceGroup().id, deployment().name)]",
            "type": "string"
        }
    }
}
```

## <a name="indexof"></a>indexOf

`indexOf(stringToSearch, stringToFind)`

傳回值在字串內的第一個位置。 此比較不區分大小寫。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| stringToSearch |是 |字串 |其中包含要尋找之項目的值。 |
| stringToFind |是 |字串 |要尋找的值。 |

### <a name="return-value"></a>傳回值

整數，代表要尋找之項目的位置。 此值是以零為起始。 如果找不到專案，則會傳回-1。

### <a name="examples"></a>範例

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json)顯示如何使用 indexOf 和 lastIndexOf 函式：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "firstT": {
            "value": "[indexOf('test', 't')]",
            "type" : "int"
        },
        "lastT": {
            "value": "[lastIndexOf('test', 't')]",
            "type" : "int"
        },
        "firstString": {
            "value": "[indexOf('abcdef', 'CD')]",
            "type" : "int"
        },
        "lastString": {
            "value": "[lastIndexOf('abcdef', 'AB')]",
            "type" : "int"
        },
        "notFound": {
            "value": "[indexOf('abcdef', 'z')]",
            "type" : "int"
        }
    }
}
```

上述範例中具有預設值的輸出如下：

| 名稱 | 類型 | 值 |
| ---- | ---- | ----- |
| firstT | Int | 0 |
| lastT | Int | 3 |
| firstString | Int | 2 |
| lastString | Int | 0 |
| notFound | Int | -1 |

## <a name="last"></a>last

`last (arg1)`

傳回字串的最後一個字元或陣列的最後一個元素。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |陣列或字串 |要擷取其最後一個元素或字元的值。 |

### <a name="return-value"></a>傳回值

陣列中最後一個字元的字串，或最後一個元素的類型 (字串、整數、陣列或物件)。

### <a name="examples"></a>範例

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/last.json)顯示如何搭配使用 last 函式與陣列和字串。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "arrayOutput": {
            "type": "string",
            "value": "[last(parameters('arrayToTest'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[last('One Two Three')]"
        }
    }
}
```

上述範例中具有預設值的輸出如下：

| 名稱 | 類型 | 值 |
| ---- | ---- | ----- |
| arrayOutput | 字串 | three |
| stringOutput | 字串 | e |

## <a name="lastindexof"></a>lastIndexOf

`lastIndexOf(stringToSearch, stringToFind)`

傳回值在字串內的最後一個位置。 此比較不區分大小寫。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| stringToSearch |是 |字串 |其中包含要尋找之項目的值。 |
| stringToFind |是 |字串 |要尋找的值。 |

### <a name="return-value"></a>傳回值

整數，代表要尋找之項目的最後一個位置。 此值是以零為起始。 如果找不到專案，則會傳回-1。

### <a name="examples"></a>範例

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json)顯示如何使用 indexOf 和 lastIndexOf 函式：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "firstT": {
            "value": "[indexOf('test', 't')]",
            "type" : "int"
        },
        "lastT": {
            "value": "[lastIndexOf('test', 't')]",
            "type" : "int"
        },
        "firstString": {
            "value": "[indexOf('abcdef', 'CD')]",
            "type" : "int"
        },
        "lastString": {
            "value": "[lastIndexOf('abcdef', 'AB')]",
            "type" : "int"
        },
        "notFound": {
            "value": "[indexOf('abcdef', 'z')]",
            "type" : "int"
        }
    }
}
```

上述範例中具有預設值的輸出如下：

| 名稱 | 類型 | 值 |
| ---- | ---- | ----- |
| firstT | Int | 0 |
| lastT | Int | 3 |
| firstString | Int | 2 |
| lastString | Int | 0 |
| notFound | Int | -1 |

## <a name="length"></a>長度

`length(string)`

傳回字串中的字元數、陣列中的元素，或物件中的根層級屬性。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |陣列、字串或物件 |要用來取得元素數目的陣列、用來取得字元數的字串，或用來取得根層級屬性數目的物件。 |

### <a name="return-value"></a>傳回值

整數。 

### <a name="examples"></a>範例

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/length.json)顯示如何搭配使用 length 與陣列和字串：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

## <a name="newguid"></a>newGuid

`newGuid()`

傳回格式為全域唯一識別碼的值。 **這個函數只能用於參數的預設值。**

### <a name="remarks"></a>備註

您只能針對參數的預設值，在運算式中使用這個函數。 在範本中的任何其他地方使用此函數會傳回錯誤。 函式在範本的其他部分中不允許，因為它會在每次呼叫時傳回不同的值。 使用相同的參數部署相同的範本，並不會可靠地產生相同的結果。

NewGuid 函數與[guid](#guid)函數不同，因為它不接受任何參數。 當您呼叫具有相同參數的 guid 時，每次都會傳回相同的識別碼。 當您需要針對特定環境可靠地產生相同的 GUID 時，請使用 guid。 當您每次需要不同的識別碼（例如將資源部署到測試環境）時，請使用 newGuid。

NewGuid 函數會使用 .NET Framework 中的[Guid 結構](/dotnet/api/system.guid)來產生全域唯一識別碼。

如果您使用[選項來重新部署先前成功的部署](rollback-on-error.md)，而先前的部署包含使用 newGuid 的參數，則不會重新評估參數。 相反地，先前部署的參數值會自動在復原部署中重複使用。

在測試環境中，您可能需要在短時間內重複部署只存留的資源。 您可以使用 newGuid 搭配[uniqueString](#uniquestring)來建立唯一的名稱，而不是建立唯一的名稱。

請小心重新部署依賴 newGuid 函數的範本，以取得預設值。 當您重新部署，但未提供參數的值時，會重新評估函式。 如果您想要更新現有的資源，而不是建立新的資源，請傳入先前部署的參數值。

### <a name="return-value"></a>傳回值

字串，包含 36 個字元，格式為全域唯一識別碼。

### <a name="examples"></a>範例

下列範例範本顯示具有新識別碼的參數。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "guidValue": {
            "type": "string",
            "defaultValue": "[newGuid()]"
        }
    },
    "resources": [
    ],
    "outputs": {
        "guidOutput": {
            "type": "string",
            "value": "[parameters('guidValue')]"
        }
    }
}
```

上述範例的輸出會因每個部署而有所不同，但會類似于：

| 名稱 | 類型 | 值 |
| ---- | ---- | ----- |
| guidOutput | 字串 | b76a51fc-bd72-4a77-b9a2-3c29e7d2e551 |

下列範例會使用 newGuid 函數來建立儲存體帳戶的唯一名稱。 此範本可能適用于儲存體帳戶存在短時間且未重新部署的測試環境。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "guidValue": {
            "type": "string",
            "defaultValue": "[newGuid()]"
        }
    },
    "variables": {
        "storageName": "[concat('storage', uniqueString(parameters('guidValue')))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageName')]",
            "location": "West US",
            "apiVersion": "2018-07-01",
            "sku":{
                "name": "Standard_LRS"
            },
            "kind": "StorageV2",
            "properties": {}
        }
    ],
    "outputs": {
        "nameOutput": {
            "type": "string",
            "value": "[variables('storageName')]"
        }
    }
}
```

上述範例的輸出會因每個部署而有所不同，但會類似于：

| 名稱 | 類型 | 值 |
| ---- | ---- | ----- |
| nameOutput | 字串 | storagenziwvyru7uxie |


## <a name="padleft"></a>padLeft

`padLeft(valueToPad, totalLength, paddingCharacter)`

藉由將字元新增至左邊，直到到達指定的總長度，以傳回靠右對齊的字串。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| valueToPad |是 |字串或整數 |要靠右對齊的值。 |
| totalLength |是 |int |傳回字串中的字元總數。 |
| paddingCharacter |否 |單一字元 |要用於左側填補直到達到總長度的字元。 預設值是空格。 |

如果原始字串長度超過要填補的字元數，則不會新增任何字元。

### <a name="return-value"></a>傳回值

至少含有指定字元數的字串。

### <a name="examples"></a>範例

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/padleft.json)顯示如何藉由新增「零」字元直到符合字元總數，以填補使用者提供的參數值。 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "123"
        }
    },
    "resources": [],
    "outputs": {
        "stringOutput": {
            "type": "string",
            "value": "[padLeft(parameters('testString'),10,'0')]"
        }
    }
}
```

上述範例中具有預設值的輸出如下：

| 名稱 | 類型 | 值 |
| ---- | ---- | ----- |
| stringOutput | 字串 | 0000000123 |

## <a name="replace"></a>取代

`replace(originalString, oldString, newString)`

傳回具備由另一個字串取代的一個字串之所有執行個體的新字串。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| originalString |是 |字串 |具備由另一個字串取代的一個字串之所有執行個體的值。 |
| oldString |是 |字串 |要從原始字串中移除的字串。 |
| newString |是 |字串 |要新增來取代移除之字串的字串。 |

### <a name="return-value"></a>傳回值

具有已取代字元的字串。

### <a name="examples"></a>範例

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/replace.json)會示範如何從使用者提供的字串中將所有的連字號移除，以及如何使用另一個字串來取代一部分的字串。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "123-123-1234"
        }
    },
    "resources": [],
    "outputs": {
        "firstOutput": {
            "type": "string",
            "value": "[replace(parameters('testString'),'-', '')]"
        },
        "secondOutput": {
            "type": "string",
            "value": "[replace(parameters('testString'),'1234', 'xxxx')]"
        }
    }
}
```

上述範例中具有預設值的輸出如下：

| 名稱 | 類型 | 值 |
| ---- | ---- | ----- |
| firstOutput | 字串 | 1231231234 |
| secondOutput | 字串 | 123-123-xxxx |

## <a name="skip"></a>skip

`skip(originalValue, numberToSkip)`

傳回位於指定字元數目之後的所有字元所組成的字串，或傳回位於指定元素數目之後的所有元素所形成的陣列。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| originalValue |是 |陣列或字串 |要用於略過的陣列或字串。 |
| numberToSkip |是 |int |要略過的元素或字元數。 如果此值為 0 或更小的值，則會傳回值內的所有元素或字元。 如果大於陣列或字串的長度，則會傳回空陣列或字串。 |

### <a name="return-value"></a>傳回值

陣列或字串。

### <a name="examples"></a>範例

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/skip.json)會略過陣列中指定的元素數目，以及字串中指定的字元數。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testArray": {
            "type": "array",
            "defaultValue": [
                "one",
                "two",
                "three"
            ]
        },
        "elementsToSkip": {
            "type": "int",
            "defaultValue": 2
        },
        "testString": {
            "type": "string",
            "defaultValue": "one two three"
        },
        "charactersToSkip": {
            "type": "int",
            "defaultValue": 4
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "array",
            "value": "[skip(parameters('testArray'),parameters('elementsToSkip'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[skip(parameters('testString'),parameters('charactersToSkip'))]"
        }
    }
}
```

上述範例中具有預設值的輸出如下：

| 名稱 | 類型 | 值 |
| ---- | ---- | ----- |
| arrayOutput | Array | ["three"] |
| stringOutput | 字串 | two three |

## <a name="split"></a>split

`split(inputString, delimiter)`

傳回包含輸入字串之子字串的字串陣列，其中的子字串已使用指定的分隔符號分隔。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| inputString |是 |字串 |要分割的字串。 |
| 分隔符號 |是 |字串或字串陣列 |用於分割字串的分隔符號。 |

### <a name="return-value"></a>傳回值

字串的陣列。

### <a name="examples"></a>範例

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/split.json)會分割輸入字串，所使用的分割字元為逗號或分號。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstString": {
            "type": "string",
            "defaultValue": "one,two,three"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "one;two,three"
        }
    },
    "variables": {
        "delimiters": [ ",", ";" ]
    },
    "resources": [],
    "outputs": {
        "firstOutput": {
            "type": "array",
            "value": "[split(parameters('firstString'),',')]"
        },
        "secondOutput": {
            "type": "array",
            "value": "[split(parameters('secondString'),variables('delimiters'))]"
        }
    }
}
```

上述範例中具有預設值的輸出如下：

| 名稱 | 類型 | 值 |
| ---- | ---- | ----- |
| firstOutput | Array | ["one", "two", "three"] |
| secondOutput | Array | ["one", "two", "three"] |

## <a name="startswith"></a>startsWith

`startsWith(stringToSearch, stringToFind)`

判斷字串開頭是否為值。 此比較不區分大小寫。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| stringToSearch |是 |字串 |其中包含要尋找之項目的值。 |
| stringToFind |是 |字串 |要尋找的值。 |

### <a name="return-value"></a>傳回值

如果第一個字元或字串字元與該值相符，則傳回 **True**；否則傳回 **False**。

### <a name="examples"></a>範例

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json)顯示如何使用 startsWith 和 endsWith 函式：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "startsTrue": {
            "value": "[startsWith('abcdef', 'ab')]",
            "type" : "bool"
        },
        "startsCapTrue": {
            "value": "[startsWith('abcdef', 'A')]",
            "type" : "bool"
        },
        "startsFalse": {
            "value": "[startsWith('abcdef', 'e')]",
            "type" : "bool"
        },
        "endsTrue": {
            "value": "[endsWith('abcdef', 'ef')]",
            "type" : "bool"
        },
        "endsCapTrue": {
            "value": "[endsWith('abcdef', 'F')]",
            "type" : "bool"
        },
        "endsFalse": {
            "value": "[endsWith('abcdef', 'e')]",
            "type" : "bool"
        }
    }
}
```

上述範例中具有預設值的輸出如下：

| 名稱 | 類型 | 值 |
| ---- | ---- | ----- |
| startsTrue | Bool | True |
| startsCapTrue | Bool | True |
| startsFalse | Bool | False |
| endsTrue | Bool | True |
| endsCapTrue | Bool | True |
| endsFalse | Bool | False |

## <a name="string"></a>字串

`string(valueToConvert)`

將指定的值轉換成字串。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| valueToConvert |是 | 任意 |要轉換成字串的值。 任何類型的值均可轉換，包括物件和陣列。 |

### <a name="return-value"></a>傳回值

轉換值的字串。

### <a name="examples"></a>範例

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/string.json)顯示如何將不同類型的值轉換為字串︰

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testObject": {
            "type": "object",
            "defaultValue": {
                "valueA": 10,
                "valueB": "Example Text"
            }
        },
        "testArray": {
            "type": "array",
            "defaultValue": [
                "a",
                "b",
                "c"
            ]
        },
        "testInt": {
            "type": "int",
            "defaultValue": 5
        }
    },
    "resources": [],
    "outputs": {
        "objectOutput": {
            "type": "string",
            "value": "[string(parameters('testObject'))]"
        },
        "arrayOutput": {
            "type": "string",
            "value": "[string(parameters('testArray'))]"
        },
        "intOutput": {
            "type": "string",
            "value": "[string(parameters('testInt'))]"
        }
    }
}
```

上述範例中具有預設值的輸出如下：

| 名稱 | 類型 | 值 |
| ---- | ---- | ----- |
| objectOutput | 字串 | {"valueA":10,"valueB":"Example Text"} |
| arrayOutput | 字串 | ["a","b","c"] |
| intOutput | 字串 | 5 |

## <a name="substring"></a>substring

`substring(stringToParse, startIndex, length)`

傳回起始於指定字元位置的子字串，其中包含指定的字元數。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| stringToParse |是 |字串 |要用來擷取子字串的原始字串。 |
| startIndex |否 |int |起始字元位置為零的子字串。 |
| 長度 |否 |int |子字串的字元數。 必須參考字串內的位置。 必須是零或更大的值。 |

### <a name="return-value"></a>傳回值

子字串。 或著，如果長度為零，則為空字串。

### <a name="remarks"></a>備註

當子字串延伸超過字串的結尾，或當長度小於零時，此函式會失敗。 下列範例會失敗，並出現錯誤「索引與長度參數必須參考字串內的位置。 索引參數: '0'，長度參數: '11'，字串參數的長度: '10'。」。

```json
"parameters": {
    "inputString": { "type": "string", "value": "1234567890" }
},
"variables": { 
    "prefix": "[substring(parameters('inputString'), 0, 11)]"
}
```

### <a name="examples"></a>範例

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/substring.json)會從參數中擷取子字串。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "one two three"
        }
    },
    "resources": [],
    "outputs": {
        "substringOutput": {
            "value": "[substring(parameters('testString'), 4, 3)]",
            "type": "string"
        }
    }
}
```

上述範例中具有預設值的輸出如下：

| 名稱 | 類型 | 值 |
| ---- | ---- | ----- |
| substringOutput | 字串 | two |

## <a name="take"></a>take

`take(originalValue, numberToTake)`

傳回由字串開頭的指定字元數目所形成的字串，或傳回由陣列開頭的指定元素數目所組成的陣列。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| originalValue |是 |陣列或字串 |要從其中擷取元素的陣列或字串。 |
| numberToTake |是 |int |要擷取的元素或字元數。 如果此值為 0 或更小的值，則會傳回空白陣列或字串。 如果大於給定陣列或字串的長度，則會傳回陣列或字串中的所有元素。 |

### <a name="return-value"></a>傳回值

陣列或字串。

### <a name="examples"></a>範例

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/take.json)會從陣列中取得指定的元素數目，以及從字串中取得指定的字元數目。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testArray": {
            "type": "array",
            "defaultValue": [
                "one",
                "two",
                "three"
            ]
        },
        "elementsToTake": {
            "type": "int",
            "defaultValue": 2
        },
        "testString": {
            "type": "string",
            "defaultValue": "one two three"
        },
        "charactersToTake": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "array",
            "value": "[take(parameters('testArray'),parameters('elementsToTake'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[take(parameters('testString'),parameters('charactersToTake'))]"
        }
    }
}
```

上述範例中具有預設值的輸出如下：

| 名稱 | 類型 | 值 |
| ---- | ---- | ----- |
| arrayOutput | Array | ["one", "two"] |
| stringOutput | 字串 | on |

## <a name="tolower"></a>toLower

`toLower(stringToChange)`

將指定的字串轉換為小寫。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| stringToChange |是 |字串 |要轉換成小寫字母的值。 |

### <a name="return-value"></a>傳回值

字串已轉換成小寫。

### <a name="examples"></a>範例

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json)會將參數值轉換為小寫和大寫。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "One Two Three"
        }
    },
    "resources": [],
    "outputs": {
        "toLowerOutput": {
            "value": "[toLower(parameters('testString'))]",
            "type": "string"
        },
        "toUpperOutput": {
            "type": "string",
            "value": "[toUpper(parameters('testString'))]"
        }
    }
}
```

上述範例中具有預設值的輸出如下：

| 名稱 | 類型 | 值 |
| ---- | ---- | ----- |
| toLowerOutput | 字串 | one two three |
| toUpperOutput | 字串 | ONE TWO THREE |

## <a name="toupper"></a>toUpper

`toUpper(stringToChange)`

將指定的字串轉換為大寫。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| stringToChange |是 |字串 |要轉換成大寫字母的值。 |

### <a name="return-value"></a>傳回值

字串已轉換成大寫。

### <a name="examples"></a>範例

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json)會將參數值轉換為小寫和大寫。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "One Two Three"
        }
    },
    "resources": [],
    "outputs": {
        "toLowerOutput": {
            "value": "[toLower(parameters('testString'))]",
            "type": "string"
        },
        "toUpperOutput": {
            "type": "string",
            "value": "[toUpper(parameters('testString'))]"
        }
    }
}
```

上述範例中具有預設值的輸出如下：

| 名稱 | 類型 | 值 |
| ---- | ---- | ----- |
| toLowerOutput | 字串 | one two three |
| toUpperOutput | 字串 | ONE TWO THREE |

## <a name="trim"></a>修剪

`trim (stringToTrim)`

從指定的字串中移除所有開頭和尾端空白字元。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| stringToTrim |是 |字串 |要修剪的值。 |

### <a name="return-value"></a>傳回值

沒有開頭和尾端空白字元的字串。

### <a name="examples"></a>範例

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/trim.json)會修剪參數的空白字元。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "    one two three   "
        }
    },
    "resources": [],
    "outputs": {
        "return": {
            "type": "string",
            "value": "[trim(parameters('testString'))]"
        }
    }
}
```

上述範例中具有預設值的輸出如下：

| 名稱 | 類型 | 值 |
| ---- | ---- | ----- |
| return | 字串 | one two three |

## <a name="uniquestring"></a>uniqueString

`uniqueString (baseString, ...)`

根據當作參數提供的值，建立具決定性的雜湊字串。 

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| baseString |是 |字串 |雜湊函式中用來建立唯一字串的值。 |
| 視需要，也會使用其他參數 |否 |字串 |您可以視需要新增多個字串，來建立指定唯一性層級的值。 |

### <a name="remarks"></a>備註

當您需要建立資源的唯一名稱時，這個函式很有幫助。 您提供限制結果唯一性範圍的參數值。 您可以指定名稱對於訂用帳戶、資源群組或部署是否唯一。 

傳回的值不是隨機字串，而是雜湊函數的結果。 傳回的值為 13 個字元長。 這不是全域唯一的。 建議您將值與來自命名慣例的前置詞結合，建立有意義的名稱。 下列範例顯示傳回值的格式。 依提供的參數而改變的實際值。

    tcvhiyu5h2o5o

下列範例顯示如何使用 uniqueString 來建立常用層級的唯一值。

在訂用帳戶範圍內是唯一

```json
"[uniqueString(subscription().subscriptionId)]"
```

在資源群組範圍內是唯一

```json
"[uniqueString(resourceGroup().id)]"
```

在資源群組的部署範圍內是唯一

```json
"[uniqueString(resourceGroup().id, deployment().name)]"
```

下列範例顯示如何根據您的資源群組建立儲存體帳戶的唯一名稱。 在資源群組內，如果以相同的方式來建立，則名稱不是唯一的。

```json
"resources": [{ 
    "name": "[concat('storage', uniqueString(resourceGroup().id))]", 
    "type": "Microsoft.Storage/storageAccounts", 
    ...
```

如果您需要在每次部署範本時建立新的唯一名稱，而不想要更新資源，您可以使用[utcNow](template-functions-date.md#utcnow)函數搭配 uniqueString。 您可以在測試環境中使用這個方法。 如需範例，請參閱[utcNow](template-functions-date.md#utcnow)。

### <a name="return-value"></a>傳回值

包含 13 個字元的字串。

### <a name="examples"></a>範例

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uniquestring.json)會從 uniquestring 傳回結果：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "uniqueRG": {
            "value": "[uniqueString(resourceGroup().id)]",
            "type" : "string"
        },
        "uniqueDeploy": {
            "value": "[uniqueString(resourceGroup().id, deployment().name)]",
            "type" : "string"
        }
    }
}
```

## <a name="uri"></a>uri

`uri (baseUri, relativeUri)`

藉由結合 baseUri 和 relativeUri 字串建立絕對 URI。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| baseUri |是 |字串 |基底 uri 的字串。 請小心觀察有關處理尾端斜線（'/'）的行為，如下表所述。  |
| relativeUri |是 |字串 |要加入至基底 uri 字串的相對 uri 字串。 |

* 如果**baseuri**以尾端斜線結束，則結果只是**baseUri** ，後面接著**relativeUri**。

* 如果**baseUri**結尾不是兩個專案的尾端斜線，則會發生這種情況。  

   * 如果**baseUri**完全沒有任何斜線（除了前面的 "//" 之外），結果只是**baseUri** ，後面接著**relativeUri**。

   * 如果**baseUri**有一些斜線，但結尾不是斜線，則會從**baseuri**移除最後一個斜線的所有內容，而結果會是**baseuri** ，後面接著**relativeUri**。
     
以下是一些範例：

```
uri('http://contoso.org/firstpath', 'myscript.sh') -> http://contoso.org/myscript.sh
uri('http://contoso.org/firstpath/', 'myscript.sh') -> http://contoso.org/firstpath/myscript.sh
uri('http://contoso.org/firstpath/azuredeploy.json', 'myscript.sh') -> http://contoso.org/firstpath/myscript.sh
uri('http://contoso.org/firstpath/azuredeploy.json/', 'myscript.sh') -> http://contoso.org/firstpath/azuredeploy.json/myscript.sh
```
如需完整的詳細資訊，請依照[RFC 3986 第5節](https://tools.ietf.org/html/rfc3986#section-5)中的指定來解析**baseUri**和**relativeUri**參數。

### <a name="return-value"></a>傳回值

字串，代表基底和相對值的絕對 URI。

### <a name="examples"></a>範例

下列範例顯示如何根據上層範本的值建構巢狀範本的連結。

```json
"templateLink": "[uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')]"
```

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json)顯示如何使用 uri、uriComponent 和 uriComponentToString：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
        "uriEncoded": "[uriComponent(variables('uriFormat'))]" 
    },
    "resources": [
    ],
    "outputs": {
        "uriOutput": {
            "type": "string",
            "value": "[variables('uriFormat')]"
        },
        "componentOutput": {
            "type": "string",
            "value": "[variables('uriEncoded')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[uriComponentToString(variables('uriEncoded'))]"
        }
    }
}
```

上述範例中具有預設值的輸出如下：

| 名稱 | 類型 | 值 |
| ---- | ---- | ----- |
| uriOutput | 字串 | `http://contoso.com/resources/nested/azuredeploy.json` |
| componentOutput | 字串 | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | 字串 | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="uricomponent"></a>uriComponent

`uricomponent(stringToEncode)`

將 URI 編碼。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| stringToEncode |是 |字串 |要編碼的值。 |

### <a name="return-value"></a>傳回值

URI 編碼值的字串。

### <a name="examples"></a>範例

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json)顯示如何使用 uri、uriComponent 和 uriComponentToString：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
        "uriEncoded": "[uriComponent(variables('uriFormat'))]" 
    },
    "resources": [
    ],
    "outputs": {
        "uriOutput": {
            "type": "string",
            "value": "[variables('uriFormat')]"
        },
        "componentOutput": {
            "type": "string",
            "value": "[variables('uriEncoded')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[uriComponentToString(variables('uriEncoded'))]"
        }
    }
}
```

上述範例中具有預設值的輸出如下：

| 名稱 | 類型 | 值 |
| ---- | ---- | ----- |
| uriOutput | 字串 | `http://contoso.com/resources/nested/azuredeploy.json` |
| componentOutput | 字串 | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | 字串 | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="uricomponenttostring"></a>uriComponentToString

`uriComponentToString(uriEncodedString)`

傳回 URI 編碼值的字串。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| uriEncodedString |是 |字串 |要轉換為字串的 URI 編碼值。 |

### <a name="return-value"></a>傳回值

URI 編碼值的解碼字串。

### <a name="examples"></a>範例

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json)顯示如何使用 uri、uriComponent 和 uriComponentToString：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
        "uriEncoded": "[uriComponent(variables('uriFormat'))]" 
    },
    "resources": [
    ],
    "outputs": {
        "uriOutput": {
            "type": "string",
            "value": "[variables('uriFormat')]"
        },
        "componentOutput": {
            "type": "string",
            "value": "[variables('uriEncoded')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[uriComponentToString(variables('uriEncoded'))]"
        }
    }
}
```

上述範例中具有預設值的輸出如下：

| 名稱 | 類型 | 值 |
| ---- | ---- | ----- |
| uriOutput | 字串 | `http://contoso.com/resources/nested/azuredeploy.json` |
| componentOutput | 字串 | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | 字串 | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="next-steps"></a>後續步驟
* 如需 Azure Resource Manager 範本中各區段的說明，請參閱[編寫 Azure Resource Manager 範本](template-syntax.md)。
* 若要合併多個範本，請參閱搭配[使用連結的範本與 Azure Resource Manager](linked-templates.md)。
* 若要在建立資源類型時反復執行指定的次數，請參閱[在 Azure Resource Manager 中建立資源的多個實例](copy-resources.md)。
* 若要了解如何部署已建立的範本，請參閱[使用 Azure Resource Manager 範本部署應用程式](deploy-powershell.md)。

