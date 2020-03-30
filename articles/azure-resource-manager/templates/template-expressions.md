---
title: 範本語法和運算式
description: 描述 Azure 資源管理器範本的聲明性 JSON 語法。
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: 172838fa24709eb60fbcb6a68277f44bbd42f01e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460104"
---
# <a name="syntax-and-expressions-in-azure-resource-manager-templates"></a>Azure 資源管理器範本中的語法和運算式

範本的基本語法是 JSON。 但是，可以使用運算式來擴展範本中可用的 JSON 值。  運算式開頭和結尾括號分別是 `[` 和 `]`。 部署範本時，會評估運算式的值。 運算式可以傳回字串、整數、布林值、陣列或物件。

範本運算式不能超過 24，576 個字元。

## <a name="use-functions"></a>使用函式

Azure[資源管理器提供了可在](template-functions.md)範本中使用的功能。 下面的示例顯示了在參數的預設值中使用函數的運算式：

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
```

在運算式中，語法`resourceGroup()`調用資源管理器提供的供範本使用的函數之一。 在這種情況下，它是[資源組](template-functions-resource.md#resourcegroup)函數。 和在 JavaScript 中相同，函式呼叫的格式為 `functionName(arg1,arg2,arg3)`。 語法`.location`從該函數返回的物件中檢索一個屬性。

範本函數和其參數不區分大小寫。 例如，Resource Manager 在解析 **variables('var1')** 和 **VARIABLES('VAR1')** 時，會將它們視為相同。 評估時，除非函式明確修改大小寫 (例如 toUpper 或 toLower)，否則函式將會保留大小寫。 某些資源類型可能具有與計算函數的方式不同的案例要求。

要將字串值作為參數傳遞給函數，請使用單引號。

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]"
```

無論部署到資源組、訂閱、管理組還是租戶，大多數函數的工作方式都相同。 以下函數具有基於範圍的限制：

* [資源組](template-functions-resource.md#resourcegroup)- 只能在部署到資源組時使用。
* [resourceId](template-functions-resource.md#resourceid) - 可在任何作用域使用，但有效參數會根據範圍而變化。
* [訂閱](template-functions-resource.md#subscription)- 只能在部署到資源組或訂閱時使用。

## <a name="escape-characters"></a>逸出字元

要讓文本字串以左括弧`[`開頭，以右括弧`]`結尾，但不將其解釋為運算式，添加一個額外的括弧以啟動字串。 `[[` 例如，變數：

```json
"demoVar1": "[[test value]"
```

解析為`[test value]`。

但是，如果文本字串沒有以括弧結尾，則不要逃離第一個括弧。 例如，變數：

```json
"demoVar2": "[test] value"
```

解析為`[test] value`。

要在運算式中轉義雙引號（如在範本中添加 JSON 物件），請使用反斜線。

```json
"tags": {
    "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
},
```

傳入參數值時，逸出字元的使用取決於參數值的指定位置。 如果在範本中設置了預設值，則需要額外的左括弧。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "demoParam1":{
            "type": "string",
            "defaultValue": "[[test value]"
        }
    },
    "resources": [],
    "outputs": {
        "exampleOutput": {
            "type": "string",
            "value": "[parameters('demoParam1')]"
        }
    }
}
```

如果使用預設值，範本將返回`[test value]`。

但是，如果將參數值傳遞到命令列，則字元將字面解釋。 使用以下功能部署以前的範本：

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName demoGroup -TemplateFile azuredeploy.json -demoParam1 "[[test value]"
```

傳回 `[[test value]`。 相反，請使用：

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName demoGroup -TemplateFile azuredeploy.json -demoParam1 "[test value]"
```

從參數檔傳入值時，也適用相同的格式。 字元被字面解釋。 當與前面的範本一起使用時，以下參數檔返回`[test value]`：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "demoParam1": {
            "value": "[test value]"
        }
   }
}
```

## <a name="null-values"></a>Null 值

要將屬性設置為 null，可以使用**null**或 **[json（"null"）]**。 當您作為參數提供`null`時[，json 函數](template-functions-array.md#json)將返回一個空物件。 在這兩種情況下，資源管理器範本都將其視為屬性不存在。

```json
"stringValue": null,
"objectValue": "[json('null')]"
```

## <a name="next-steps"></a>後續步驟

* 如需範本函數的完整清單，請參閱 [Azure 資源管理員範本函數](template-functions.md)。
* 有關範本檔的詳細資訊，請參閱瞭解[Azure 資源管理器範本的結構和語法](template-syntax.md)。
