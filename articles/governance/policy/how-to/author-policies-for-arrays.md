---
title: 在資源上撰寫陣列屬性的原則
description: 瞭解如何使用陣列參數和陣列語言運算式、評估 [*] 別名，以及附加具有 Azure 原則定義規則的元素。
ms.date: 11/26/2019
ms.topic: how-to
ms.openlocfilehash: 991d159f6444133d902382bc9ca43bc2acd201e2
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79280660"
---
# <a name="author-policies-for-array-properties-on-azure-resources"></a>在 Azure 資源上撰寫陣列屬性的原則

Azure Resource Manager 屬性通常會定義為字串和布林值。 當存在一對多關聯性時，複雜屬性會改定義為數組。 在 Azure 原則中，陣列會以數種不同的方式使用：

- [定義參數](../concepts/definition-structure.md#parameters)的類型，可提供多個選項
- 使用或**notIn** **中**之條件的[原則規則](../concepts/definition-structure.md#policy-rule)的一部分
- 評估[\[\*\] 別名](../concepts/definition-structure.md#understanding-the--alias)的原則規則之一部分：
  - 如**None**、 **Any**或**All**等案例
  - 具有**計數**的複雜案例
- 在取代或加入現有陣列的[附加效果](../concepts/effects.md#append)中

本文涵蓋 Azure 原則的每個使用方式，並提供數個範例定義。

## <a name="parameter-arrays"></a>參數陣列

### <a name="define-a-parameter-array"></a>定義參數陣列

將參數定義為數組，可在需要一個以上的值時，允許原則彈性。
此原則定義允許**allowedLocations**參數的任何單一位置，並預設為_eastus2_：

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

As**類型**為_字串_，指派原則時只能設定一個值。 如果指派此原則，則範圍內的資源只能在單一 Azure 區域中使用。 大部分的原則定義都需要允許已核准的選項清單，例如允許_eastus2_、 _eastus_和_westus2_。

若要建立原則定義以允許多個選項，請使用_陣列_**類型**。 相同的原則可以改寫如下：

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
> 一旦儲存原則定義之後，就無法變更參數上的**type**屬性。

這個新的參數定義會在原則指派期間接受一個以上的值。 定義陣列屬性**allowedValues**之後，指派期間可用的值會進一步限制為預先定義的選項清單。 **AllowedValues**的使用是選擇性的。

### <a name="pass-values-to-a-parameter-array-during-assignment"></a>指派期間將值傳遞給參數陣列

透過 Azure 入口網站指派原則時，**類型**_陣列_的參數會顯示為單一文字方塊。 提示顯示「使用;來分隔值。 （例如倫敦;紐約）」。 若要將_eastus2_、 _eastus_和_westus2_的允許位置值傳遞給參數，請使用下列字串：

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

- Azure CLI：命令[az policy 指派 create](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) with parameter **params**
- Azure PowerShell： Cmdlet [get-azpolicyassignment](/powershell/module/az.resources/New-Azpolicyassignment)與參數**PolicyParameter**
- REST API：在_PUT_ [create](/rest/api/resources/policyassignments/create)作業中做為要求主體的一部分，作為 properties 屬性的值 **。**

## <a name="policy-rules-and-arrays"></a>原則規則和陣列

### <a name="array-conditions"></a>陣列條件

可搭配使用_陣列_
**類型**參數的原則規則[條件](../concepts/definition-structure.md#conditions)僅限於 `in` 和 `notIn`。 採用下列原則定義，並以條件 `equals` 做為範例：

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

- 「原則 ' {GUID} ' 無法參數化，因為發生驗證錯誤。 請檢查是否已正確定義原則參數。 語言運算式 ' [參數（' allowedLocations '）] ' 的內部例外狀況 ' 評估結果為類型 ' Array '，預期的類型為 ' String '。 '。

預期的條件**類型**`equals` 為_字串_。 由於**allowedLocations**是定義為**類型**_陣列_，原則引擎會評估語言運算式，並擲回錯誤。 使用 `in` 和 `notIn` 條件時，原則引擎會預期語言運算式中的**類型**_陣列_。 若要解決此錯誤訊息，請將 `equals` 變更為 `in` 或 `notIn`。

### <a name="evaluating-the--alias"></a>評估 [*] 別名

具有附加至其名稱之 **\[\*\]** 的別名，表示該**類型**為_陣列_。 **\[\*\]** 可讓您個別評估陣列的每個專案，而不是評估整個陣列的值，而是以邏輯和其間的方式來計算陣列的每個元素。 在下列三個標準案例中，每個專案評估適用于： _None_、 _Any_或_All_元素相符。 針對複雜的案例，請使用[count](../concepts/definition-structure.md#count)。

只有當**if**規則評估為 true 時，原則**引擎才會觸發中的** **效果**。
這一點很重要，請務必瞭解 **\[\*\]** 評估陣列的每個個別元素的方式。

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

下列案例資料表的**ipRules**陣列如下所示：

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
|`{<field>,"notEquals":"127.0.0.1"}` |這裡 |無相符 |一個陣列元素會評估為 false （127.0.0.1！ = 127.0.0.1），另一個為 true （127.0.0.1！ = 192.168.1.1），因此**notEquals**條件為_false_且不會觸發效果。 |
|`{<field>,"notEquals":"10.0.4.1"}` |原則效果 |無相符 |這兩個陣列元素會評估為 true （10.0.4.1！ = 127.0.0.1 和10.0.4.1！ = 192.168.1.1），因此**notEquals**條件為_true_ ，且會觸發效果。 |
|`"not":{<field>,"notEquals":"127.0.0.1" }` |原則效果 |一或多個相符項 |一個陣列元素會評估為 false （127.0.0.1！ = 127.0.0.1），另一個為 true （127.0.0.1！ = 192.168.1.1），因此**notEquals**條件為_false_。 邏輯運算子會評估為 true （**不**是_false_），因此會觸發效果。 |
|`"not":{<field>,"notEquals":"10.0.4.1"}` |這裡 |一或多個相符項 |這兩個陣列元素會評估為 true （10.0.4.1！ = 127.0.0.1 和10.0.4.1！ = 192.168.1.1），因此**notEquals**條件為_true_。 邏輯運算子會評估為 false （**不**是_true_），因此不會觸發效果。 |
|`"not":{<field>,"Equals":"127.0.0.1"}` |原則效果 |不是全部相符 |一個陣列元素會評估為 true （127.0.0.1 = = 127.0.0.1），另一個為 false （127.0.0.1 = = 192.168.1.1），因此**Equals**條件為_false_。 邏輯運算子會評估為 true （**不**是_false_），因此會觸發效果。 |
|`"not":{<field>,"Equals":"10.0.4.1"}` |原則效果 |不是全部相符 |這兩個陣列元素都評估為 false （10.0.4.1 = = 127.0.0.1 和 10.0.4.1 = = 192.168.1.1），因此**Equals**條件為_false_。 邏輯運算子會評估為 true （**不**是_false_），因此會觸發效果。 |
|`{<field>,"Equals":"127.0.0.1"}` |這裡 |全部符合 |一個陣列元素會評估為 true （127.0.0.1 = = 127.0.0.1），另一個為 false （127.0.0.1 = = 192.168.1.1），因此**Equals**條件為_false_且不會觸發效果。 |
|`{<field>,"Equals":"10.0.4.1"}` |這裡 |全部符合 |這兩個陣列元素都評估為 false （10.0.4.1 = = 127.0.0.1 和 10.0.4.1 = = 192.168.1.1），因此**Equals**條件為_false_且不會觸發效果。 |

## <a name="the-append-effect-and-arrays"></a>附加效果和陣列

[附加效果](../concepts/effects.md#append)的行為會因 [**詳細資料] 欄位**是 **\[\*\]** 別名而有所不同。

- 當不是 **\[\*\]** 別名時，append 會將整個陣列取代為**value**屬性
- 當 **\[\*\]** 別名時，append 會將**value**屬性加入至現有的陣列，或建立新的陣列。

如需詳細資訊，請參閱[附加範例](../concepts/effects.md#append-examples)。

## <a name="next-steps"></a>後續步驟

- 如[Azure 原則範例](../samples/index.md)，請參閱範例。
- 檢閱 [Azure 原則定義結構](../concepts/definition-structure.md)。
- 檢閱[了解原則效果](../concepts/effects.md)。
- 瞭解如何以程式設計[方式建立原則](programmatically-create.md)。
- 瞭解如何[補救不符合規範的資源](remediate-resources.md)。
- 透過[使用 Azure 管理群組來組織資源](../../management-groups/overview.md)來檢閱何謂管理群組。
