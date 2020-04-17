---
title: 原則定義結構的詳細資料
description: 描述如何使用策略定義為組織中的 Azure 資源建立約定。
ms.date: 04/03/2020
ms.topic: conceptual
ms.openlocfilehash: 0a7c4e05270ff242fa97b253b27a5de92895368a
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460999"
---
# <a name="azure-policy-definition-structure"></a>Azure 原則定義結構

Azure 策略為資源建立約定。 策略定義描述資源合規性[條件](#conditions)以及滿足條件時要達到的效果。 條件將資源屬性[欄位](#fields)與所需值進行比較。 使用[別名](#aliases)存取資源屬性欄位。 資源屬性欄位是單值欄位或多個值的[陣列](#understanding-the--alias)。 條件計算在陣列上是不同的。
瞭解有關[條件](#conditions)的更多。

藉由定義慣例，您可以控制成本以及更輕鬆地管理您的資源。 例如，您可以指定僅允許特定類型的虛擬機器。 或者，您可以要求所有資源都有特定標籤。 原則會由所有子資源繼承。 如果將某個原則套用至資源群組，它會適用於該資源群組中的所有資源。

原則定義架構的以下的信任 :[https://schema.management.azure.com/schemas/2019-06-01/policyDefinition.json](https://schema.management.azure.com/schemas/2019-06-01/policyDefinition.json)

使用 JSON 來建立原則定義。 原則定義中包含以下的項目︰

- mode
- 參數
- 顯示名稱
- description
- 原則規則
  - 邏輯評估
  - 效果

例如，下列 JSON 示範一個會限制資源部署位置的原則：

```json
{
    "properties": {
        "mode": "all",
        "parameters": {
            "allowedLocations": {
                "type": "array",
                "metadata": {
                    "description": "The list of locations that can be specified when deploying resources",
                    "strongType": "location",
                    "displayName": "Allowed locations"
                },
                "defaultValue": [ "westus2" ]
            }
        },
        "displayName": "Allowed locations",
        "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
        "policyRule": {
            "if": {
                "not": {
                    "field": "location",
                    "in": "[parameters('allowedLocations')]"
                }
            },
            "then": {
                "effect": "deny"
            }
        }
    }
}
```

所有 Azure 策略範例都在[Azure 策略範例](../samples/index.md)中。

## <a name="mode"></a>[模式]

**根據**策略的目標是 Azure 資源管理員屬性或資源提供程式屬性,配置模式。

### <a name="resource-manager-modes"></a>資源管理員模式

**Mode** 決定原則要評估哪些資源類型。 支援的模式如下：

- `all`：評估資源群組和所有資源類型
- `indexed`：只評估支援標記和位置的資源類型

例如,資源`Microsoft.Network/routeTables`支持標記和位置,並在這兩種模式下進行評估。 但是,無法`Microsoft.Network/routeTables/routes`標記資源,並且無法在`Indexed`模式下進行評估。

我們建議您在大部分的情況下都將 **mode** 設定為 `all`。 透過入口網站使用 `all` 模式建立的所有原則定義。 如果您是使用 PowerShell 或 Azure CLI，則可手動指定 **mode** 參數。 如果原則定義未包含 **mode** 值，則在 Azure PowerShell 中會預設為 `all`，而在 Azure CLI 中會預設為 `null`。 `null` 模式與使用 `indexed` 來支援回溯相容性相同。

建立會強制執行標籤或位置的原則時，應該使用 `indexed`。 雖然並非必要，但它可防止不支援標籤和位置的資源在合規性結果中顯示為不符合規範。 有一個例外，就是**資源群組**。 原則如果會在資源群組上強制執行位置或標籤，就應該將 **mode** 設定為 `all`，並明確地以 `Microsoft.Resources/subscriptions/resourceGroups` 類型作為目標。 如需範例，請參閱[強制執行資源群組標籤](../samples/enforce-tag-rg.md)。 有關支援標記的資源清單,請參閱[標籤對 Azure 資源的支援](../../../azure-resource-manager/management/tag-support.md)。

### <a name="resource-provider-modes-preview"></a><a name="resource-provider-modes" />資源提供常式模式(預覽)

預覽期間目前支援以下資源提供者模式:

- `Microsoft.ContainerService.Data`用於管理[Azure 庫伯內斯服務](../../../aks/intro-kubernetes.md)上的入場控制器規則。 使用此資源提供程式模式的策略**必須**使用[強制重新策略](./effects.md#enforceregopolicy)效果。
- `Microsoft.Kubernetes.Data`用於在 Azure 上管理自管理的 AKS 引擎庫伯奈斯群集。
  使用此資源提供程式模式的策略**必須**使用[強制OPA約束](./effects.md#enforceopaconstraint)效果。
- `Microsoft.KeyVault.Data`用於在[Azure 金鑰保管庫](../../../key-vault/general/overview.md)中管理保管庫和證書。

> [!NOTE]
> 資源提供程式模式僅支援內建策略定義,並且在預覽版中不支援計劃。

## <a name="parameters"></a>參數

參數可減少原則定義數量來幫助您簡化原則管理。 將參數想像成就像表單上的欄位一樣 – `name``address`、`city``state`。 這些參數一律保持不變，不過它們的值則會根據填寫表單的個人而有所變更。
在建立原則時，參數也是以相同的方式運作。 藉由在原則定義中納入參數，您便可以針對不同的案例使用不同的值，來重複使用該原則。

> [!NOTE]
> 參數可能會加入至現有的和指派的定義。 新的參數必須包含 **defaultValue** 屬性。 這可避免原則或計畫的現有指派間接地變成無效。

### <a name="parameter-properties"></a>參數屬性

參數有下列在原則定義中使用的屬性：

- **名稱**: 參數的名稱。 由原則規則中的 `parameters` 部署函式使用。 如需詳細資訊，請參閱[使用參數值](#using-a-parameter-value)。
- `type`:確定參數是**字串**,**陣列**,**物件**,**布林、****整數**,**浮點**還是**日期時間**。
- `metadata`:定義主要由 Azure 門戶用於顯示使用者友好資訊的子屬性:
  - `description`:參數用於什麼的說明。 能用來提供可接受值的範例。
  - `displayName`:參數門戶中顯示的友好名稱。
  - `version`:(可選)跟蹤有關策略定義內容版本的詳細資訊。

    > [!NOTE]
    > Azure 策略服務`version``preview`使用`deprecated`和 屬性來表示對內置策略定義或計劃與狀態的更改級別。 的`version`格式是: `{Major}.{Minor}.{Patch}`。 特定狀態(如_棄用_或_預覽_)作為`version`**布斯斯**追加到屬性或其他屬性中。

  - `category`:(可選)確定在 Azure 門戶中顯示策略定義的類別。
  - `strongType`:(可選)通過門戶分配策略定義時使用。 提供內容感知清單。 如需詳細資訊，請參閱 [strongType](#strongtype)。
  - `assignPermissions`:(可選) 設定為_true,_ 以便 Azure 門戶在策略分配期間創建角色分配。 如果您希望在分配範圍之外分配許可權,則此屬性很有用。 策略中每個角色定義有一個角色分配(或計劃中的所有策略中每個角色定義)。 參數值必須是有效的資源或作用域。
- `defaultValue`:(可選)如果未給出值,則設置賦值中的參數值。
  更新已指派的現有原則定義時需要。
- `allowedValues`:(可選) 提供參數在賦值期間接受的值陣列。

舉例來說，您可以定義一個原則定義來限制可部署資源的位置。 該原則定義的參數可為 **allowedLocations**。 原則定義的每個指派都會使用此參數來限制接受的值。 透過入口網站完成指派時，**strongType** 提供增強的體驗：

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": [ "westus2" ],
        "allowedValues": [
            "eastus2",
            "westus2",
            "westus"
        ]
    }
}
```

### <a name="using-a-parameter-value"></a>使用參數值

在策略規則中,引用具有以下`parameters`函數語法的參數:

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

此範例參考[參數屬性](#parameter-properties)中示範的 **allowedLocations** 參數。

### <a name="strongtype"></a>strongType

在 `metadata` 屬性內，您可以使用 **strongType** 在 Azure 入口網站內提供可複選的選項清單。 **強類型**可以是受支援_的資源類型_或允許的值。 要確定_資源類型_是否對**強類型**有效,請使用[Get-AzResource 提供者](/powershell/module/az.resources/get-azresourceprovider)。

**支援 Get-AzResourceProvider**未傳回的某些_資源類型_。 它們是:

- `Microsoft.RecoveryServices/vaults/backupPolicies`

**強類型**允許的非_資源類型_值是:

- `location`
- `resourceTypes`
- `storageSkus`
- `vmSKUs`
- `existingResourceGroups`

## <a name="definition-location"></a>定義位置

建立方案或原則時，必須指定定義位置。 定義位置必須是管理群組或訂用帳戶。 此位置可決定方案或原則的指派範圍。 資源必須是定義位置階層內的直屬成員或其子系，才可作為指派的目標。

如果定義位置為：

- **訂用帳戶** - 只能將原則指派給該訂用帳戶內的資源。
- **管理群組** - 只能將原則指派給子管理群組與子訂用帳戶內的資源。 如果您打算將原則定義套用至數個訂用帳戶，則位置必須是包含那些訂用帳戶的管理群組。

## <a name="display-name-and-description"></a>顯示名稱和描述

您可以使用 **displayName** 和 **description** 來識別原則定義，以及提供其使用時機的內容。 **displayName** 的長度上限為 _128_ 個字元，**description** 的長度上限則為 _512_ 個字元。

> [!NOTE]
> 在建立或更新策略定義期間 **,ID、****類型**和**名稱**由 JSON 外部的屬性定義,並且 JSON 檔中不需要。 通過 SDK 獲取策略定義將返回**ID、type**和**名稱**屬性作為 JSON 的一部分,但每個屬性都是與**type**策略定義的唯讀資訊。

## <a name="policy-rule"></a>原則規則

原則規則包含 **If** 和 **Then**區塊。 在 **If** 區塊中，您可以定義一個或多個指定強制執行此原則時間的條件。 您可以將邏輯運算子套用至這些條件，以精確地定義原則的案例。

在 **Then** 區塊中，定義當 **If** 條件履行時所發生的效果。

```json
{
    "if": {
        <condition> | <logical operator>
    },
    "then": {
        "effect": "deny | audit | append | auditIfNotExists | deployIfNotExists | disabled"
    }
}
```

### <a name="logical-operators"></a>邏輯運算子

支援的邏輯運算子包括︰

- `"not": {condition  or operator}`
- `"allOf": [{condition or operator},{condition or operator}]`
- `"anyOf": [{condition or operator},{condition or operator}]`

**not** 語法會反轉條件的結果。 **allOf** 語法 (類似於邏輯**And** 作業) 需要所有的條件為 true。 **anyOf** 語法 (類似於邏輯**Or** 作業) 需要一或多個條件為 true。

您可以巢狀邏輯運算子。 下列範例顯示 **allOf** 作業中的巢狀 **not** 作業。

```json
"if": {
    "allOf": [{
            "not": {
                "field": "tags",
                "containsKey": "application"
            }
        },
        {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
        }
    ]
},
```

### <a name="conditions"></a>條件

條件會評估 **field** 或 **value** 存取子是否符合特定條件。 支援的條件如下︰

- `"equals": "stringValue"`
- `"notEquals": "stringValue"`
- `"like": "stringValue"`
- `"notLike": "stringValue"`
- `"match": "stringValue"`
- `"matchInsensitively": "stringValue"`
- `"notMatch": "stringValue"`
- `"notMatchInsensitively": "stringValue"`
- `"contains": "stringValue"`
- `"notContains": "stringValue"`
- `"in": ["stringValue1","stringValue2"]`
- `"notIn": ["stringValue1","stringValue2"]`
- `"containsKey": "keyName"`
- `"notContainsKey": "keyName"`
- `"less": "dateValue"` | `"less": "stringValue"` | `"less": intValue`
- `"lessOrEquals": "dateValue"` | `"lessOrEquals": "stringValue"` | `"lessOrEquals": intValue`
- `"greater": "dateValue"` | `"greater": "stringValue"` | `"greater": intValue`
- `"greaterOrEquals": "dateValue"` | `"greaterOrEquals": "stringValue"` | `"greaterOrEquals": intValue`
- `"exists": "bool"`

對於**較少的****「小於或相等」、****更大**和**更大的 OrEquals,** 如果屬性類型與條件類型不匹配,則引發錯誤。 使用`InvariantCultureIgnoreCase`進行字串比較。

使用 **like** 和 **notLike** 條件時，您可以在值中提供 `*` 萬用字元。
值不應包含多個 `*` 萬用字元。

使用**匹配****和不匹配**條件時`#`,提供匹配`?`數位、`.`字母、 匹配任何字元和任何其他字元以匹配該實際字元。 雖然**匹配**和**不匹配**區分大小寫,但評估_字串Value_的所有其他條件都不區分大小寫。 不會區分大小寫的替代項目，可在 **matchInsensitively** 和 **notMatchInsensitively** 中取得。

**在 別\]名 陣列欄位值中,陣列中的每個元素都使用邏輯 和元素之間單獨計算。\* \[****and** 有關詳細資訊,請參閱[\[\*\]評估 別名](../how-to/author-policies-for-arrays.md#evaluating-the--alias)。

### <a name="fields"></a>欄位

條件是透過欄位所形成。 欄位會比對資源要求裝載中的屬性，並描述資源的狀態。

支援下列欄位：

- `name`
- `fullName`
  - 傳回資源的完整名稱。 資源的完整名稱是資源名稱前面加上任何父系資源名稱 (例如 "myServer/myDatabase")。
- `kind`
- `type`
- `location`
  - 針對不受特定位置限制的資源，請使用 **global**。
- `identity.type`
  - 傳回資源上所啟用[受控識別](../../../active-directory/managed-identities-azure-resources/overview.md)的類型。
- `tags`
- `tags['<tagName>']`
  - 此括號語法支援具有連字號、句號或空格等標點符號的標籤名稱。
  - **其中標記名稱\>是標記的名稱,用於驗證 其條件。 \<**
  - 範例：`tags['Acct.CostCenter']`，其中 **Acct.CostCenter** 是標籤的名稱。
- `tags['''<tagName>''']`
  - 此括號語法能透過以雙引號進行逸出，來支援具有單引號的標籤名稱。
  - **其中『tagName』\<\>** 是用於驗證其條件的標記的名稱。
  - 示例`tags['''My.Apostrophe.Tag''']`**:"My.Apost/he.Tag"** 是標記的名稱。
- 屬性別名 - 如需清單，請參閱[別名](#aliases)。

> [!NOTE]
> `tags.<tagName>`、`tags[tagName]` 和 `tags[tag.with.dots]` 都仍是可接受的宣告標籤欄位方式。 不過，建議的運算式為上面所列的運算式。

#### <a name="use-tags-with-parameters"></a>搭配參數使用標籤

可以將參數值傳遞到標籤欄位。 將參數傳遞到標籤欄位能在原則指派期間提升原則定義的彈性。

在下列範例中，`concat` 被用來建立針對名稱為 **tagName** 參數值之標籤的標籤欄位查閱。 如果該標記不存在,則**修改**效果用於使用`resourcegroup()`look 查找函數使用審核的資源父資源組上設置的相同命名標記的值添加標記。

```json
{
    "if": {
        "field": "[concat('tags[', parameters('tagName'), ']')]",
        "exists": "false"
    },
    "then": {
        "effect": "modify",
        "details": {
            "operations": [{
                "operation": "add",
                "field": "[concat('tags[', parameters('tagName'), ']')]",
                "value": "[resourcegroup().tags[parameters('tagName')]]"
            }],
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ]
        }
    }
}
```

### <a name="value"></a>值

條件也可以使用 **value** 形成。 **value** 會檢查 [parameters](#parameters)、[支援的範本函式](#policy-functions)或常值的條件。
**value** 已和任何支援的 [condition](#conditions) 配對。

> [!WARNING]
> 如果「範本函式」__ 的結果為錯誤，則原則評估會失敗。 失敗的評估隱含著**拒絕**的意思。 如需詳細資訊，請參閱[避免範本錯誤](#avoiding-template-failures)。 使用**DoNotEnforce** [的強制模式](./assignment-structure.md#enforcement-mode),在測試和驗證新的策略定義時,防止評估失敗對新資源或更新的資源的影響。

#### <a name="value-examples"></a>Value 範例

此原則規則範例使用 **value** 來將 `resourceGroup()` 函式和傳回的 **name** 屬性與 `*netrg` 的 **like** 條件比較。 規則會拒絕名稱結尾是 `*netrg` 的任何資源群組中，任何不屬於 `Microsoft.Network/*` **type** 的任何資源。

```json
{
    "if": {
        "allOf": [{
                "value": "[resourceGroup().name]",
                "like": "*netrg"
            },
            {
                "field": "type",
                "notLike": "Microsoft.Network/*"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

此原則規則範例使用 **value** 來檢查多個巢狀函式的結果是否 **equals** `true`。 規則會拒絕沒有至少三個標籤的任何資源。

```json
{
    "mode": "indexed",
    "policyRule": {
        "if": {
            "value": "[less(length(field('tags')), 3)]",
            "equals": "true"
        },
        "then": {
            "effect": "deny"
        }
    }
}
```

#### <a name="avoiding-template-failures"></a>避免範本故障

在**值**中使用_範本函數_允許使用許多複雜的嵌套函數。 如果「範本函式」__ 的結果為錯誤，則原則評估會失敗。 失敗的評估隱含著**拒絕**的意思。 在某些情況下失敗**的值**的範例:

```json
{
    "policyRule": {
        "if": {
            "value": "[substring(field('name'), 0, 3)]",
            "equals": "abc"
        },
        "then": {
            "effect": "audit"
        }
    }
}
```

上面的範例策略規則使用[子字串()](../../../azure-resource-manager/templates/template-functions-string.md#substring)將**名稱**的前三個字元與**abc**進行比較。 如果**名稱**短於三個字元`substring()`,則 函數會導致錯誤。 此錯誤會導致策略成為**拒絕**效果。

相反,使用[if()](../../../azure-resource-manager/templates/template-functions-logical.md#if)函數檢查**名稱**的前三個字元是否等於**abc,** 而**不允許名稱短**於三個字元導致錯誤:

```json
{
    "policyRule": {
        "if": {
            "value": "[if(greaterOrEquals(length(field('name')), 3), substring(field('name'), 0, 3), 'not starting with abc')]",
            "equals": "abc"
        },
        "then": {
            "effect": "audit"
        }
    }
}
```

使用修訂後的策略規則`if()`, 在嘗試獲取少於三`substring()`個字元 的值之前,檢查**名稱**的長度。 如果**名稱**太短,則返回值"不從abc開始",並將其與**abc**進行比較。 名稱短且不以**abc**開頭的資源仍然失敗策略規則,但在評估期間不再導致錯誤。

### <a name="count"></a>Count

可以使用**count**運算式來表示計算資源負載中陣列滿足條件表達式的成員數的條件。 常見方案是檢查陣組成員是否滿足條件,"至少一個","確切地說,全部"或"沒有一個"。 **count**計算條件表達式的每個[\[\*\]別名](#understanding-the--alias)數位成員並求和真實結果,然後將_該結果_與表達式運算符進行比較。

**count**表示式的結構是:

```json
{
    "count": {
        "field": "<[*] alias>",
        "where": {
            /* condition expression */
        }
    },
    "<condition>": "<compare the count of true condition expression array members to this value>"
}
```

以下屬性與**count**一起使用:

- **count.field(** 必要):包含陣列的路徑,並且必須是陣列別名。 如果缺少陣列,則表達式計算為_false,_ 而不考慮條件運算式。
- **計數.where(** 可選):用於單獨計算**count.field**的每個[\[\*\]別名](#understanding-the--alias)數位成員的條件表達式。 如果未提供此屬性,則所有路徑為「欄位」的陣列成員將計算為_true_。 任何[條件](../concepts/definition-structure.md#conditions)都可以在此屬性內使用。
  [邏輯運算元](#logical-operators)可用於此屬性內創建複雜的評估要求。
- 條件(必需):該值與滿足**count.where**條件表達式的項數進行**\>\<** 比較。 應使用數值[條件](../concepts/definition-structure.md#conditions)。

#### <a name="count-examples"></a>計算範例

範例 1:檢查陣列是否為空

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]"
    },
    "equals": 0
}
```

範例 2:僅檢查一個陣列成員以滿足條件運算式

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "My unique description"
        }
    },
    "equals": 1
}
```

範例 3:檢查至少一個陣列成員以滿足條件運算式

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "My common description"
        }
    },
    "greaterOrEquals": 1
}
```

範例 4:檢查所有物件陣列成員是否符合條件運算式

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "description"
        }
    },
    "equals": "[length(field('Microsoft.Network/networkSecurityGroups/securityRules[*]'))]"
}
```

範例 5:檢查所有字串陣元成員是否符合條件運算式

```json
{
    "count": {
        "field": "Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]",
        "where": {
            "field": "Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]",
            "like": "*@contoso.com"
        }
    },
    "equals": "[length(field('Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]'))]"
}
```

範例 6:使用**內部值的欄位**檢查所有陣列成員是否符合**value**條件運算式

```json
{
    "count": {
        "field": "Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]",
        "where": {
            "value": "[last(split(first(field('Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]')), '@'))]",
            "equals": "contoso.com"
        }
    },
    "equals": "[length(field('Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]'))]"
}
```

範例 7:檢查至少一個陣列成員是否與條件運算式中的多個屬性匹配

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "allOf": [
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].direction",
                    "equals": "Inbound"
                },
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].access",
                    "equals": "Allow"
                },
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].destinationPortRange",
                    "equals": "3389"
                }
            ]
        }
    },
    "greater": 0
}
```

### <a name="effect"></a>效果

Azure 政策支援以下類型的效果:

- **Append**：會在要求中加入一組已定義的欄位
- **Audit**：會在活動記錄中產生警告事件，但不會讓要求失敗
- **稽核IfNot存在**:如果相關資源不存在,則在活動日誌中生成警告事件
- **Deny**：會在活動記錄中產生事件，並讓要求失敗
- **部署不存在**:部署相關資源(如果不存在)
- **Disabled**：不會評估資源是否符合原則規則的規範
- **實施OPA約束**(預覽):使用門衛 v3 配置開放策略代理准入控制器,用於 Azure 上的自管理的 Kubernetes 群集(預覽)
- **執行重新策略**(預覽):在 Azure Kubernetes 服務中配置具有門衛 v2 的開放策略代理准入控制器
- **變更**: 從資源新增、更新或移除定義的標籤

有關每個效果、評估順序、屬性和範例的完整詳細資訊,請參閱[瞭解 Azure 策略效果](effects.md)。

### <a name="policy-functions"></a>原則函式

除以下函數和使用者定義的函數外,所有[資源管理員範本函數](../../../azure-resource-manager/templates/template-functions.md)都可以在策略規則中使用:

- copyIndex()
- deployment()
- list*
- 新吉德()
- 拾取區()
- providers()
- reference()
- resourceId()
- variables()

> [!NOTE]
> 這些函數在**部署中的**範本`details.deployment.properties.template`部署 部分仍然可用。

以下函數可用於策略規則,但與 Azure 資源管理員樣本中的使用不同:

- `utcNow()`- 與資源管理器範本不同,可以在預設值之外使用。
  - 傳回在通用 ISO 8601 DateTime 格式「yyyyy-MM-ddTHH:mm:s.fffffZ」中設定為當前日期和時間的字串

以下函數只在策略規則中可用:

- `addDays(dateTime, numberOfDaysToAdd)`
  - **日期時間**: [必需] 字串 - 通用 ISO 8601 日期時間格式的字串 "yyyy-MM-ddTHH:mm:s.fffffZ"
  - **要新增的天數**: [必需] 整數 - 要新增的天數
- `field(fieldName)`
  - **欄位名稱**: [必要] 字串 - 要檢索的[欄位](#fields)的名稱
  - 從「If 條件」正在評估的資源中傳回該欄位的值
  - `field` 主要是與 **AuditIfNotExists** 和 **DeployIfNotExists** 搭配使用，以參考所評估資源上的欄位。 如需此用法的範例，請參閱 [DeployIfNotExists 範例](effects.md#deployifnotexists-example)。
- `requestContext().apiVersion`
  - 傳回觸發原則評估的要求的 API 版本`2019-09-01`(例如: 。
    這將是 PUT/PATCH 請求中使用的 API 版本,用於評估資源創建/更新。 在現有資源的合規性評估期間,始終使用最新的 API 版本。
  
#### <a name="policy-function-example"></a>原則函式範例

此原則規則範例會使用 `resourceGroup` 資源函式來取得**名稱**屬性，並與 `concat` 陣列和物件函式結合來建置 `like` 條件，以強制資源名稱的開頭使用資源群組名稱。

```json
{
    "if": {
        "not": {
            "field": "name",
            "like": "[concat(resourceGroup().name,'*')]"
        }
    },
    "then": {
        "effect": "deny"
    }
}
```

## <a name="aliases"></a>別名

您可以使用屬性別名來存取資源類型的特定屬性。 別名可讓您針對資源上的屬性限制所允許的值或條件。 每個別名會對應至指定資源類型之不同 API 版本中的路徑。 在原則評估期間，原則引擎會取得該 API 版本的屬性路徑。

別名清單會不斷成長。 若要了解「Azure 原則」目前支援哪些別名，請使用下列其中一種方法：

- 視覺化工作室代碼的 Azure 策略延伸(建議)

  使用[視覺化工作室代碼的 Azure 策略擴展](../how-to/extension-for-vscode.md)來查看和發現資源屬性的別名。

  ![視覺化工作室代碼的 Azure 策略延伸](../media/extension-for-vscode/extension-hover-shows-property-alias.png)

- Azure Resource Graph

  使用`project`運算子顯示資源的**別名**。

  ```kusto
  Resources
  | where type=~'microsoft.storage/storageaccounts'
  | limit 1
  | project aliases
  ```
  
  ```azurecli-interactive
  az graph query -q "Resources | where type=~'microsoft.storage/storageaccounts' | limit 1 | project aliases"
  ```
  
  ```azurepowershell-interactive
  Search-AzGraph -Query "Resources | where type=~'microsoft.storage/storageaccounts' | limit 1 | project aliases"
  ```

- Azure PowerShell

  ```azurepowershell-interactive
  # Login first with Connect-AzAccount if not using Cloud Shell

  # Use Get-AzPolicyAlias to list available providers
  Get-AzPolicyAlias -ListAvailable

  # Use Get-AzPolicyAlias to list aliases for a Namespace (such as Azure Compute -- Microsoft.Compute)
  (Get-AzPolicyAlias -NamespaceMatch 'compute').Aliases
  ```

- Azure CLI

  ```azurecli-interactive
  # Login first with az login if not using Cloud Shell

  # List namespaces
  az provider list --query [*].namespace

  # Get Azure Policy aliases for a specific Namespace (such as Azure Compute -- Microsoft.Compute)
  az provider show --namespace Microsoft.Compute --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
  ```

- REST API/ARMClient

  ```http
  GET https://management.azure.com/providers/?api-version=2017-08-01&$expand=resourceTypes/aliases
  ```

### <a name="understanding-the--alias"></a>了解 [*] 別名

幾個可用的別名具有顯示為"正常"名稱的版本,另一個名稱已**\[\*** 附加到該名稱。 例如：

- `Microsoft.Storage/storageAccounts/networkAcls.ipRules`
- `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]`

"正常"別名將欄位表示為單個值。 此欄位用於完全匹配比較方案,當整個值集必須完全按照定義、不再和更少時。

**別名\*可以比較數位中每個元素的值和每個元素的特定\[** 屬性。 此方法可以比較元素屬性的"如果沒有","如果任何"或"如果所有"方案。 對於更複雜的方案,請使用[計數](#count)條件表達式。 _value_使用**\[\*ipRules,** 一個範例將驗證每個_操作_都是_Deny,_ 但不擔心存在多少規則或 IP 值是什麼。
此範例規則檢查**\[\*\]ipRules .value**到**10.0.4.1**的任何符合項,並且僅在找不到至少符合項目時套用**效果型態**:

```json
"policyRule": {
    "if": {
        "allOf": [
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "exists": "true"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value",
                "notEquals": "10.0.4.1"
            }
        ]
    },
    "then": {
        "effect": "[parameters('effectType')]"
    }
}
```

有關詳細資訊,請參閱評估[\** 別名](../how-to/author-policies-for-arrays.md#evaluating-the--alias)。

## <a name="initiatives"></a>計畫

計畫可讓您將數個相關的原則定義組成群組來簡化指派和管理，因為您可以將一個群組當作單一項目來使用。 例如，您可以將相關的標籤原則定義組成單一方案。 您可以套用該計畫，而不個別指派每個原則。

> [!NOTE]
> 一旦分配了計劃,則無法更改計劃級別參數。 因此,建議在定義參數時設定**預設值**。

下列範例說明如何建立一個處理兩個標籤 (`costCenter` 和 `productName`) 的計畫。 它會使用兩個內建的原則來套用預設標籤值。

```json
{
    "properties": {
        "displayName": "Billing Tags Policy",
        "policyType": "Custom",
        "description": "Specify cost Center tag and product name tag",
        "parameters": {
            "costCenterValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for Cost Center tag"
                },
                "defaultValue": "DefaultCostCenter"
            },
            "productNameValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for product Name tag"
                },
                "defaultValue": "DefaultProduct"
            }
        },
        "policyDefinitions": [{
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            }
        ]
    }
}
```

## <a name="next-steps"></a>後續步驟

- 查看[Azure 策略範例](../samples/index.md)中的範例。
- 檢閱[了解原則效果](effects.md)。
- 瞭解如何[以程式設計方式建立原則](../how-to/programmatically-create.md)。
- 瞭解如何[取得合規性資料](../how-to/get-compliance-data.md)。
- 瞭解如何[修復不合規資源](../how-to/remediate-resources.md)。
- 使用[Azure 管理組查看](../../management-groups/overview.md)管理組的內容。請組織資源。
