---
title: 範本函式-邏輯
description: 描述 Azure Resource Manager 範本中用來決定邏輯值的函式。
ms.topic: conceptual
ms.date: 11/18/2020
ms.openlocfilehash: b54c104c8af5bb742b2c82d8a075515b8696501b
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2020
ms.locfileid: "96004546"
---
# <a name="logical-functions-for-arm-templates"></a>ARM 範本的邏輯函數

Resource Manager 提供數個函式，可在您的 Azure Resource Manager (ARM) 範本中進行比較。

* [and](#and)
* [bool](#bool)
* [false](#false)
* [if](#if)
* [not](#not)
* [or](#or)
* [true](#true)

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="and"></a>及

`and(arg1, arg2, ...)`

檢查所有參數值是否為 true。 `and`Bicep 中不支援此函數。 請改用 `&&` 運算子。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |boolean |要檢查是否為 true 的第一個值。 |
| arg2 |是 |boolean |要檢查是否為 true 的第二個值。 |
| 其他引數 |否 |boolean |要檢查是否為 true 的其他引數。 |

### <a name="return-value"></a>傳回值

如果所有值都是 true，則傳回 **True**，否則會傳回 **False**。

### <a name="examples"></a>範例

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json)會示範如何使用邏輯函式。

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "andExampleOutput": {
      "type": "bool",
      "value": "[and(bool('true'), bool('false'))]"
    },
    "orExampleOutput": {
      "type": "bool",
      "value": "[or(bool('true'), bool('false'))]"
    },
    "notExampleOutput": {
      "type": "bool",
      "value": "[not(bool('true'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output andExampleOutput bool = bool('true') && bool('false')
output orExampleOutput bool = bool('true') || bool('false')
output notExampleOutput bool = !(bool('true'))
```

---

前述範例的輸出為：

| 名稱 | 類型 | 值 |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | False |

## <a name="bool"></a>bool

`bool(arg1)`

將參數轉換為布林值。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |字串或整數 |要轉換為布林值的值。 |

### <a name="return-value"></a>傳回值

轉換值的布林值。

### <a name="remarks"></a>備註

您也可以使用 [true ( # B1 ](#true) 和 [False ( # B3 ](#false) 來取得布林值。

### <a name="examples"></a>範例

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/bool.json)顯示如何搭配使用 bool 與字串或整數。

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "trueString": {
      "type": "bool",
      "value": "[bool('true')]",
    },
    "falseString": {
      "type": "bool",
      "value": "[bool('false')]"
    },
    "trueInt": {
      "type": "bool",
      "value": "[bool(1)]"
    },
    "falseInt": {
      "type": "bool",
      "value": "[bool(0)]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output trueString bool = bool('true')
output falseString bool = bool('false')
output trueInt bool = bool(1)
output falseInt bool = bool(0)
```

---
上述範例中具有預設值的輸出如下：

| 名稱 | 類型 | 值 |
| ---- | ---- | ----- |
| trueString | Bool | True |
| falseString | Bool | False |
| trueInt | Bool | True |
| falseInt | Bool | False |

## <a name="false"></a>false

`false()`

傳回 false。 `false`函數無法在 Bicep 中使用。  請改用 `false` 關鍵字。

### <a name="parameters"></a>參數

False 函數不接受任何參數。

### <a name="return-value"></a>傳回值

一律為 false 的布林值。

### <a name="example"></a>範例

下列範例會傳回 false 的輸出值。

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "falseOutput": {
      "type": "bool",
      "value": "[false()]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output falseOutput bool = false
```

---

前述範例的輸出為：

| 名稱 | 類型 | 值 |
| ---- | ---- | ----- |
| falseOutput | Bool | False |

## <a name="if"></a>if

`if(condition, trueValue, falseValue)`

根據條件是 true 或 false 傳回值。 `if`Bicep 中不支援此函數。 請改用 `?:` 運算子。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| condition (條件) |是 |boolean |要檢查其是否為 true 或 false 的值。 |
| trueValue |是 | 字串、int、物件或陣列 |條件為 true 時，傳回的值。 |
| falseValue |是 | 字串、int、物件或陣列 |條件為 false 時，傳回的值。 |

### <a name="return-value"></a>傳回值

當第一個參數是 **True** 時，傳回第二個參數；否則會傳回第三個參數。

### <a name="remarks"></a>備註

當條件為 **true** 時，只會評估 true 值。 當條件為 **false** 時，只會評估 false 值。 使用 **if** 函式時，您可以包含只有有條件地有效的運算式。 例如，您可以參考存在於某個條件下但未在另一個條件下的資源。 下一節顯示有條件地評估運算式的範例。

### <a name="examples"></a>範例

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/if.json)會示範如何使用 `if` 函式。

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
  ],
  "outputs": {
    "yesOutput": {
      "type": "string",
      "value": "[if(equals('a', 'a'), 'yes', 'no')]"
    },
    "noOutput": {
      "type": "string",
      "value": "[if(equals('a', 'b'), 'yes', 'no')]"
    },
    "objectOutput": {
      "type": "object",
      "value": "[if(equals('a', 'a'), json('{\"test\": \"value1\"}'), json('null'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output yesOutput string = 'a' == 'a' ? 'yes' : 'no'
output noOutput string = 'a' == 'b' ? 'yes' : 'no'
output objectOutput object = 'a' == 'a' ? json('{"test": "value1"}') : json('null')
```

---

前述範例的輸出為：

| 名稱 | 類型 | 值 |
| ---- | ---- | ----- |
| yesOutput | String | 是 |
| noOutput | String | 否 |
| objectOutput | Object | { "test": "value1" } |

下列 [範例範本](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/conditionWithReference.json) 顯示如何使用此函式搭配僅有條件地有效的運算式。

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string"
    },
    "location": {
      "type": "string"
    },
    "logAnalytics": {
      "type": "string",
      "defaultValue": ""
    }
  },
  "resources": [
    {
      "condition": "[not(empty(parameters('logAnalytics')))]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "apiVersion": "2017-03-30",
      "name": "[concat(parameters('vmName'),'/omsOnboarding')]",
      "location": "[parameters('location')]",
      "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "workspaceId": "[if(not(empty(parameters('logAnalytics'))), reference(parameters('logAnalytics'), '2015-11-01-preview').customerId, json('null'))]"
        },
        "protectedSettings": {
          "workspaceKey": "[if(not(empty(parameters('logAnalytics'))), listKeys(parameters('logAnalytics'), '2015-11-01-preview').primarySharedKey, json('null'))]"
        }
      }
    }
  ],
  "outputs": {
    "mgmtStatus": {
      "type": "string",
      "value": "[if(not(empty(parameters('logAnalytics'))), 'Enabled monitoring for VM!', 'Nothing to enable')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

> [!NOTE]
> `Conditions` 尚未在 Bicep 中執行。 請參閱 [條件](https://github.com/Azure/bicep/issues/186)。

---

## <a name="not"></a>not

`not(arg1)`

將布林值轉換為其相反值。 `not`Bicep 中不支援此函數。 請改用 `!` 運算子。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |boolean |要進行轉換的值。 |

### <a name="return-value"></a>傳回值

當參數是 **False** 時，傳回 **True**。 當參數是 **True** 時，傳回 **False**。

### <a name="examples"></a>範例

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json)會示範如何使用邏輯函式。

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "andExampleOutput": {
      "type": "bool",
      "value": "[and(bool('true'), bool('false'))]",
    },
    "orExampleOutput": {
      "type": "bool",
      "value": "[or(bool('true'), bool('false'))]"
    },
    "notExampleOutput": {
      "type": "bool",
      "value": "[not(bool('true'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output andExampleOutput bool = bool('true') && bool('false')
output orExampleOutput bool = bool('true') || bool('false')
output notExampleOutput bool = !(bool('true'))
```

---

前述範例的輸出為：

| 名稱 | 類型 | 值 |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | False |

下列 [範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json)使用 **not** 搭配 [equals](template-functions-comparison.md#equals)。

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output checkNotEquals bool = !(1 == 2)
```

---

前述範例的輸出為：

| 名稱 | 類型 | 值 |
| ---- | ---- | ----- |
| checkNotEquals | Bool | True |

## <a name="or"></a>或

`or(arg1, arg2, ...)`

檢查任何參數值是否為 true。 `or`Bicep 中不支援此函數。 請改用 `||` 運算子。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |boolean |要檢查是否為 true 的第一個值。 |
| arg2 |是 |boolean |要檢查是否為 true 的第二個值。 |
| 其他引數 |否 |boolean |要檢查是否為 true 的其他引數。 |

### <a name="return-value"></a>傳回值

如果任何值為 true，傳回 **True**，否則會傳回 **False**。

### <a name="examples"></a>範例

下列[範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json)會示範如何使用邏輯函式。

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "andExampleOutput": {
      "value": "[and(bool('true'), bool('false'))]",
      "type": "bool"
    },
    "orExampleOutput": {
      "value": "[or(bool('true'), bool('false'))]",
      "type": "bool"
    },
    "notExampleOutput": {
      "value": "[not(bool('true'))]",
      "type": "bool"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output andExampleOutput bool = bool('true') && bool('false')
output orExampleOutput bool = bool('true') || bool('false')
output notExampleOutput bool = !(bool('true'))
```

---

前述範例的輸出為：

| 名稱 | 類型 | 值 |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | False |

## <a name="true"></a>true

`true()`

傳回 true。 `true`函數無法在 Bicep 中使用。  請改用 `true` 關鍵字。

### <a name="parameters"></a>參數

True 函數不接受任何參數。 `true`函數無法在 Bicep 中使用。  請改用 `true` 關鍵字。

### <a name="return-value"></a>傳回值

一律為 true 的布林值。

### <a name="example"></a>範例

下列範例會傳回真正的輸出值。

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "trueOutput": {
      "type": "bool",
      "value": "[true()]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output trueOutput bool = true
```

---

前述範例的輸出為：

| 名稱 | 類型 | 值 |
| ---- | ---- | ----- |
| trueOutput | Bool | True |

## <a name="next-steps"></a>後續步驟

* 如需 Azure Resource Manager 範本中各區段的說明，請參閱 [瞭解 ARM 範本的結構和語法](template-syntax.md)。
