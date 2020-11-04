---
title: 資源陣列屬性編寫原則
description: 瞭解如何使用陣列參數和陣列語言運算式、評估 [*] 別名，以及附加具有 Azure 原則定義規則的元素。
ms.date: 10/22/2020
ms.topic: how-to
ms.openlocfilehash: 92339a6da4fd2061d66935cc8d04428c69822862
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93323230"
---
# <a name="author-policies-for-array-properties-on-azure-resources"></a>對於 Azure 資源編寫陣列屬性的原則

Azure Resource Manager 屬性通常會定義為字串和布林值。 存在一對多關聯性時，複雜屬性會改為定義為陣列。 在 Azure 原則中，能夠以多種不同的方式使用陣列：

- [定義參數](../concepts/definition-structure.md#parameters)的類型，可提供多個選項
- 使用條件 **in** 或 **notIn** 的 [原則規則](../concepts/definition-structure.md#policy-rule)一部分
- 評估 [\[\*\] 別名](../concepts/definition-structure.md#understanding-the--alias)的原則規則，將會評估：
  - **無** 、 **任何** 或 **全部** 的案例
  - **計數** 的複雜案例
- 在[附加效果](../concepts/effects.md#append)中取代或加入至現有的陣列

本文涵蓋 Azure 原則的每個使用方式，並提供數個範例定義。

## <a name="parameter-arrays"></a>參數陣列

### <a name="define-a-parameter-array"></a>定義參數陣列

將參數定義為陣列，可在需要一個以上的值時，允許原則彈性。
此原則定義允許 **allowedLocations** 參數的任何單一位置，並預設為 _eastus2_ ：

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

由於 **類型** 為 _字串_ ，因此指派原則時只能設定一個值。 如果指派此原則，則範圍內的資源只能在單一 Azure 區域中使用。 大部分的原則定義都必須允許已核准的選項清單，例如允許 _eastus2_ 、 _eastus_ 和 _westus2_ 。

若要建立原則定義以允許多個選項，請使用「陣列」 **類型** 。 相同的原則可以改寫如下：

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
> 一旦儲存原則定義之後，就無法變更參數上的 **類型** 屬性。

這個新的參數定義會在原則指派期間接受一個以上的值。 陣列屬性 **allowedValues** 定義完畢後，指派期間可用的值會進一步限制為預先定義的選項清單。 使用 **allowedValues** 為選擇性。

### <a name="pass-values-to-a-parameter-array-during-assignment"></a>於指派期間將值傳遞給參數陣列

透過 Azure 入口網站指派原則時， **類型** 「陣列」的參數會顯示為單一文字方塊。 提示顯示「請使用 ; 來分隔值。 (例如倫敦;紐約)」。 若要將 _eastus2_ 、 _eastus_ 和 _westus2_ 允許的位置值傳遞給參數，請使用下列字串：

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

- Azure CLI：命令 [az policy assignment create](/cli/azure/policy/assignment#az-policy-assignment-create) 搭配參數 **params**
- Azure PowerShell：Cmdlet [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) 搭配參數 **PolicyParameter**
- REST API： _PUT_ 中屬於要求本文的 **properties.parameters** 屬性值 [create](/rest/api/resources/policyassignments/create)作業

## <a name="array-conditions"></a>陣列條件

參數的「陣列」
**類型** 可使用的原則規則[條件](../concepts/definition-structure.md#conditions)僅限於 `in` 和 `notIn`。 採用下列原則定義，並以條件 `equals` 做為範例：

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

條件 `equals` 的預期 **類型** 是 _字串_ 。 由於 **allowedLocations** 定義為 **類型**_陣列_ ，原則引擎會評估語言運算式，並擲回錯誤。 有了 `in` 和 `notIn` 條件，原則引擎就會預期語言運算式中的 **類型**_陣列_ 。 若要解決此錯誤訊息，請將 `equals` 變更為 `in` 或 `notIn`。

## <a name="referencing-array-resource-properties"></a>參考陣列資源屬性

許多使用案例都需要使用評估的資源中的陣列屬性。 某些案例需要參考整個陣列 (例如，檢查其長度) 。 有些則需要將條件套用到每個個別陣列成員 (例如，確定所有防火牆規則封鎖從網際網路) 的存取。 瞭解 Azure 原則可以參考資源屬性的不同方式，以及這些參考在參考陣列屬性時的行為，是撰寫涵蓋這些案例之條件的關鍵。

### <a name="referencing-resource-properties"></a>參考資源屬性
Azure 原則使用 [別名](../concepts/definition-structure.md#aliases) 來參考資源屬性，有兩種方式可以參考 Azure 原則內資源屬性的值：

- 使用 [欄位](../concepts/definition-structure.md#fields) 條件來檢查 **所有** 選取的資源屬性是否符合條件。 範例：

  ```json
  {
    "field" : "Microsoft.Test/resourceType/property",
    "equals": "value"
  }
  ```

- 使用 `field()` 函數來存取屬性的值。 範例：

  ```json
  {
    "value": "[take(field('Microsoft.Test/resourceType/property'), 7)]",
    "equals": "prefix_"
  }
  ```

欄位條件具有隱含的「全部」行為。 如果別名代表值的集合，則會檢查所有個別值是否符合條件。 函 `field()` 式會傳回由別名表示的值，然後由其他範本函式來操作。

### <a name="referencing-array-fields"></a>參考陣列欄位

陣列資源屬性通常是以兩種不同類型的別名來表示。 一個已附加至它的「一般」別名和 [陣列別名](../concepts/definition-structure.md#understanding-the--alias) `[*]` ：

- `Microsoft.Test/resourceType/stringArray`
- `Microsoft.Test/resourceType/stringArray[*]`

#### <a name="referencing-the-array"></a>參考陣列

第一個別名代表單一值，也就是 `stringArray` 要求內容中的屬性值。 因為該屬性的值是陣列，所以在原則條件下並不太實用。 例如：

```json
{
  "field": "Microsoft.Test/resourceType/stringArray",
  "equals": "..."
}
```

這個條件會將整個 `stringArray` 陣列與單一字串值進行比較。 大部分的條件（包括 `equals` ）只接受字串值，因此比較陣列與字串沒有太多用途。 參考陣列屬性的主要案例是在檢查它是否存在時很有用：

```json
{
  "field": "Microsoft.Test/resourceType/stringArray",
  "exists": "true"
}
```

使用函式 `field()` 時，傳回的值是要求內容中的陣列，之後可以搭配接受陣列引數的任何 [支援範本函數](../concepts/definition-structure.md#policy-functions) 使用。 例如，下列條件會檢查的長度是否 `stringArray` 大於0：

```json
{
  "value": "[length(field('Microsoft.Test/resourceType/stringArray'))]",
  "greater": 0
}
```

#### <a name="referencing-the-array-members-collection"></a>參考陣列成員集合

使用語法的別名 `[*]` 代表 **從陣列屬性選取的屬性值集合** ，與選取陣列屬性本身不同。 如果是 `Microsoft.Test/resourceType/stringArray[*]` ，它會傳回一個集合，其中包含的所有成員 `stringArray` 。 如先前所述， `field` 條件會檢查所有選取的資源屬性是否符合條件，因此只有當的 **所有** 成員 `stringArray` 都等於 ' "value" ' 時，下列條件才為 true。

```json
{
  "field": "Microsoft.Test/resourceType/stringArray[*]",
  "equals": "value"
}
```

如果陣列包含物件，則 `[*]` 可以使用別名來選取每個陣列成員的特定屬性值。 範例：

```json
{
  "field": "Microsoft.Test/resourceType/objectArray[*].property",
  "equals": "value"
}
```

如果中所有屬性的值都等於，則此條件為 true `property` `objectArray` `"value"` 。

使用函 `field()` 式來參考陣列別名時，傳回的值為所有選取值的陣列。 此行為表示函式的常見使用案例，將範本函式套用 `field()` 至資源屬性值的功能非常有限。 在此情況下，唯一可以使用的範本函式是接受陣列引數的函式。 例如，您可以使用取得陣列的長度 `[length(field('Microsoft.Test/resourceType/objectArray[*].property'))]` 。 不過，更複雜的案例，例如將範本函式套用至每個陣列成員，並且將它與所需的值進行比較，只有在使用運算式時才有可能 `count` 。 如需詳細資訊，請參閱 [計數運算式](#count-expressions)。

總而言之，請參閱下列範例資源內容，以及各種別名所傳回的選取值：

```json
{
  "tags": {
    "env": "prod"
  },
  "properties":
  {
    "stringArray": [ "a", "b", "c" ],
    "objectArray": [
      {
        "property": "value1",
        "nestedArray": [ 1, 2 ]
      },
      {
        "property": "value2",
        "nestedArray": [ 3, 4 ]
      }
    ]
  }
}
```

在範例資源內容上使用欄位條件時，結果如下所示：

| Alias | 選取的值 |
|:--- |:---|
| `Microsoft.Test/resourceType/missingArray` | `null` |
| `Microsoft.Test/resourceType/missingArray[*]` | 值的空集合。 |
| `Microsoft.Test/resourceType/missingArray[*].property` | 值的空集合。 |
| `Microsoft.Test/resourceType/stringArray` | `["a", "b", "c"]` |
| `Microsoft.Test/resourceType/stringArray[*]` | `"a"`, `"b"`, `"c"` |
| `Microsoft.Test/resourceType/objectArray[*]` |  `{ "property": "value1", "nestedArray": [ 1, 2 ] }`,<br/>`{ "property": "value2", "nestedArray": [ 3, 4 ] }`|
| `Microsoft.Test/resourceType/objectArray[*].property` | `"value1"`, `"value2"` |
| `Microsoft.Test/resourceType/objectArray[*].nestedArray` | `[ 1, 2 ]`, `[ 3, 4 ]` |
| `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` | `1`, `2`, `3`, `4` |

在範例資源內容上使用函式時，結果如下所示 `field()` ：

| 運算式 | 傳回值 |
|:--- |:---|
| `[field('Microsoft.Test/resourceType/missingArray')]` | `""` |
| `[field('Microsoft.Test/resourceType/missingArray[*]')]` | `[]` |
| `[field('Microsoft.Test/resourceType/missingArray[*].property')]` | `[]` |
| `[field('Microsoft.Test/resourceType/stringArray')]` | `["a", "b", "c"]` |
| `[field('Microsoft.Test/resourceType/stringArray[*]')]` | `["a", "b", "c"]` |
| `[field('Microsoft.Test/resourceType/objectArray[*]')]` |  `[{ "property": "value1", "nestedArray": [ 1, 2 ] }, { "property": "value2", "nestedArray": [ 3, 4 ] }]`|
| `[field('Microsoft.Test/resourceType/objectArray[*].property')]` | `["value1", "value2"]` |
| `[field('Microsoft.Test/resourceType/objectArray[*].nestedArray')]` | `[[ 1, 2 ], [ 3, 4 ]]` |
| `[field('Microsoft.Test/resourceType/objectArray[*].nestedArray[*]')]` | `[1, 2, 3, 4]` |

## <a name="count-expressions"></a>計數運算式

[計數](../concepts/definition-structure.md#count) 運算式會計算符合條件的陣列成員數目，並將計數與目標值進行比較。 `Count` 相較于條件，比起評估陣列更具直覺性 `field` 。 語法為：

```json
{
  "count": {
    "field": <[*] alias>,
    "where": <optional policy condition expression>
  },
  "equals|greater|less|any other operator": <target value>
}
```

使用時若沒有 ' where ' 條件， `count` 只會傳回陣列的長度。 使用上一節中的範例資源內容，下列 `count` 運算式會評估為， `true` 因為 `stringArray` 有三個成員：

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]"
  },
  "equals": 3
}
```

此行為也適用于嵌套陣列。 例如，下列 `count` 運算式會評估為， `true` 因為陣列中有四個數組成員 `nestedArray` ：

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]"
  },
  "greaterOrEquals": 4
}
```

在 `count` 條件中的威力 `where` 。 當指定這個值時，Azure 原則會列舉陣列成員，並針對條件進行評估，以計算有多少陣列成員評估為 `true` 。 具體來說，在條件評估的每個反復專案中 `where` ，Azure 原則會選取單一陣列成員 * **i** _，並根據 `where` 條件 * 來評估資源內容 _* 就像 *_i_*_ 是 array_ * 的唯一成員一樣。 每個反復專案中只有一個可用的陣列成員，可讓您在每個個別陣列成員上套用複雜的條件。

範例：
```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]",
    "where": {
      "field": "Microsoft.Test/resourceType/stringArray[*]",
      "equals": "a"
    }
  },
  "equals": 1
}
```
為了評估 `count` 運算式，Azure 原則 `where` 會評估條件3次，每個成員的一次會計算 `stringArray` 評估的次數 `true` 。 當 `where` 條件參考 `Microsoft.Test/resourceType/stringArray[*]` 陣列成員，而不是選取的所有成員時 `stringArray` ，每次只會選取單一陣列成員：

| 反覆運算 | 選取的 `Microsoft.Test/resourceType/stringArray[*]` 值 | `where` 評估結果 |
|:---|:---|:---|
| 1 | `"a"` | `true` |
| 2 | `"b"` | `false` |
| 3 | `"c"` | `false` |

因此， `count` 將會傳回 `1` 。

以下是更複雜的運算式：
```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
      "allOf": [
        {
          "field": "Microsoft.Test/resourceType/objectArray[*].property",
          "equals": "value2"
        },
        {
          "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]",
          "greater": 2
        }
      ]
    }
  },
  "equals": 1
}
```

| 反覆運算 | 選取的值 | `where` 評估結果 |
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value1"` </br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1`, `2` | `false` |
| 2 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value2"` </br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3`, `4`| `true` |

因此，會 `count` 傳回 `1` 。

`where`針對 **整個** 要求內容評估運算式 (只有目前列舉) 的陣列成員有變更，表示該 `where` 條件也可以參考陣列以外的欄位：
```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
      "field": "tags.env",
      "equals": "prod"
    }
  }
}
```

| 反覆運算 | 選取的值 | `where` 評估結果 |
|:---|:---|:---|
| 1 | `tags.env` => `"prod"` | `true` |
| 2 | `tags.env` => `"prod"` | `true` |

也允許使用 Nested count 運算式：
```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
      "allOf": [
        {
          "field": "Microsoft.Test/resourceType/objectArray[*].property",
          "equals": "value2"
        },
        {
          "count": {
            "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]",
            "where": {
              "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]",
              "equals": 3
            },
            "greater": 0
          }
        }
      ]
    }
  }
}
```
 
| 外部迴圈反覆運算 | 選取的值 | 內部迴圈反復專案 | 選取的值 |
|:---|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value1`</br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1`, `2` | 1 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1` |
| 1 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value1`</br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1`, `2` | 2 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `2` |
| 2 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value2`</br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3`, `4` | 1 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3` |
| 2 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value2`</br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3`, `4` | 2 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `4` |

### <a name="the-field-function-inside-where-conditions"></a>`field()`條件內的函數 `where`

在 `field()` 條件內以 `where` 下列概念為基礎時，函數的行為方式：
1. 陣列別名會解析成從所有陣列成員選取的值集合。
1. `field()` 參考陣列別名的函式會傳回具有所選取值的陣列。
1. 在條件內參考計數的陣列別名 `where` 會傳回一個集合，其中包含從陣列成員選取的單一值，此值會在目前的反復專案中進行評估。

此行為表示，在條件內使用函式來參考計數陣列成員時 `field()` `where` ， **會傳回具有單一成員的陣列** 。 雖然這可能不是直覺的，但與陣列別名一律會傳回所選屬性集合的概念一致。 以下是範例：

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]",
    "where": {
      "field": "Microsoft.Test/resourceType/stringArray[*]",
      "equals": "[field('Microsoft.Test/resourceType/stringArray[*]')]"
    }
  },
  "equals": 0
}
```

| 反覆運算 | 運算式值 | `where` 評估結果 |
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/stringArray[*]` => `"a"` </br>  `[field('Microsoft.Test/resourceType/stringArray[*]')]` => `[ "a" ]` | `false` |
| 2 | `Microsoft.Test/resourceType/stringArray[*]` => `"b"` </br>  `[field('Microsoft.Test/resourceType/stringArray[*]')]` => `[ "b" ]` | `false` |
| 3 | `Microsoft.Test/resourceType/stringArray[*]` => `"c"` </br>  `[field('Microsoft.Test/resourceType/stringArray[*]')]` => `[ "c" ]` | `false` |

因此，當需要使用函式來存取計數陣列別名的值時，執行這項作業 `field()` 的方式是將它包裝為範本函式 `first()` ：

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]",
    "where": {
      "field": "Microsoft.Test/resourceType/stringArray[*]",
      "equals": "[first(field('Microsoft.Test/resourceType/stringArray[*]'))]"
    }
  }
}
```

| 反覆運算 | 運算式值 | `where` 評估結果 |
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/stringArray[*]` => `"a"` </br>  `[first(field('Microsoft.Test/resourceType/stringArray[*]'))]` => `"a"` | `true` |
| 2 | `Microsoft.Test/resourceType/stringArray[*]` => `"b"` </br>  `[first(field('Microsoft.Test/resourceType/stringArray[*]'))]` => `"b"` | `true` |
| 3 | `Microsoft.Test/resourceType/stringArray[*]` => `"c"` </br>  `[first(field('Microsoft.Test/resourceType/stringArray[*]'))]` => `"c"` | `true` |

如需實用的範例，請參閱 [計數範例](../concepts/definition-structure.md#count-examples)。

## <a name="modifying-arrays"></a>修改陣列

在建立或更新期間， [附加](../concepts/effects.md#append) 和 [修改](../concepts/effects.md#modify) 資源的 alter 屬性。 使用陣列屬性時，這些效果的行為取決於作業是否嘗試修改  **\[\*\]** 別名而定：

> [!NOTE]
> 使用 `modify` 具有別名的效果目前為 **預覽** 狀態。

|Alias |效果 | 結果 |
|-|-|-|
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules` | `append` | Azure 原則會將效果詳細資料中指定的整個陣列附加至遺失的情況。 |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules` | `modify` 使用 `add` 作業 | Azure 原則會將效果詳細資料中指定的整個陣列附加至遺失的情況。 |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules` | `modify` 使用 `addOrReplace` 作業 | 如果遺漏或取代現有的陣列，Azure 原則會附加效果詳細資料中指定的整個陣列。 |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]` | `append` | Azure 原則會附加在效果詳細資料中指定的陣列成員。 |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]` | `modify` 使用 `add` 作業 | Azure 原則會附加在效果詳細資料中指定的陣列成員。 |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]` | `modify` 使用 `addOrReplace` 作業 | Azure 原則移除所有現有的陣列成員，並附加在效果詳細資料中指定的陣列成員。 |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action` | `append` | Azure 原則將值附加至 `action` 每個陣列成員的屬性。 |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action` | `modify` 使用 `add` 作業 | Azure 原則將值附加至 `action` 每個陣列成員的屬性。 |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action` | `modify` 使用 `addOrReplace` 作業 | Azure 原則會附加或取代 `action` 每個陣列成員的現有屬性。 |

如需詳細資訊，請參閱[附加範例](../concepts/effects.md#append-examples)。

## <a name="next-steps"></a>後續步驟

- 在 [Azure 原則範例](../samples/index.md)檢閱範例。
- 檢閱 [Azure 原則定義結構](../concepts/definition-structure.md)。
- 檢閱[了解原則效果](../concepts/effects.md)。
- 了解如何[以程式設計方式建立原則](programmatically-create.md)。
- 了解如何[補救不符合規範的資源](remediate-resources.md)。
- 透過[使用 Azure 管理群組來組織資源](../../management-groups/overview.md)來檢閱何謂管理群組。
