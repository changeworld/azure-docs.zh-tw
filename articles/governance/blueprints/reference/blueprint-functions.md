---
title: Azure 藍圖函式
description: 描述可用於 Azure 藍圖定義和指派中藍圖成品的函式。
ms.date: 12/09/2019
ms.topic: reference
ms.openlocfilehash: 0aab2fe0511ccc11842d0e132a83d6e3f7fac27f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79280673"
---
# <a name="functions-for-use-with-azure-blueprints"></a>搭配 Azure 藍圖使用的函數

Azure 藍圖提供讓藍圖定義更具動態功能的函式。 這些函式可與藍圖定義和藍圖成品搭配使用。 除了透過藍圖參數取得動態值之外，Resource Manager 範本成品也支援 Resource Manager 函數的完整使用。

支援下列功能：

- [成品](#artifacts)
- [concat](#concat)
- [參數](#parameters)
- [resourceGroup](#resourcegroup)
- [resourceGroups](#resourcegroups)
- [預定](#subscription)

## <a name="artifacts"></a>構件

`artifacts(artifactName)`

傳回已填入藍圖成品輸出之屬性的物件。

> [!NOTE]
> `artifacts()`無法從 Resource Manager 範本內部使用函式。 在使用 Azure PowerShell 或 REST API 作為[藍圖即程式碼](https://github.com/Azure/azure-blueprints/blob/master/README.md)的一部分來管理藍圖時，函數只能用於藍圖定義 JSON 或成品 JSON 中。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 說明 |
|:--- |:--- |:--- |:--- |
| artifactName |是 |字串 |藍圖成品的名稱。 |

### <a name="return-value"></a>傳回值

輸出屬性的物件。 **輸出**屬性取決於所參考的藍圖成品類型。 所有類型都遵循以下格式：

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

#### <a name="resource-manager-template-artifact"></a>Resource Manager 範本成品

傳回之物件的**輸出**屬性會定義在 Resource Manager 範本內，並由部署傳回。

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

具有識別碼_myTemplateArtifact_的 Resource Manager 範本成品，其中包含下列範例輸出屬性：

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

從_myTemplateArtifact_範例中取出資料的一些範例包括：

| 運算是 | 類型 | 值 |
|:---|:---|:---|
|`[artifacts("myTemplateArtifact").outputs.myArray]` | Array | \[「第一個」、「第二個」\] |
|`[artifacts("myTemplateArtifact").outputs.myArray[0]]` | 字串 | 頭 |
|`[artifacts("myTemplateArtifact").outputs.myString]` | 字串 | 「我的字串值」 |
|`[artifacts("myTemplateArtifact").outputs.myObject]` | Object | {"myproperty"： "my value"，"anotherProperty"： true} |
|`[artifacts("myTemplateArtifact").outputs.myObject.myProperty]` | 字串 | 「我的價值」 |
|`[artifacts("myTemplateArtifact").outputs.myObject.anotherProperty]` | Bool | True |

## <a name="concat"></a>concat

`concat(string1, string2, string3, ...)`

結合多個字串值，並傳回串連的字串。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 說明 |
|:--- |:--- |:--- |:--- |
| string1 |是 |字串 |串連的第一個值。 |
| 其他引數 |否 |字串 |串連的順序中的其他值 |

### <a name="return-value"></a>傳回值

串連值的字串。

### <a name="remarks"></a>備註

Azure 藍圖函式與 Azure Resource Manager 範本函式不同之處在于，它只適用于字串。

### <a name="example"></a>範例

`concat(parameters('organizationName'), '-vm')`

## <a name="parameters"></a>參數

`parameters(parameterName)`

傳回藍圖參數值。 指定的參數名稱必須在藍圖定義或藍圖成品中定義。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 說明 |
|:--- |:--- |:--- |:--- |
| parameterName |是 |字串 |要傳回的參數名稱。 |

### <a name="return-value"></a>傳回值

指定之藍圖或藍圖成品參數的值。

### <a name="remarks"></a>備註

Azure 藍圖函式與 Azure Resource Manager 範本函式不同之處在于，它只適用于藍圖參數。

### <a name="example"></a>範例

在藍圖定義中定義參數_principalIds_ ：

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
                    "description": "This is a blueprint parameter that any artifact can reference. We'll display these descriptions for you in the info bubble. Supply principal IDs for the users,groups, or service principals for the RBAC assignment.",
                    "strongType": "PrincipalId"
                }
            }
        },
        ...
    }
}
```

然後使用_principalIds_作為藍圖成品`parameters()`中的引數：

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

Azure 藍圖功能與 Azure Resource Manager 範本函式不同。 無法`resourceGroup()`在訂用帳戶層級成品或藍圖定義中使用函數。 它只能用於屬於資源群組成品一部分的藍圖成品。

`resourceGroup()`函數的常見用法是在與資源群組成品相同的位置中建立資源。

### <a name="example"></a>範例

若要使用資源群組的位置，請在藍圖定義或指派期間設定為另一個成品的位置，並在藍圖定義中宣告資源群組預留位置物件。 在此範例中， _NetworkingPlaceholder_是資源群組預留位置的名稱。

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

然後在以`resourceGroup()`資源群組預留位置物件為目標之藍圖成品的內容中使用函式。 在此範例中，會將範本成品部署至_NetworkingPlaceholder_資源群組，並將_NetworkingPlaceholder_資源群組位置動態填入的參數_resourceLocation_提供給範本。 _NetworkingPlaceholder_資源群組的位置可能已在藍圖定義上以靜態方式定義，或在指派期間以動態方式定義。 不論是哪一種情況，範本成品都會以參數的形式提供，並使用它將資源部署到正確的位置。

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

傳回物件，表示指定的資源群組成品。 不同`resourceGroup()`于需要成品內容的，此函式是在不在該資源群組的內容中時，用來取得特定資源群組預留位置的屬性。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 說明 |
|:--- |:--- |:--- |:--- |
| placeholderName |是 |字串 |要傳回之資源群組成品的預留位置名稱。 |

### <a name="return-value"></a>傳回值

傳回的物件會使用下列格式：

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="example"></a>範例

若要使用資源群組的位置，請在藍圖定義或指派期間設定為另一個成品的位置，並在藍圖定義中宣告資源群組預留位置物件。 在此範例中， _NetworkingPlaceholder_是資源群組預留位置的名稱。

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

然後從任何`resourceGroups()`藍圖成品的內容使用函式，以取得資源群組預留位置物件的參考。 在此範例中，範本成品會部署在_NetworkingPlaceholder_資源群組之外，並提供在範本中以_NetworkingPlaceholder_資源群組位置動態填入的參數_artifactLocation_ 。 _NetworkingPlaceholder_資源群組的位置可能已在藍圖定義上以靜態方式定義，或在指派期間以動態方式定義。 不論是哪一種情況，範本成品都會以參數的形式提供，並使用它將資源部署到正確的位置。

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

傳回目前藍圖指派之訂用帳戶的詳細資料。

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

使用訂用帳戶的顯示名稱和`concat()`函式 _，來建立當做參數名稱_傳遞至範本成品的命名慣例。

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