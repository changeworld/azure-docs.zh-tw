---
title: 資源陣列屬性編寫原則
description: 瞭解如何使用陣列參數和陣列語言運算式、評估 [*] 別名，以及附加具有 Azure 原則定義規則的元素。
ms.date: 05/20/2020
ms.topic: how-to
ms.openlocfilehash: f3d30f76d555386e5ab8041a0b8cc82b5b60e28e
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684250"
---
# <a name="author-policies-for-array-properties-on-azure-resources"></a>對於 Azure 資源編寫陣列屬性的原則

Azure Resource Manager 屬性通常會定義為字串和布林值。 存在一對多關聯性時，複雜屬性會改為定義為陣列。 在 Azure 原則中，能夠以多種不同的方式使用陣列：

- [定義參數](../concepts/definition-structure.md#parameters)的類型，可提供多個選項
- 使用條件 **in** 或 **notIn** 的[原則規則](../concepts/definition-structure.md#policy-rule)一部分
- 評估 [\[\*\] 別名](../concepts/definition-structure.md#understanding-the--alias)的原則規則，將會評估：
  - **無**、**任何**或**全部**的案例
  - **計數**的複雜案例
- 在[附加效果](../concepts/effects.md#append)中取代或加入至現有的陣列

本文涵蓋 Azure 原則的每個使用方式，並提供數個範例定義。

## <a name="parameter-arrays"></a>參數陣列

### <a name="define-a-parameter-array"></a>定義參數陣列

將參數定義為陣列，可在需要一個以上的值時，允許原則彈性。
此原則定義允許 **allowedLocations** 參數的任何單一位置，並預設為 _eastus2_：

```json
"parameters": {
    "allowedLocations": {
        "type": "string",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2"
    }
}
```

由於**類型**為_字串_，因此指派原則時只能設定一個值。 如果指派此原則，則範圍內的資源只能在單一 Azure 區域中使用。 大部分的原則定義都必須允許已核准的選項清單，例如允許 _eastus2_、_eastus_ 和 _westus2_。

若要建立原則定義以允許多個選項，請使用「陣列」**類型**。 相同的原則可以改寫如下：

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2",
        "allowedValues": [
            "eastus2",
            "eastus",
            "westus2"
        ]

    }
}
```

> [!NOTE]
> 一旦儲存原則定義之後，就無法變更參數上的**類型**屬性。

這個新的參數定義會在原則指派期間接受一個以上的值。 陣列屬性 **allowedValues** 定義完畢後，指派期間可用的值會進一步限制為預先定義的選項清單。 使用 **allowedValues** 為選擇性。

### <a name="pass-values-to-a-parameter-array-during-assignment"></a>於指派期間將值傳遞給參數陣列

透過 Azure 入口網站指派原則時，**類型**「陣列」的參數會顯示為單一文字方塊。 提示顯示「請使用 ; 來分隔值。 (例如倫敦;紐約)」。 若要將 _eastus2_、_eastus_ 和 _westus2_ 允許的位置值傳遞給參數，請使用下列字串：

`eastus2;eastus;westus2`

使用 Azure CLI、Azure PowerShell 或 REST API 時，參數值的格式會不同。 這些值會透過同時包含參數名稱的 JSON 字串傳遞。

```json
{
    "allowedLocations": {
        "value": [
            "eastus2",
            "eastus",
            "westus2"
        ]
    }
}
```

若要將此字串與每個 SDK 搭配使用，請使用下列命令：

- Azure CLI：命令 [az policy assignment create](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) 搭配參數 **params**
- Azure PowerShell：Cmdlet [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) 搭配參數 **PolicyParameter**
- REST API：_PUT_ 中屬於要求本文的 **properties.parameters** 屬性值 [create](/rest/api/resources/policyassignments/create)作業

## <a name="policy-rules-and-arrays"></a>原則規則和陣列

### <a name="array-conditions"></a>陣列條件

參數的「陣列」
**類型**可使用的原則規則[條件](../concepts/definition-structure.md#conditions)僅限於 `in` 和 `notIn`。 採用下列原則定義，並以條件 `equals` 做為範例：

```json
{
  "policyRule": {
    "if": {
      "not": {
        "field": "location",
        "equals": "[parameters('allowedLocations')]"
      }
    },
    "then": {
      "effect": "audit"
    }
  },
  "parameters": {
    "allowedLocations": {
      "type": "Array",
      "metadata": {
        "description": "The list of allowed locations for resources.",
        "displayName": "Allowed locations",
        "strongType": "location"
      }
    }
  }
}
```

嘗試透過 Azure 入口網站建立此原則定義會導致錯誤，例如此錯誤訊息：

- 「因為發生驗證錯誤，所以無法將原則 '{GUID}' 參數化。 請檢查是否已正確定義原則參數。 內部例外狀況 '語言運算式 '[parameters('allowedLocations')] 的評估結果為 'Array' 類型，而預期類型為 'String'。'」。

條件 `equals` 的預期**類型**是_字串_。 由於 **allowedLocations** 定義為**類型**_陣列_，原則引擎會評估語言運算式，並擲回錯誤。 有了 `in` 和 `notIn` 條件，原則引擎就會預期語言運算式中的**類型**_陣列_。 若要解決此錯誤訊息，請將 `equals` 變更為 `in` 或 `notIn`。

### <a name="evaluating-the--alias"></a>評估 [*] 別名

具有 **\[\*\]** 附加至其名稱的別名，表示**類型**是_陣列_。 **\[\*\]** 可讓您個別評估陣列的每個元素，並以邏輯方式 AND 評估，而不是評估整個陣列的值。 有三個標準案例，每個項目評估在中都很有用：_無_、_任何_或_全部_元素相符。 針對複雜的案例，請使用[計數](../concepts/definition-structure.md#count)。

只有在 **if** 規則評估為 true 時，原則引擎會在 **then** 中觸發**效果**。
必須瞭解 **\[\*\]** 評估陣列的每個個別元素所用的方式。

下列案例資料表的範例原則規則：

```json
"policyRule": {
    "if": {
        "allOf": [
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "exists": "true"
            },
            <-- Condition (see table below) -->
        ]
    },
    "then": {
        "effect": "[parameters('effectType')]"
    }
}
```

下列情節資料表的 **ipRules** 陣列如下所示：

```json
"ipRules": [
    {
        "value": "127.0.0.1",
        "action": "Allow"
    },
    {
        "value": "192.168.1.1",
        "action": "Allow"
    }
]
```

針對下列每個條件範例，將 `<field>` 取代為 `"field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value"`。

下列結果是條件和範例原則規則和上述現有值陣列的組合結果：

|條件 |成果 | 狀況 |說明 |
|-|-|-|-|
|`{<field>,"notEquals":"127.0.0.1"}` |不執行任何動作 |無相符 |一個陣列元素會評估為 false (127.0.0.1 != 127.0.0.1)，另一個為 true (127.0.0.1 != 192.168.1.1)，因此 **notEquals** 條件是 _false_ 且不會觸發效果。 |
|`{<field>,"notEquals":"10.0.4.1"}` |原則效果 |無相符 |這兩個陣列元素會評估為 true (10.0.4.1 != 127.0.0.1 and 10.0.4.1 != 192.168.1.1)，因此 **notEquals** 條件是 _true_ 並觸發效果。 |
|`"not":{<field>,"notEquals":"127.0.0.1" }` |原則效果 |一或多個作業 |一個陣列元素會評估為 false (127.0.0.1 != 127.0.0.1)，另一個為 true (127.0.0.1 != 192.168.1.1)，因此 **notEquals** 條件是 _false_。 邏輯運算子會評估為 true (**not** _false_)，因此會觸發效果。 |
|`"not":{<field>,"notEquals":"10.0.4.1"}` |不執行任何動作 |一或多個作業 |這兩個陣列元素會評估為 true (10.0.4.1 != 127.0.0.1 and 10.0.4.1 != 192.168.1.1)，因此 **notEquals** 條件是 _true_。 邏輯運算子會評估為 false (**not** _true)_ ，因此不會觸發效果。 |
|`"not":{<field>,"Equals":"127.0.0.1"}` |原則效果 |不是全部相符 |一個陣列元素會評估為 true (127.0.0.1 == 127.0.0.1)，另一個為 false (127.0.0.1 == 192.168.1.1)，因此 **Equals** 條件是 _false_。 邏輯運算子會評估為 true (**not** _false_)，因此會觸發效果。 |
|`"not":{<field>,"Equals":"10.0.4.1"}` |原則效果 |不是全部相符 |這兩個陣列元素都評估為 false (10.0.4.1 == 127.0.0.1 和 10.0.4.1 == 192.168.1.1)，因此 **Equals** 條件為 _false_。 邏輯運算子會評估為 true (**not** _false_)，因此會觸發效果。 |
|`{<field>,"Equals":"127.0.0.1"}` |不執行任何動作 |全部相符 |一個陣列元素會評估為 true (127.0.0.1 == 127.0.0.1)，另一個為 false (127.0.0.1 == 192.168.1.1)，因此 **Equals** 條件是 _false_ 且不會觸發效果。 |
|`{<field>,"Equals":"10.0.4.1"}` |不執行任何動作 |全部相符 |這兩個陣列元素都評估為 false (10.0.4.1 == 127.0.0.1 和 10.0.4.1 == 192.168.1.1)，因此 **Equals** 條件是 _false_ 且不會觸發效果。 |

## <a name="the-append-effect-and-arrays"></a>附加效果和陣列

[附加效果](../concepts/effects.md#append)的行為會根據 **details.field** 是否為 **\[\*\]** 別名而有所不同。

- 不是 **\[\*\]** 別名時，append 會將整個陣列取代為**值**屬性
- 是 **\[\*\]** 別名時，append 會將**值**屬性加入現有的陣列中，或建立新的陣列

如需詳細資訊，請參閱[附加範例](../concepts/effects.md#append-examples)。

## <a name="next-steps"></a>後續步驟

- 在 [Azure 原則範例](../samples/index.md)檢閱範例。
- 檢閱 [Azure 原則定義結構](../concepts/definition-structure.md)。
- 檢閱[了解原則效果](../concepts/effects.md)。
- 了解如何[以程式設計方式建立原則](programmatically-create.md)。
- 瞭解如何[補救不符合規範的資源](remediate-resources.md)。
- 透過[使用 Azure 管理群組來組織資源](../../management-groups/overview.md)來檢閱何謂管理群組。
