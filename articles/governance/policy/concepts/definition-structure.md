---
title: 原則定義結構的詳細資料
description: 描述如何使用原則定義來建立組織中 Azure 資源的慣例。
ms.date: 06/12/2020
ms.topic: conceptual
ms.openlocfilehash: 87cdca414a04d287f02fec5b3510c4f561cab8c0
ms.sourcegitcommit: 0820c743038459a218c40ecfb6f60d12cbf538b3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87116996"
---
# <a name="azure-policy-definition-structure"></a>Azure 原則定義結構

Azure 原則可建立資源的慣例。 原則定義會描述資源合規性[條件](#conditions)，以及符合條件時所要採取的效果。 條件會將資源屬性[欄位](#fields)與必要值進行比較。 資源屬性欄位是使用[別名](#aliases)來存取。 資源屬性欄位可以是單一值欄位或是多重值的[陣列](#understanding-the--alias)。 陣列上的條件評估有所不同。
深入了解[條件](#conditions)。

藉由定義慣例，您可以控制成本以及更輕鬆地管理您的資源。 例如，您可以指定僅允許特定類型的虛擬機器。 或者，您可以要求資源必須具有特定標記。 子資源會繼承原則指派。 如果原則指派套用至資源群組，則適用于該資源群組中的所有資源。

您可在這裡找到原則定義結構描述：[https://schema.management.azure.com/schemas/2019-09-01/policyDefinition.json](https://schema.management.azure.com/schemas/2019-09-01/policyDefinition.json)

使用 JSON 來建立原則定義。 原則定義中包含以下的項目︰

- 顯示名稱
- description
- mode
- 中繼資料
- 參數
- 原則規則
  - 邏輯評估
  - 效果

例如，下列 JSON 示範一個會限制資源部署位置的原則：

```json
{
    "properties": {
        "displayName": "Allowed locations",
        "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
        "mode": "Indexed",
        "metadata": {
            "version": "1.0.0",
            "category": "Locations"
        },
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

Azure 原則內建和模式位於 Azure 原則的[範例](../samples/index.md)。

## <a name="display-name-and-description"></a>顯示名稱和描述

您可以使用 **displayName** 和 **description** 來識別原則定義，以及提供其使用時機的內容。 **displayName** 的長度上限為 _128_ 個字元，**description** 的長度上限則為 _512_ 個字元。

> [!NOTE]
> 在建立或更新原則定義期間，**識別碼**、**類型**和**名稱**是由 JSON 外部的屬性所定義，而且不需要在 JSON 檔案中加以定義。 透過 SDK 擷取原則定義會傳回**識別碼**、**類型**和**名稱**屬性做為 JSON 的一部分，但每個都是與原則定義相關的唯讀資訊。

## <a name="type"></a>類型

雖然無法設定**type**屬性，但 SDK 會傳回三個值，並在入口網站中顯示：

- `Builtin`：這些原則定義由 Microsoft 提供及維護。
- `Custom`：所有由客戶建立的原則定義都有此值。
- `Static`：表示具有 Microsoft**擁有權**的[法規合規性](./regulatory-compliance.md)原則定義。 這些原則定義的相容性結果是 Microsoft 基礎結構上的協力廠商審核結果。 在 Azure 入口網站中，此值有時會顯示為 [ **Microsoft 管理**]。 如需詳細資訊，請參閱[雲端中的共同責任](../../../security/fundamentals/shared-responsibility.md)。

## <a name="mode"></a>[模式]

**模式**的設定方式取決於原則是以 Azure Resource Manager 屬性還是以資源提供者屬性為目標。

### <a name="resource-manager-modes"></a>Resource Manager 模式

**模式**會決定針對原則定義評估的資源類型。 支援的模式如下：

- `all`：評估資源群組、訂用帳戶和所有資源類型
- `indexed`：只評估支援標記和位置的資源類型

例如，資源 `Microsoft.Network/routeTables` 支援標籤和位置，並在這兩種模式中進行評估。 不過，無法標記資源 `Microsoft.Network/routeTables/routes`，也不會在 `Indexed` 模式中對其進行評估。

我們建議您在大部分的情況下都將 **mode** 設定為 `all`。 透過入口網站使用 `all` 模式建立的所有原則定義。 如果您是使用 PowerShell 或 Azure CLI，則可手動指定 **mode** 參數。 如果原則定義未包含 **mode** 值，則在 Azure PowerShell 中會預設為 `all`，而在 Azure CLI 中會預設為 `null`。 `null` 模式與使用 `indexed` 來支援回溯相容性相同。

建立會強制執行標籤或位置的原則時，應該使用 `indexed`。 雖然並非必要，但它可防止不支援標籤和位置的資源在合規性結果中顯示為不符合規範。 有一個例外，就是**資源群組**和**訂用帳戶**。 原則定義若在資源群組或訂用帳戶上強制執行位置或標籤，就應該將 **mode** 設定為 `all`，並明確地以 `Microsoft.Resources/subscriptions/resourceGroups` 或 `Microsoft.Resources/subscriptions` 類型作為目標。 如需範例，請參閱[模式：標籤 - 範例 #1](../samples/pattern-tags.md)。 如需支援標籤的資源清單，請參閱 [Azure 資源的標籤支援](../../../azure-resource-manager/management/tag-support.md)。

### <a name="resource-provider-modes-preview"></a><a name="resource-provider-modes"></a>資源提供者模式 (預覽)

預覽期間目前支援下列資源提供者模式：

- `Microsoft.ContainerService.Data`，用來管理 [Azure Kubernetes Service](../../../aks/intro-kubernetes.md) 上的許可控制站規則。 使用此資源提供者模式的定義**必須**使用[EnforceRegoPolicy](./effects.md#enforceregopolicy)效果。 此模型即將_淘汰_。
- `Microsoft.Kubernetes.Data`，用來在 Azure 上或外部管理 Kubernetes 叢集。 使用此資源提供者模式的定義會使用 [ _audit_]、[ _deny_] 和 [ _disabled_] 效果。 [EnforceOPAConstraint](./effects.md#enforceopaconstraint)效果的使用已被_取代_。
- `Microsoft.KeyVault.Data`，用來管理 [Azure Key Vault](../../../key-vault/general/overview.md) 中的保存庫和憑證。

> [!NOTE]
> 資源提供者模式只支援內建原則定義，且在預覽期間不支援計劃。

## <a name="metadata"></a>中繼資料

選擇性的 `metadata` 屬性會儲存原則定義的相關資訊。 客戶可以在中定義適用于其組織的任何屬性和值 `metadata` 。 不過，Azure 原則和內建有一些_常用_的屬性。

### <a name="common-metadata-properties"></a>通用中繼資料屬性

- `version`（字串）：追蹤原則定義內容版本的詳細資料。
- `category`（字串）：決定要在哪個類別目錄中顯示原則定義 Azure 入口網站。
- `preview`（布林值）：如果原則定義為_預覽_，則為 True 或 false 旗標。
- `deprecated`（布林值）：如果原則定義已標示為已被_取代_，則為 True 或 false 旗標。

> [!NOTE]
> Azure 原則服務會使用 `version`、`preview` 和 `deprecated` 屬性，將變更層級傳達給內建原則定義或計畫和狀態。 `version` 的格式如下：`{Major}.{Minor}.{Patch}`。 特定狀態 (例如 _已淘汰_或_預覽_) 會附加至 `version` 屬性，或在另一個屬性中附加為**布林值**。 如需 Azure 原則版本內建之方式的詳細資訊，請參閱[內建版本](https://github.com/Azure/azure-policy/blob/master/built-in-policies/README.md)設定。

## <a name="parameters"></a>參數

參數可減少原則定義數量來幫助您簡化原則管理。 將參數想像成就像表單上的欄位一樣 – `name``address`、`city``state`。 這些參數一律保持不變，不過它們的值則會根據填寫表單的個人而有所變更。
在建立原則時，參數也是以相同的方式運作。 藉由在原則定義中納入參數，您便可以針對不同的案例使用不同的值，來重複使用該原則。

> [!NOTE]
> 參數可能會加入至現有的和指派的定義。 新的參數必須包含 **defaultValue** 屬性。 這可避免原則或計畫的現有指派間接地變成無效。

### <a name="parameter-properties"></a>參數屬性

參數有下列在原則定義中使用的屬性：

- `name`：參數的名稱。 由原則規則中的 `parameters` 部署函式使用。 如需詳細資訊，請參閱[使用參數值](#using-a-parameter-value)。
- `type`:判斷參數是**字串**、**陣列**、**物件**、**布林值**、**整數**、**浮點**，還是**日期時間**。
- `metadata`:定義主要由 Azure 入口網站使用的子屬性，以顯示使用者易讀的資訊：
  - `description`:參數用途的說明。 能用來提供可接受值的範例。
  - `displayName`:參數在入口網站中顯示的易記名稱。
  - `strongType`:(選擇性) 透過入口網站指派原則定義時會使用。 提供內容感知清單。 如需詳細資訊，請參閱 [strongType](#strongtype)。
  - `assignPermissions`:(選擇性) 設定為 _true_，讓 Azure 入口網站在原則指派期間建立角色指派。 如果您想要在指派範圍之外指派權限，此屬性會很有用。 原則中的每個角色定義 (或計畫中所有原則中的每個角色定義) 都有一個角色指派。 參數值必須是有效的資源或範圍。
- `defaultValue`:(選擇性) 如果沒有提供值，就在指派中設定參數的值。
  更新已指派的現有原則定義時需要。
- `allowedValues`:(選擇性) 提供參數在指派期間所接受的值陣列。

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

在原則規則中，您可以使用下列 `parameters` 函式語法參考參數：

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

此範例參考[參數屬性](#parameter-properties)中示範的 **allowedLocations** 參數。

### <a name="strongtype"></a>strongType

在 `metadata` 屬性內，您可以使用 **strongType** 在 Azure 入口網站內提供可複選的選項清單。 **strongType** 可以是支援的_資源類型_或允許的值。 若要判斷_資源類型_是否對 **strongType** 有效，請使用 [Get-AzResourceProvider](/powershell/module/az.resources/get-azresourceprovider)。 _資源類型_ **strongType**的格式為 `<Resource Provider>/<Resource Type>` 。 例如： `Microsoft.Network/virtualNetworks/subnets` 。

支援部分不是由 **Get-AzResourceProvider** 傳回的_資源類型_。 這些是：

- `Microsoft.RecoveryServices/vaults/backupPolicies`

**strongType** 的非_資源類型_允許值如下：

- `location`
- `resourceTypes`
- `storageSkus`
- `vmSKUs`
- `existingResourceGroups`

## <a name="definition-location"></a>定義位置

建立方案或原則時，必須指定定義位置。 定義位置必須是管理群組或訂用帳戶。 此位置可決定方案或原則的指派範圍。 資源必須是定義位置階層內的直屬成員或其子系，才可作為指派的目標。

如果定義位置為：

- **訂用帳戶** - 只能將原則指派給該訂用帳戶內的資源。
- **管理群組** - 只能將原則指派給子管理群組與子訂用帳戶內的資源。 如果您打算將原則定義套用至數個訂用帳戶，則位置必須是包含訂閱的管理群組。

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

對於 **less**、**lessOrEquals**、**greater** 和 **greaterOrEquals**，如果屬性類型不符合條件類型，就會擲回錯誤。 字串比較是使用 `InvariantCultureIgnoreCase` 進行。

使用 **like** 和 **notLike** 條件時，您可以在值中提供 `*` 萬用字元。
值不應包含多個 `*` 萬用字元。

使用 **match** 和 **notMatch** 條件時，請提供 `#` 來比對數字、`?` 來比對字母、`.` 來比對任何字元，以及任何其他字元來比對該實際字元。 **Match**和**notMatch**會區分大小寫，而所有其他評估_stringValue_的條件則不區分大小寫。 不會區分大小寫的替代項目，可在 **matchInsensitively** 和 **notMatchInsensitively** 中取得。

在 **\[\*\] 別名**陣列欄位值中，陣列中的每個元素都會搭配元素之間的邏輯 **and** 進行個別評估。 如需詳細資訊，請參閱[評估 \[\*\] 別名](../how-to/author-policies-for-arrays.md#evaluating-the--alias)。

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
  - 其中 **\<tagName\>** 是要驗證條件的標記名稱。
  - 範例：`tags['Acct.CostCenter']`，其中 **Acct.CostCenter** 是標籤的名稱。
- `tags['''<tagName>''']`
  - 此括號語法能透過以雙引號進行逸出，來支援具有單引號的標籤名稱。
  - 其中 **' \<tagName\> '** 是要驗證條件的標記名稱。
  - 範例：`tags['''My.Apostrophe.Tag''']`，其中 **'My.Apostrophe.Tag'** 是標籤的名稱。
- 屬性別名 - 如需清單，請參閱[別名](#aliases)。

> [!NOTE]
> `tags.<tagName>`、`tags[tagName]` 和 `tags[tag.with.dots]` 都仍是可接受的宣告標籤欄位方式。 不過，建議的運算式為上面所列的運算式。

#### <a name="use-tags-with-parameters"></a>搭配參數使用標籤

可以將參數值傳遞到標籤欄位。 將參數傳遞到標籤欄位能在原則指派期間提升原則定義的彈性。

在下列範例中，`concat` 被用來建立針對名稱為 **tagName** 參數值之標籤的標籤欄位查閱。 如果該標籤不存在，系統會使用**修改**效果，透過 `resourcegroup()` 查閱函式使用在已稽核的資源父資源群組上具有相同名稱的標籤值來新增該標籤。

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
> 如果「範本函式」的結果為錯誤，則原則評估會失敗。 失敗的評估隱含著**拒絕**的意思。 如需詳細資訊，請參閱[避免範本錯誤](#avoiding-template-failures)。 使用 **DoNotEnforce** 的 [enforcementMode](./assignment-structure.md#enforcement-mode)，以防止在測試和驗證新的原則定義時，由於新的或更新的資源評估失敗而受到影響。

#### <a name="value-examples"></a>Value 範例

此原則規則範例使用 **value** 來將 `resourceGroup()` 函式和傳回的 **name** 屬性與 `*netrg` 的 **like** 條件比較。 規則會拒絕名稱結尾是 `*netrg` 的任何資源群組中，任何不屬於 `Microsoft.Network/*`**type** 的任何資源。

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

此原則規則範例使用 **value** 來檢查多個巢狀函式的結果是否**等於** `true`。 規則會拒絕沒有至少三個標籤的任何資源。

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

#### <a name="avoiding-template-failures"></a>避免範本失敗

在 **value** 中使用「範本函式」允許許多複雜的巢狀函式。 如果「範本函式」的結果為錯誤，則原則評估會失敗。 失敗的評估隱含著**拒絕**的意思。 在特定情況下失敗的 **value** 範例：

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

上述範例原則規則使用 [substring()](../../../azure-resource-manager/templates/template-functions-string.md#substring)，將**名稱**的前三個字元與 **abc** 進行比較。 如果**名稱**少於三個字元，`substring()` 函式會導致錯誤。 此錯誤會導致原則變成**拒絕**效果。

相反地，請使用 [if()](../../../azure-resource-manager/templates/template-functions-logical.md#if) 函式來檢查**名稱**的前三個字元是否等於 **abc**，不允許**名稱**少於 3 個字元，這會造成錯誤：

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

使用已修訂的原則規則，`if()` 會先檢查**名稱**的長度，然後嘗試在少於三個字元的值上取得 `substring()`。 如果**名稱**太短，則會改為傳回「不是以 abc 開頭」一值，並與 **abc** 進行比較。 簡短名稱開頭不是 **abc** 的資源仍會使原則規則失敗，但在評估期間不會再造成錯誤。

### <a name="count"></a>Count

計算資源承載中陣列有多少成員符合條件運算式的條件，可以使用 **count** 運算式來構成。 常見的案例是檢查「其中至少一個」、「只有一個」、「全部」或「沒有一個」陣列成員符合條件。 **count** 會評估條件運算式的每個 [\[\*\] 別名](#understanding-the--alias) 陣列成員，並加總 _true_ 結果，然後再與運算式運算子進行比較。 **計數**運算式最多可以加上三次至單一**policyRule**定義。

**count** 運算式的結構如下：

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

下列屬性與 **count** 搭配使用：

- **count.field** (必要)：包含陣列的路徑，而且必須是陣列別名。 如果陣列遺漏，則會將運算式評估為 _false_，而不考慮條件運算式。
- **count.where** (選擇性)：此條件運算式會個別評估 **count.field** 的每個 [\[\*\] 別名](#understanding-the--alias)陣列成員。 如果未提供此屬性，則會將路徑為 ' field ' 的所有陣列成員評估為_true_。 任何[條件](../concepts/definition-structure.md#conditions)都可以在此屬性內使用。
  [邏輯運算子](#logical-operators)可以在此屬性內使用，以建立複雜的評估需求。
- **\<condition\>**（必要）：值會與符合計數的專案數進行比較（ **where**條件運算式）。 應該使用數值[條件](../concepts/definition-structure.md#conditions)。

#### <a name="count-examples"></a>計數範例

範例 1：檢查陣列是否空白

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]"
    },
    "equals": 0
}
```

範例 2：檢查是否只有一個陣列成員符合條件運算式

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

範例 3：檢查是否至少有一個陣列成員符合條件運算式

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

範例 4：檢查是否所有物件陣列成員都符合條件運算式

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

範例5：檢查至少有一個陣列成員符合條件運算式中的多個屬性

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

Azure 原則支援下列類型的效果：

- **Append**：會在要求中加入一組已定義的欄位
- **Audit**：會在活動記錄中產生警告事件，但不會讓要求失敗
- **AuditIfNotExists**：如果相關資源不存在，則會在活動記錄中產生警告事件
- **Deny**：會在活動記錄中產生事件，並讓要求失敗
- **DeployIfNotExists**：如果相關資源不存在，則會部署該資源
- **Disabled**：不會評估資源是否符合原則規則的規範
- **EnforceOPAConstraint** (預覽)：針對 Azure 上的自我管理 Kubernetes 叢集，設定 Open Policy Agent 許可控制站與 Gatekeeper v3 (預覽)
- **EnforceRegoPolicy** (預覽)：在 Azure Kubernetes Service 中，設定 Open Policy Agent 許可控制站與 Gatekeeper v2 (預覽)
- **Modify**：新增、更新或移除資源中已定義的標籤

如需每個效果的完整詳細資料、評估順序、屬性和範例，請參閱[了解 Azure 原則效果](effects.md)。

### <a name="policy-functions"></a>原則函式

除了下列函式和使用者定義函式，所有的 [Resource Manager 範本函式](../../../azure-resource-manager/templates/template-functions.md)都可在原則規則內使用：

- copyIndex()
- deployment()
- list*
- newGuid()
- pickZones()
- providers()
- reference()
- resourceId()
- variables()

> [!NOTE]
> 在 **deployIfNotExists** 原則定義中，仍可在其範本部署的 `details.deployment.properties.template` 部分內使用這些函式。

下列函式可在原則規則中使用，但不同于在 Azure Resource Manager 範本（ARM 範本）中使用：

- `utcNow()`-與 ARM 範本不同的是，這個屬性可以在_defaultValue_以外的地方使用。
  - 以通用 ISO 8601 日期時間格式 'yyyy-MM-ddTHH:mm:ss.fffffffZ' 傳回設定為目前日期和時間的字串

下列函式僅適用於原則規則：

- `addDays(dateTime, numberOfDaysToAdd)`
  - **dateTime**：[必要] 字串 - 通用 ISO 8601 日期時間 'yyyy-MM-ddTHH:mm:ss.fffffffZ' 中的字串
  - **numberOfDaysToAdd**：[必要] 整數 - 要新增的天數
- `field(fieldName)`
  - **fieldName**：[必要] 字串 - 要擷取的 [field](#fields) 名稱
  - 從 If 條件所評估的資源中傳回該欄位的值
  - `field` 主要是與 **AuditIfNotExists** 和 **DeployIfNotExists** 搭配使用，以參考所評估資源上的欄位。 如需此用法的範例，請參閱 [DeployIfNotExists 範例](effects.md#deployifnotexists-example)。
- `requestContext().apiVersion`
  - 傳回已觸發原則評估的要求 API 版本 (範例：`2019-09-01`)。
    此值是在建立/更新資源時，用於放置/修補要求以進行評估的 API 版本。 在對現有資源進行合規性評估時，一律使用最新的 API 版本。
  
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

- 適用於 Visual Studio Code 的 Azure 原則延伸模組 (建議)

  使用[適用於 Visual Studio Code 的 Azure 原則延伸模組](../how-to/extension-for-vscode.md)來檢視和探索資源屬性的別名。

  :::image type="content" source="../media/extension-for-vscode/extension-hover-shows-property-alias.png" alt-text="適用於 Visual Studio Code 的 Azure 原則延伸模組" border="false":::

- Azure Resource Graph

  使用 `project` 運算子來顯示資源的**別名**。

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
  GET https://management.azure.com/providers/?api-version=2019-10-01&$expand=resourceTypes/aliases
  ```

### <a name="understanding-the--alias"></a>了解 [*] 別名

許多可用的別名都有會一個顯示為「正常」名稱的版本，和另一個附加 **\[\*\]** 的版本。 例如：

- `Microsoft.Storage/storageAccounts/networkAcls.ipRules`
- `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]`

「正常」別名會將欄位表示為單一值。 當整個值組必須完全依照定義，不多也不少時，此欄位適用於完全相符的比較案例。

**\[\*\]** 別名可以比較陣列中每個元素的值，以及每個專案的特定屬性。 這個方法可讓您比較 'if none of'、'if any of' 或 'if all of' 案例的元素屬性。 若為更複雜的案例，請使用 [count](#count) 條件運算式。 使用 **ipRules\[\*\]** ，範例會驗證每個_動作_是否為_拒絕_，但不會擔心有多少規則存在或 IP _值_是什麼。
此範例規則會檢查 **ipRules\[\*\].value** 是否符合 **10.0.4.1**，並僅在找不到至少一個相符項時，才套用 **effectType**：

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

如需詳細資訊，請參閱[評估 [\*] 別名](../how-to/author-policies-for-arrays.md#evaluating-the--alias)。

## <a name="next-steps"></a>後續步驟

- 請參閱[計畫定義結構](./initiative-definition-structure.md)
- 在 [Azure 原則範例](../samples/index.md)檢閱範例。
- 檢閱[了解原則效果](effects.md)。
- 了解如何[以程式設計方式建立原則](../how-to/programmatically-create.md)。
- 了解如何[取得合規性資料](../how-to/get-compliance-data.md)。
- 了解如何[補救不符合規範的資源](../how-to/remediate-resources.md)。
- 透過[使用 Azure 管理群組來組織資源](../../management-groups/overview.md)來檢閱何謂管理群組。
