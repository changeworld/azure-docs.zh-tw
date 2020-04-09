---
title: 樣本函數 - 日期
description: 描述要在 Azure 資源管理器樣本中使用用於日期的函數。
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 75234a142b9f2fbe61c337bfeb378b47534bac79
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986274"
---
# <a name="date-functions-for-arm-templates"></a>ARM 範本日期函數

資源管理員提供以下功能,用於在 Azure 資源管理員 (ARM) 樣本中處理日期:

* [日期時間新增](#datetimeadd)
* [utcNow](#utcnow)

## <a name="datetimeadd"></a>日期時間新增

`dateTimeAdd(base, duration, [format])`

將持續時間添加到基本日期時間值。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| base | 是 | 字串 | 添加的開始日期時間值。 使用[ISO 8601 時間戳格式](https://en.wikipedia.org/wiki/ISO_8601)。 |
| duration | 是 | 字串 | 要添加到基的值的時間值。 它可以是負值。 使用[ISO 8601 持續時間格式](https://en.wikipedia.org/wiki/ISO_8601#Durations)。 |
| format | 否 | 字串 | 日期時間結果的輸出格式。 如果未提供,則使用基值的格式。 使用[標準格式字串](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)或[自訂格式字串](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)。 |

### <a name="return-value"></a>傳回值

將工期值添加到基值時產生的日期時間值。

### <a name="examples"></a>範例

以下範例範本顯示了添加時間值的不同方法。

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

當使用 的基本`2020-04-07 14:53:14Z`時間 部署前面的樣本時,輸出為:

| 名稱 | 類型 | 值 |
| ---- | ---- | ----- |
| 新增3年 | String | 2023年4月7日 下午2:53:14 |
| 減去9天 | String | 2020年3月29日 下午2:53:14 |
| 新增1小時 | String | 2020年4月7日 下午3:53:14 |

下一個範例範本展示如何設置自動化計畫的開始時間。

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

以指定格式返回當前 (UTC) 日期時間值。 如果未提供格式,則使用 ISO 8601(yyyMMddTHHmmssZ)格式。 **此函數只能在參數的預設值中使用。**

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| format |否 |字串 |要轉換為字串的 URI 編碼值。 使用[標準格式字串](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)或[自訂格式字串](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)。 |

### <a name="remarks"></a>備註

只能在運算式中為參數的預設值使用此函數。 在範本中的其他地方使用此函數將返回錯誤。 範本的其他部分不允許函數,因為它每次調用時都會返回不同的值。 使用相同的參數部署同一範本不會可靠地生成相同的結果。

如果使用[選項重新部署較早的成功部署](rollback-on-error.md),並且早期部署包含使用 utcNow 的參數,則不會重新評估該參數。 相反,早期部署中的參數值將自動在回滾部署中重用。

請小心重新部署依賴於utcNow函數的預設值的範本。 重新部署並且不為參數提供值時,將重新評估該函數。 如果要更新現有資源而不是創建新資源,請從早期部署中傳遞參數值。

### <a name="return-value"></a>傳回值

當前 UTC 日期時間值。

### <a name="examples"></a>範例

以下範例範本顯示日期時間值的不同格式。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

上例中的輸出因每個部署而異,但類似於:

| 名稱 | 類型 | 值 |
| ---- | ---- | ----- |
| utc 輸出 | 字串 | 20190305T175318Z |
| utc短輸出 | 字串 | 2019 年 3 月 5 日 |
| utc 自訂輸出 | 字串 | 3 5 |

下一個範例展示如何在設置標記值時使用函數中的值。

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