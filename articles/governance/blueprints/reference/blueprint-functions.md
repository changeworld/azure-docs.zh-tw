---
title: Azure 藍圖函式
description: 描述可用於 Azure 藍圖定義和指派中藍圖成品的函式。
ms.date: 01/27/2021
ms.topic: reference
ms.openlocfilehash: 92cb906e87179073b7a69aa0bd4eab22c77087f9
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98919269"
---
# <a name="functions-for-use-with-azure-blueprints"></a>搭配 Azure 藍圖使用的函式

Azure 藍圖會提供讓藍圖定義更具動態性的函式。 這些函式可與藍圖定義和藍圖成品搭配使用。 Azure Resource Manager 範本 (ARM 範本) 成品除了透過藍圖參數取得動態值之外，還支援 Resource Manager 函式的完整使用。

支援下列函式：

- [artifacts](#artifacts)
- [concat](#concat)
- [parameters](#parameters)
- [resourceGroup](#resourcegroup)
- [resourceGroups](#resourcegroups)
- [訂用帳戶](#subscription)

## <a name="artifacts"></a>artifacts

`artifacts(artifactName)`

傳回已填入藍圖成品輸出的屬性物件。

> [!NOTE]
> `artifacts()`無法從 ARM 範本內部使用函數。 在使用 Azure PowerShell 或 REST API 作為 [藍圖即程式碼 (Blueprints-as-code)](https://github.com/Azure/azure-blueprints/blob/master/README.md) 的一部分來管理藍圖時，函式只能用於藍圖定義 JSON 或成品 JSON 中。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| artifactName |是 |字串 |藍圖成品的名稱。 |

### <a name="return-value"></a>傳回值

輸出屬性的物件。 **輸出** 屬性取決於所參考的藍圖成品類型。 所有類型都會遵循以下格式：

```json
{
  "outputs": {collectionOfOutputProperties}
}
```

#### <a name="policy-assignment-artifact"></a>原則指派成品

```json
{
    "outputs": {
        "policyAssignmentId": "{resourceId-of-policy-assignment}",
        "policyAssignmentName": "{name-of-policy-assignment}",
        "policyDefinitionId": "{resourceId-of-policy-definition}",
    }
}
```

#### <a name="arm-template-artifact"></a>ARM 範本成品

傳回物件的 **輸出** 屬性會定義在 ARM 範本內，並由部署所傳回。

#### <a name="role-assignment-artifact"></a>角色指派成品

```json
{
    "outputs": {
        "roleAssignmentId": "{resourceId-of-role-assignment}",
        "roleDefinitionId": "{resourceId-of-role-definition}",
        "principalId": "{principalId-role-is-being-assigned-to}",
    }
}
```

### <a name="example"></a>範例

具有識別碼 _myTemplateArtifact_ 的 ARM 範本成品，包含下列範例輸出屬性：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    ...
    "outputs": {
        "myArray": {
            "type": "array",
            "value": ["first", "second"]
        },
        "myString": {
            "type": "string",
            "value": "my string value"
        },
        "myObject": {
            "type": "object",
            "value": {
                "myProperty": "my value",
                "anotherProperty": true
            }
        }
    }
}
```

從 myTemplateArtifact 範例擷取資料的一些範例如下：

| 運算是 | 類型 | 值 |
|:---|:---|:---|
|`[artifacts("myTemplateArtifact").outputs.myArray]` | Array | \["first", "second"\] |
|`[artifacts("myTemplateArtifact").outputs.myArray[0]]` | String | "first" |
|`[artifacts("myTemplateArtifact").outputs.myString]` | String | "my string value" |
|`[artifacts("myTemplateArtifact").outputs.myObject]` | Object | { "myproperty": "my value", "anotherProperty": true } |
|`[artifacts("myTemplateArtifact").outputs.myObject.myProperty]` | String | "my value" |
|`[artifacts("myTemplateArtifact").outputs.myObject.anotherProperty]` | Bool | True |

## <a name="concat"></a>concat

`concat(string1, string2, string3, ...)`

結合多個字串值，並傳回串連的字串。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| string1 |是 |字串 |串連的第一個值。 |
| 其他引數 |否 |字串 |串連的其他值 (循序順序) |

### <a name="return-value"></a>傳回值

串連值的字串。

### <a name="remarks"></a>備註

Azure 藍圖函式與 ARM 範本功能不同之處在于，它只適用于字串。

### <a name="example"></a>範例

`concat(parameters('organizationName'), '-vm')`

## <a name="parameters"></a>參數

`parameters(parameterName)`

傳回藍圖參數值。 指定的參數名稱必須在藍圖定義或藍圖成品中定義。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| parameterName |是 |字串 |要傳回的參數名稱。 |

### <a name="return-value"></a>傳回值

指定藍圖或藍圖成品參數的值。

### <a name="remarks"></a>備註

Azure 藍圖函式與 ARM 範本函式不同之處在于，它只適用于藍圖參數。

### <a name="example"></a>範例

在藍圖定義中定義 principalIds 參數：

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "parameters": {
            "principalIds": {
                "type": "array",
                "metadata": {
                    "displayName": "Principal IDs",
                    "description": "This is a blueprint parameter that any artifact can reference. We'll display these descriptions for you in the info bubble. Supply principal IDs for the users,groups, or service principals for the Azure role assignment.",
                    "strongType": "PrincipalId"
                }
            }
        },
        ...
    }
}
```

然後使用 principalIds 作為藍圖成品中 `parameters()` 的引數：

```json
{
    "type": "Microsoft.Blueprint/blueprints/artifacts",
    "kind": "roleAssignment",
    ...
    "properties": {
        "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
        "principalIds": "[parameters('principalIds')]",
        ...
    }
}
```

## <a name="resourcegroup"></a>resourceGroup

`resourceGroup()`

傳回代表目前資源群組的物件。

### <a name="return-value"></a>傳回值

傳回的物件會使用下列格式：

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="remarks"></a>備註

Azure 藍圖函式與 ARM 範本功能不同。 `resourceGroup()` 函式不能用在訂用帳戶層級成品或藍圖定義中。 其只能用於屬於資源群組成品一部分的藍圖成品。

`resourceGroup()` 函式的常見用法是在和資源群組成品相同的位置中建立資源。

### <a name="example"></a>範例

若要將藍圖定義中或指派期間設定的資源群組位置當作另一個成品的位置使用，請在藍圖定義中宣告資源群組的預留位置物件。 在此範例中，NetworkingPlaceholder 是資源群組預留位置的名稱。

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "resourceGroups": {
            "NetworkingPlaceholder": {
                "location": "eastus"
            }
        }
    }
}
```

然後在以資源群組預留位置物件為目標的藍圖成品內容中，使用 `resourceGroup()` 函式。 在此範例中，範本成品會部署到 NetworkingPlaceholder 資源群組中，並將以動態方式填入 NetworkingPlaceholder 資源群組位置的 resourceLocation 參數提供給範本。 NetworkingPlaceholder 資源群組的位置可能已在藍圖定義上以靜態方式定義，或在指派期間以動態方式定義。 不論是哪一種情況，範本成品都會以參數的形式提供，並使用其將資源部署到正確的位置。

```json
{
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "kind": "template",
  "properties": {
      "template": {
        ...
      },
      "resourceGroup": "NetworkingPlaceholder",
      ...
      "parameters": {
        "resourceLocation": {
          "value": "[resourceGroup().location]"
        }
      }
  }
}
```

## <a name="resourcegroups"></a>resourceGroups

`resourceGroups(placeholderName)`

傳回表示指定資源群組成品的物件。 不同於需要成品內容的 `resourceGroup()`，不在該資源群組的內容中時，可使用此函式來取得特定資源群組預留位置的屬性。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| placeholderName |是 |字串 |要傳回的資源群組成品的預留位置名稱。 |

### <a name="return-value"></a>傳回值

傳回的物件會使用下列格式：

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="example"></a>範例

若要將藍圖定義中或指派期間設定的資源群組位置當作另一個成品的位置使用，請在藍圖定義中宣告資源群組的預留位置物件。 在此範例中，NetworkingPlaceholder 是資源群組預留位置的名稱。

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "resourceGroups": {
            "NetworkingPlaceholder": {
                "location": "eastus"
            }
        }
    }
}
```

然後，從任何藍圖成品的內容使用 `resourceGroups()` 函式，以取得資源群組預留位置物件的參考。 在此範例中，範本成品會部署到 NetworkingPlaceholder 資源群組外，並將以動態方式填入 NetworkingPlaceholder 資源群組位置的 artifactLocation 參數提供給範本。 NetworkingPlaceholder 資源群組的位置可能已在藍圖定義上以靜態方式定義，或在指派期間以動態方式定義。 不論是哪一種情況，範本成品都會以參數的形式提供，並使用其將資源部署到正確的位置。

```json
{
  "kind": "template",
  "properties": {
      "template": {
          ...
      },
      ...
      "parameters": {
        "artifactLocation": {
          "value": "[resourceGroups('NetworkingPlaceholder').location]"
        }
      }
  },
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "name": "myTemplate"
}
```

## <a name="subscription"></a>訂用帳戶

`subscription()`

傳回目前藍圖指派的訂用帳戶詳細資料。

### <a name="return-value"></a>傳回值

傳回的物件會使用下列格式：

```json
{
    "id": "/subscriptions/{subscriptionId}",
    "subscriptionId": "{subscriptionId}",
    "tenantId": "{tenantId}",
    "displayName": "{name-of-subscription}"
}
```

### <a name="example"></a>範例

使用訂用帳戶的顯示名稱和 `concat()` 函式，建立作為 resourceName 參數傳遞至範本成品的命名慣例。

```json
{
  "kind": "template",
  "properties": {
      "template": {
          ...
      },
      ...
      "parameters": {
        "resourceName": {
          "value": "[concat(subscription().displayName, '-vm')]"
        }
      }
  },
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "name": "myTemplate"
}
```

## <a name="next-steps"></a>後續步驟

- 了解[藍圖生命週期](../concepts/lifecycle.md)。
- 了解如何使用[靜態與動態參數](../concepts/parameters.md)。
- 了解如何自訂[藍圖排序順序](../concepts/sequencing-order.md)。
- 了解如何使用[藍圖資源鎖定](../concepts/resource-locking.md)。
- 了解如何[更新現有的指派](../how-to/update-existing-assignments.md)。
- 使用[一般疑難排解](../troubleshoot/general.md)來解決藍圖指派期間發生的問題。