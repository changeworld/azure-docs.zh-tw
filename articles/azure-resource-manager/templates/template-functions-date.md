---
title: 範本函式-日期
description: 描述在 Azure Resource Manager 範本中用來處理日期的函式。
ms.topic: conceptual
ms.date: 06/22/2020
ms.openlocfilehash: abdc88ce15279b90f8f9dc05a38a2ae236498f12
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86058039"
---
# <a name="date-functions-for-arm-templates"></a>ARM 範本的日期函數

Resource Manager 提供下列函式來處理 Azure Resource Manager (ARM) 範本中的日期：

* [dateTimeAdd](#datetimeadd)
* [utcNow](#utcnow)

## <a name="datetimeadd"></a>dateTimeAdd

`dateTimeAdd(base, duration, [format])`

將持續時間新增至基底值。 需要 ISO 8601 格式。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 說明 |
|:--- |:--- |:--- |:--- |
| base | 是 | 字串 | 加法的開始日期時間值。 使用 [ISO 8601 時間戳記格式](https://en.wikipedia.org/wiki/ISO_8601)。 |
| duration | 是 | 字串 | 要加入至基底的時間值。 它可以是負數值。 使用 [ISO 8601 持續時間格式](https://en.wikipedia.org/wiki/ISO_8601#Durations)。 |
| format | 否 | 字串 | 日期時間結果的輸出格式。 如果未提供，則會使用基底值的格式。 請使用 [標準格式字串](/dotnet/standard/base-types/standard-date-and-time-format-strings) 或 [自訂格式字串](/dotnet/standard/base-types/custom-date-and-time-format-strings)。 |

### <a name="return-value"></a>傳回值

將 duration 值新增至基底值所產生的日期時間值。

### <a name="examples"></a>範例

下列範例範本顯示新增時間值的不同方式。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters":{
        "baseTime":{
            "type":"string",
            "defaultValue": "[utcNow('u')]"
        }
    },
    "variables": {
        "add3Years": "[dateTimeAdd(parameters('baseTime'), 'P3Y')]",
        "subtract9Days": "[dateTimeAdd(parameters('baseTime'), '-P9D')]",
        "add1Hour": "[dateTimeAdd(parameters('baseTime'), 'PT1H')]"
    },
    "resources": [],
    "outputs": {
        "add3Years": {
            "value": "[variables('add3Years')]",
            "type": "string"
        },
        "subtract9Days": {
            "value": "[variables('subtract9Days')]",
            "type": "string"
        },
        "add1Hour": {
            "value": "[variables('add1Hour')]",
            "type": "string"
        },
    }
}
```

當先前的範本以的基本時間進行部署時 `2020-04-07 14:53:14Z` ，輸出為：

| 名稱 | 類型 | 值 |
| ---- | ---- | ----- |
| add3Years | String | 4/7/2023 2:53:14 PM |
| subtract9Days | String | 3/29/2020 2:53:14 PM |
| add1Hour | String | 4/7/2020 3:53:14 PM |

下一個範例範本顯示如何設定自動化排程的開始時間。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "omsAutomationAccountName": {
            "type": "string",
            "defaultValue": "demoAutomation",
            "metadata": {
                "description": "Use an existing Automation account."
            }
        },
        "scheduleName": {
            "type": "string",
            "defaultValue": "demoSchedule1",
            "metadata": {
                "description": "Name of the new schedule."
            }
        },
        "baseTime":{
            "type":"string",
            "defaultValue": "[utcNow('u')]",
            "metadata": {
                "description": "Schedule will start one hour from this time."
            }
        }
    },
    "variables": {
        "startTime": "[dateTimeAdd(parameters('baseTime'), 'PT1H')]"
    },
    "resources": [
        {
            "name": "[concat(parameters('omsAutomationAccountName'), '/', parameters('scheduleName'))]",
            "type": "microsoft.automation/automationAccounts/schedules",
            "apiVersion": "2015-10-31",
            "location": "eastus2",
            "tags": {
            },
            "properties": {
                "description": "Demo Scheduler",
                "startTime": "[variables('startTime')]",
                "isEnabled": "true",
                "interval": 1,
                "frequency": "hour"
            }
        }
    ],
    "outputs": {
    }
}
```

## <a name="utcnow"></a>utcNow

`utcNow(format)`

以指定的格式傳回目前的 (UTC) 日期時間值。 如果未提供格式，則會使用 ISO 8601 (yyyyMMddTHHmmssZ) 格式。 **此函數只能用於參數的預設值。**

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 說明 |
|:--- |:--- |:--- |:--- |
| format |否 |字串 |要轉換為字串的 URI 編碼值。 請使用 [標準格式字串](/dotnet/standard/base-types/standard-date-and-time-format-strings) 或 [自訂格式字串](/dotnet/standard/base-types/custom-date-and-time-format-strings)。 |

### <a name="remarks"></a>備註

您只能在運算式中使用此函式，以取得參數的預設值。 在範本中的其他地方使用此函式會傳回錯誤。 範本的其他部分不允許函數，因為它會在每次呼叫時傳回不同的值。 使用相同的參數部署相同的範本，並不會可靠地產生相同的結果。

如果您使用選項，將 [錯誤回復](rollback-on-error.md) 為先前成功的部署，而先前的部署包含使用 utcNow 的參數，則不會重新評估參數。 相反地，會在復原部署中自動重複使用先前部署的參數值。

請小心重新部署依賴 utcNow 函式作為預設值的範本。 當您重新部署但未提供參數的值時，會重新評估函式。 如果您想要更新現有的資源，而不是建立新的資源，請從先前的部署傳入參數值。

### <a name="return-value"></a>傳回值

目前的 UTC 日期時間值。

### <a name="examples"></a>範例

下列範例範本會顯示 datetime 值的不同格式。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcValue": {
            "type": "string",
            "defaultValue": "[utcNow()]"
        },
        "utcShortValue": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
        "utcCustomValue": {
            "type": "string",
            "defaultValue": "[utcNow('M d')]"
        }
    },
    "resources": [
    ],
    "outputs": {
        "utcOutput": {
            "type": "string",
            "value": "[parameters('utcValue')]"
        },
        "utcShortOutput": {
            "type": "string",
            "value": "[parameters('utcShortValue')]"
        },
        "utcCustomOutput": {
            "type": "string",
            "value": "[parameters('utcCustomValue')]"
        }
    }
}
```

上述範例的輸出會因每個部署而異，但如下所示：

| 名稱 | 類型 | 值 |
| ---- | ---- | ----- |
| utcOutput | 字串 | 20190305T175318Z |
| utcShortOutput | 字串 | 2019 年 3 月 5 日 |
| utcCustomOutput | 字串 | 3 5 |

下一個範例示範如何在設定標記值時使用函數中的值。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcShort": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
        "rgName": {
            "type": "string"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "name": "[parameters('rgName')]",
            "location": "westeurope",
            "tags":{
                "createdDate": "[parameters('utcShort')]"
            },
            "properties":{}
        }
    ],
    "outputs": {
        "utcShort": {
            "type": "string",
            "value": "[parameters('utcShort')]"
        }
    }
}
```

## <a name="next-steps"></a>接下來的步驟

* 如需 Azure Resource Manager 範本中各區段的說明，請參閱 [瞭解 ARM 範本的結構和語法](template-syntax.md)。
