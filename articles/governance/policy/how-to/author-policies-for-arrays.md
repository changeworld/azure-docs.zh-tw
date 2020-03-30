---
title: 資源上的陣列屬性的編寫策略
description: 瞭解如何使用陣列參數和陣列語言運算式、計算 +別名以及使用 Azure 策略定義規則追加元素。
ms.date: 11/26/2019
ms.topic: how-to
ms.openlocfilehash: 991d159f6444133d902382bc9ca43bc2acd201e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280660"
---
# <a name="author-policies-for-array-properties-on-azure-resources"></a>為 Azure 資源上的陣列屬性編寫策略

Azure 資源管理器屬性通常定義為字串和布林。 當存在一對多關聯性時，複雜屬性將被定義為數組。 在 Azure 策略中，陣列以幾種不同的方式使用：

- [定義參數](../concepts/definition-structure.md#parameters)的類型，以提供多個選項
- 策略[規則的](../concepts/definition-structure.md#policy-rule)一部分，使用**或 in** **中**的條件
- 策略規則的一部分，用於評估要評估的[\[\*\]別名](../concepts/definition-structure.md#understanding-the--alias)：
  - 方案，如 **"無**"、**任意**或**全部**
  - 具有**計數**的複雜方案
- 在要替換或添加到現有陣列的附加[效果](../concepts/effects.md#append)中

本文介紹 Azure 策略的每次使用，並提供了幾個示例定義。

## <a name="parameter-arrays"></a>參數陣列

### <a name="define-a-parameter-array"></a>定義參數陣列

將參數定義為數組允許在需要多個值時具有策略靈活性。
此策略定義允許允許的參數的任何單個位置**定位**和預設值到_Eastus2：_

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

由於**類型**是_字串_，因此在分配策略時只能設置一個值。 如果分配了此策略，則範圍中的資源僅允許在單個 Azure 區域內。 大多數政策定義需要允許一個已批准的選項清單，例如允許_東2、__東和__西烏斯2。_

要創建策略定義以允許多個選項，請使用_陣列_**類型**。 相同的策略可以重寫如下：

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
> 保存策略定義後，無法更改參數上的**類型**屬性。

此新參數定義在策略分配期間獲取多個值。 定義了陣列屬性**的值**後，分配期間可用的值將進一步限制為預定義的選項清單。 使用**允許的值**是可選的。

### <a name="pass-values-to-a-parameter-array-during-assignment"></a>在分配期間將值傳遞給參數陣列

通過 Azure 門戶分配策略時，**類型**_陣列_的參數顯示為單個文字方塊。 提示說"使用;以分隔值。 （例如倫敦;紐約）"。 要將_東2、__東和__西烏斯2_的允許位置值傳遞給參數，請使用以下字串：

`eastus2;eastus;westus2`

使用 Azure CLI、Azure PowerShell 或 REST API 時，參數值的格式不同。 這些值通過 JSON 字串傳遞，該字串還包括參數的名稱。

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

要將此字串用於每個 SDK，請使用以下命令：

- Azure CLI：使用參數**參數**創建命令[az 策略分配](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create)
- Azure 電源外殼：使用參數**策略參數**的 Cmdlet[新策略分配](/powershell/module/az.resources/New-Azpolicyassignment)
- REST API：在_PUT_中[創建](/rest/api/resources/policyassignments/create)操作作為請求正文的一部分作為**屬性的值。**

## <a name="policy-rules-and-arrays"></a>策略規則和陣列

### <a name="array-conditions"></a>陣列條件


_陣列_**類型的**參數可以使用的策略規則`in`[條件](../concepts/definition-structure.md#conditions)僅限於 和`notIn`。 以以下策略定義為例，以`equals`條件為例：

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

嘗試通過 Azure 門戶創建此策略定義會導致錯誤，如此錯誤訊息：

- 由於驗證錯誤，無法對策略'{GUID]"進行參數化。 請檢查策略參數是否正確定義。 內部異常"語言運算式的"[參數（允許的位置'）"的計算結果"鍵入"Array"，預期類型為"String"。

預期`equals`條件**類型**是_字串_。 由於**允許的位置**定義為**類型**_陣列_，策略引擎將計算語言運算式並引發錯誤。 使用`in`和`notIn`條件時，策略引擎將期望語言運算式中的**類型**_陣列_。 要解決此問題消息，請更改為`equals`或`in``notIn`。

### <a name="evaluating-the--alias"></a>評估 *別名

** \[ \*** 附加到其名稱的別名表示**類型**是_陣列_。 與其計算整個陣列的值，**\[\*** 不如單獨計算陣列的每個元素，並在它們之間使用邏輯 AND。 每個專案評估有三個標準方案在以下方面很有用：_無_、_任何或__所有_元素匹配。 對於複雜方案，請使用[count](../concepts/definition-structure.md#count)。

**僅當****if**規則計算為 true 時，策略引擎才會觸發**效果**。
在計算陣列的每個單個元素的**\[\*** 上下文中，瞭解這一事實非常重要。

下面的方案表的示例策略規則：

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

**ipRules**陣列如下，用於下面的方案表：

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

對於下面的每個條件示例，請`<field>`替換為`"field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value"`。

以下結果是條件與示例策略規則以及上述現有值陣列的組合的結果：

|條件 |成果 | 狀況 |說明 |
|-|-|-|-|
|`{<field>,"notEquals":"127.0.0.1"}` |Nothing |無匹配 |一個陣列元素計算為 false （127.0.0.1 ！= 127.0.0.1）， 1 個元素為 true （127.0.0.1 ！= 192.168.1.1），因此**不相等**條件_為 false，_ 並且不會觸發效果。 |
|`{<field>,"notEquals":"10.0.4.1"}` |策略效應 |無匹配 |兩個陣列元素都計算為 true （10.0.4.1 ！= 127.0.0.1 和 10.0.4.1 ！= 192.168.1.1），因此**不相等**條件_為 true_並觸發效果。 |
|`"not":{<field>,"notEquals":"127.0.0.1" }` |策略效應 |一個或多個匹配項 |一個陣列元素計算為 false （127.0.0.1 ！= 127.0.0.1）， 1 個元素為 true （127.0.0.1 ！= 192.168.1.1），因此**不相等**條件_為 false_。 邏輯運算子評估為 true （**而不是** _false），_ 因此將觸發效果。 |
|`"not":{<field>,"notEquals":"10.0.4.1"}` |Nothing |一個或多個匹配項 |兩個陣列元素都計算為 true （10.0.4.1 ！= 127.0.0.1 和 10.0.4.1 ！= 192.168.1.1），因此**不相等**條件_為 true_。 邏輯運算子評估為 false （**不** _true），_ 因此不會觸發效果。 |
|`"not":{<field>,"Equals":"127.0.0.1"}` |策略效應 |並非所有匹配項 |一個陣列元素計算為 true （127.0.0.1 = 127.0.0.1）， 一個計算為 false （127.0.0.1 = 192.168.1.1.1），因此**Equals**條件_為 false_。 邏輯運算子評估為 true （**而不是** _false），_ 因此將觸發效果。 |
|`"not":{<field>,"Equals":"10.0.4.1"}` |策略效應 |並非所有匹配項 |兩個陣列元素都計算為 false （10.0.4.1 = 127.0.0.1 和 10.0.4.1 = 192.168.1.1），因此**Equals**條件_為 false_。 邏輯運算子評估為 true （**而不是** _false），_ 因此將觸發效果。 |
|`{<field>,"Equals":"127.0.0.1"}` |Nothing |所有匹配項 |一個陣列元素計算為 true （127.0.0.1 = 127.0.0.1）， 一個計算為 false （127.0.0.1 = 192.168.1.1.1），因此**Equals**條件_為 false，_ 並且不會觸發效果。 |
|`{<field>,"Equals":"10.0.4.1"}` |Nothing |所有匹配項 |兩個陣列元素都計算為 false （10.0.4.1 = 127.0.0.1 和 10.0.4.1 = 192.168.1.1.1），因此**Equals**條件_為 false，_ 並且不會觸發效果。 |

## <a name="the-append-effect-and-arrays"></a>追加效果和陣列

根據**詳細資訊.field**是否為**\[\*** 別名，[追加效果](../concepts/effects.md#append)的行為會有所不同。

- 當不是別名**\[\*** 時，追加將整個陣列替換為**值**屬性
- 當別名**\[\*** 時，追加將**值**屬性添加到現有陣列或創建新陣列

有關詳細資訊，請參閱[追加示例](../concepts/effects.md#append-examples)。

## <a name="next-steps"></a>後續步驟

- 查看[Azure 策略示例](../samples/index.md)中的示例。
- 檢閱 [Azure 原則定義結構](../concepts/definition-structure.md)。
- 回顧[瞭解政策效果](../concepts/effects.md)。
- 瞭解如何[以程式設計方式創建策略](programmatically-create.md)。
- 瞭解如何[修復不合規資源](remediate-resources.md)。
- 使用[Azure 管理組查看](../../management-groups/overview.md)管理組的內容。請組織資源。
