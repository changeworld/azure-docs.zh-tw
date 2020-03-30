---
title: Azure 藍圖功能
description: 描述可用於 Azure 藍圖定義和分配中的藍圖工件的函數。
ms.date: 12/09/2019
ms.topic: reference
ms.openlocfilehash: 0aab2fe0511ccc11842d0e132a83d6e3f7fac27f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280673"
---
# <a name="functions-for-use-with-azure-blueprints"></a>與 Azure 藍圖一起使用的函數

Azure 藍圖提供了使藍圖定義更具動態功能的功能。 這些函數用於藍圖定義和藍圖工件。 資源管理器範本專案除了支援通過藍圖參數獲取動態值外，還支援充分利用資源管理器函數。

支援以下功能：

- [工件](#artifacts)
- [concat](#concat)
- [參數](#parameters)
- [資源組](#resourcegroup)
- [resourceGroups](#resourcegroups)
- [訂閱](#subscription)

## <a name="artifacts"></a>構件

`artifacts(artifactName)`

返回使用該藍圖工件輸出填充的屬性的物件。

> [!NOTE]
> 無法`artifacts()`從資源管理器範本內部使用該函數。 該函數只能在藍圖定義 JSON 或工件 JSON 中使用，當使用 Azure PowerShell 或 REST API 作為[藍圖作為代碼](https://github.com/Azure/azure-blueprints/blob/master/README.md)的一部分時，才可以使用該函數。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| 專案名稱 |是 |字串 |藍圖工件的名稱。 |

### <a name="return-value"></a>傳回值

輸出屬性的物件。 **輸出**屬性取決於要引用的藍圖工件的類型。 所有類型都遵循以下格式：

```json
{
  "outputs": {collectionOfOutputProperties}
}
```

#### <a name="policy-assignment-artifact"></a>策略分配專案

```json
{
    "outputs": {
        "policyAssignmentId": "{resourceId-of-policy-assignment}",
        "policyAssignmentName": "{name-of-policy-assignment}",
        "policyDefinitionId": "{resourceId-of-policy-definition}",
    }
}
```

#### <a name="resource-manager-template-artifact"></a>資源管理器範本專案

返回物件的**輸出**屬性在資源管理器範本中定義，並由部署返回。

#### <a name="role-assignment-artifact"></a>角色指派專案

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

包含以下示例輸出屬性的 ID _myTemplate 工件_的資源管理器範本專案：

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

從_myTemplateArtifact_示例檢索資料的一些示例包括：

| 運算是 | 類型 | 值 |
|:---|:---|:---|
|`[artifacts("myTemplateArtifact").outputs.myArray]` | Array | \["第一"，"第二"\] |
|`[artifacts("myTemplateArtifact").outputs.myArray[0]]` | String | "第一" |
|`[artifacts("myTemplateArtifact").outputs.myString]` | String | "我的字串值" |
|`[artifacts("myTemplateArtifact").outputs.myObject]` | Object | [ "我的財產"："我的價值"，"另一個財產"：真實 | |
|`[artifacts("myTemplateArtifact").outputs.myObject.myProperty]` | String | "我的價值" |
|`[artifacts("myTemplateArtifact").outputs.myObject.anotherProperty]` | Bool | True |

## <a name="concat"></a>concat

`concat(string1, string2, string3, ...)`

結合多個字串值，並傳回串連的字串。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| string1 |是 |字串 |串連的第一個值。 |
| 其他引數 |否 |字串 |按順序排列的附加值 |

### <a name="return-value"></a>傳回值

串聯值的字串。

### <a name="remarks"></a>備註

Azure 藍圖功能不同于 Azure 資源管理器範本功能，因為它僅適用于字串。

### <a name="example"></a>範例

`concat(parameters('organizationName'), '-vm')`

## <a name="parameters"></a>參數

`parameters(parameterName)`

返回藍圖參數值。 指定的參數名稱必須在藍圖定義或藍圖工件中定義。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| parameterName |是 |字串 |要傳回的參數名稱。 |

### <a name="return-value"></a>傳回值

指定藍圖或藍圖工件參數的值。

### <a name="remarks"></a>備註

Azure 藍圖功能不同于 Azure 資源管理器範本功能，因為它僅適用于藍圖參數。

### <a name="example"></a>範例

在藍圖定義中定義參數_主體 Id：_

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

然後使用_主體 Id_作為藍圖工`parameters()`件中的參數：

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

Azure 藍圖功能不同于 Azure 資源管理器範本功能。 該`resourceGroup()`函數不能用於訂閱級專案或藍圖定義。 它只能用於作為資源組工件的一部分的藍圖工件。

`resourceGroup()`函數的常見用途是創建與資源組專案位於同一位置的資源。

### <a name="example"></a>範例

要使用資源組的位置（在藍圖定義中設置或在分配期間作為另一個專案的位置），請聲明藍圖定義中的資源組預留位置物件。 在此示例中，_網路位置擁有者_是資源組預留位置的名稱。

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

然後在以資源`resourceGroup()`組預留位置物件的藍圖專案上下文中使用 函數。 在此示例中，範本專案部署到_網路位置擁有者_資源組中，並提供參數_資源位置位置_動態填充與_網路位置擁有者_資源組位置到範本。 _網路位置擁有者_資源組的位置可以在藍圖定義上靜態定義，也可以在分配期間動態定義。 在這兩種情況下，範本專案都作為參數提供該資訊，並用它來將資源部署到正確的位置。

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

返回表示指定資源組專案的物件。 與`resourceGroup()`需要工件上下文的 不同，此函數用於獲取特定資源組預留位置的屬性，而該屬性不在該資源組的上下文中。

### <a name="parameters"></a>參數

| 參數 | 必要 | 類型 | 描述 |
|:--- |:--- |:--- |:--- |
| 預留位置名稱 |是 |字串 |要返回的資源組專案名。 |

### <a name="return-value"></a>傳回值

傳回的物件會使用下列格式：

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="example"></a>範例

要使用資源組的位置（在藍圖定義中設置或在分配期間作為另一個專案的位置），請聲明藍圖定義中的資源組預留位置物件。 在此示例中，_網路位置擁有者_是資源組預留位置的名稱。

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

然後，`resourceGroups()`使用任何藍圖工件上下文中的函數來獲取對資源組預留位置物件的引用。 在此示例中，範本專案部署在_網路位置擁有者_資源組之外，並提供參數_工件位置，_ 使用 _"網路位置"_ 資源組位置動態填充到範本。 _網路位置擁有者_資源組的位置可以在藍圖定義上靜態定義，也可以在分配期間動態定義。 在這兩種情況下，範本專案都作為參數提供該資訊，並用它來將資源部署到正確的位置。

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

返回有關當前藍圖分配訂閱的詳細資訊。

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

使用訂閱的顯示名稱和`concat()`函數創建作為參數_資源_傳遞給範本工件的命名約定。

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

- 瞭解[藍圖生命週期](../concepts/lifecycle.md)。
- 了解如何使用[靜態與動態參數](../concepts/parameters.md)。
- 了解如何自訂[藍圖排序順序](../concepts/sequencing-order.md)。
- 了解如何使用[藍圖資源鎖定](../concepts/resource-locking.md)。
- 瞭解如何[更新現有作業](../how-to/update-existing-assignments.md)。
- 在分配藍圖期間使用[常規故障排除時](../troubleshoot/general.md)解決問題。