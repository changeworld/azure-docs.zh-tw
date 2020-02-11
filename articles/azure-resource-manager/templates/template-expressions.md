---
title: 範本語法和運算式
description: 描述 Azure Resource Manager 範本的宣告式 JSON 語法。
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 42649d4b04b03de32b82335fce68401192de75a3
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2020
ms.locfileid: "77120592"
---
# <a name="syntax-and-expressions-in-azure-resource-manager-templates"></a>Azure Resource Manager 範本中的語法和運算式

範本的基本語法是 JSON。 不過，您可以使用運算式來擴充範本內可用的 JSON 值。  運算式的開頭和結尾都是方括弧：分別是 `[` 和 `]`。 部署範本時，會評估運算式的值。 運算式可以傳回字串、整數、布林值、陣列或物件。

範本運算式不能超過24576個字元。

運算式支援 json （' null '）和屬性支援 null 的常值。 在這兩種情況下，Resource Manager 範本都會將其視為屬性不存在。

## <a name="use-functions"></a>使用函式

下列範例會顯示參數預設值中的運算式：

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
```

在運算式中，語法 `resourceGroup()` 會呼叫 Resource Manager 提供的其中一個函式，以便在範本中使用。 在此情況下，它是[resourceGroup](template-functions-resource.md#resourcegroup)函數。 和在 JavaScript 中相同，函式呼叫的格式為 `functionName(arg1,arg2,arg3)`。 語法 `.location` 會從該函式所傳回的物件中，抓取一個屬性。

範本函數和其參數不區分大小寫。 例如，Resource Manager 在解析 **variables('var1')** 和 **VARIABLES('VAR1')** 時，會將它們視為相同。 評估時，除非函式明確修改大小寫 (例如 toUpper 或 toLower)，否則函式將會保留大小寫。 某些資源類型可能會有不同于評估函式方式的案例需求。

若要將字串值當做參數傳遞至函式，請使用單引號。

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]"
```

## <a name="escape-characters"></a>Escape 字元

若要讓常值字串開頭為左括弧 `[` 並以右方括弧 `]`結尾，但不會將它轉譯為運算式，請加入額外的括弧以使用 `[[`來啟動字串。 例如，變數：

```json
"demoVar1": "[[test value]"
```

解析為 `[test value]`。

不過，如果常值字串不是以括弧結尾，請勿將第一個括弧換成。 例如，變數：

```json
"demoVar2": "[test] value"
```

解析為 `[test] value`。

若要在運算式中以雙引號括住，例如在範本中新增 JSON 物件，請使用反斜線。

```json
"tags": {
    "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
},
```

## <a name="next-steps"></a>後續步驟

* 如需範本函數的完整清單，請參閱 [Azure 資源管理員範本函數](template-functions.md)。
* 如需範本檔案的詳細資訊，請參閱[瞭解 Azure Resource Manager 範本的結構和語法](template-syntax.md)。
