---
title: 範本語法和運算式
description: 描述 Azure Resource Manager 範本的宣告式 JSON 語法。
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: baddedae1b918502e579d2ed230e0779960f45e7
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82203823"
---
# <a name="syntax-and-expressions-in-azure-resource-manager-templates"></a>Azure Resource Manager 範本中的語法和運算式

範本的基本語法是 JSON。 不過，您可以使用運算式來擴充範本內可用的 JSON 值。  運算式開頭和結尾括號分別是 `[` 和 `]`。 部署範本時，會評估運算式的值。 運算式可以傳回字串、整數、布林值、陣列或物件。

範本運算式不能超過24576個字元。

## <a name="use-functions"></a>使用函式

Azure Resource Manager 提供[可](template-functions.md)在範本中使用的函式。 下列範例顯示在參數的預設值中使用函數的運算式：

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
```

在運算式中，語法`resourceGroup()`會呼叫 Resource Manager 提供的其中一個函式，以便在範本中使用。 在此情況下，它是[resourceGroup](template-functions-resource.md#resourcegroup)函數。 和在 JavaScript 中相同，函式呼叫的格式為 `functionName(arg1,arg2,arg3)`。 語法`.location`會從該函式所傳回的物件中，抓取一個屬性。

範本函數和其參數不區分大小寫。 例如，Resource Manager 在解析 **variables('var1')** 和 **VARIABLES('VAR1')** 時，會將它們視為相同。 評估時，除非函式明確修改大小寫 (例如 toUpper 或 toLower)，否則函式將會保留大小寫。 某些資源類型可能會有不同于評估函式方式的案例需求。

若要將字串值當做參數傳遞至函式，請使用單引號。

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]"
```

無論部署至資源群組、訂用帳戶、管理群組或租使用者，大部分函式的運作方式都相同。 下列函式具有以範圍為基礎的限制：

* [resourceGroup](template-functions-resource.md#resourcegroup) -只能用在資源群組的部署中。
* [resourceId](template-functions-resource.md#resourceid) -可以用於任何範圍，但有效的參數會根據範圍而變更。
* [訂](template-functions-resource.md#subscription)用帳戶-只能用在資源群組或訂用帳戶的部署中。

## <a name="escape-characters"></a>逸出字元

若要讓常值字串開頭為左括弧`[` ，並以右括弧`]`結尾，但未將它解釋為運算式，請加入額外的括弧，以開頭的字串`[[`。 例如，變數：

```json
"demoVar1": "[[test value]"
```

會解析`[test value]`為。

不過，如果常值字串不是以括弧結尾，請勿將第一個括弧換成。 例如，變數：

```json
"demoVar2": "[test] value"
```

會解析`[test] value`為。

若要在運算式中以雙引號括住，例如在範本中新增 JSON 物件，請使用反斜線。

```json
"tags": {
    "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
},
```

傳入參數值時，使用 escape 字元取決於指定參數值的位置。 如果您在範本中設定預設值，則需要額外的左括弧。

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

如果您使用預設值，範本會傳回`[test value]`。

不過，如果您透過命令列傳入參數值，字元就會以字面方式轉譯。 使用來部署先前的範本：

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName demoGroup -TemplateFile azuredeploy.json -demoParam1 "[[test value]"
```

傳回 `[[test value]`。 相反地，請使用：

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName demoGroup -TemplateFile azuredeploy.json -demoParam1 "[test value]"
```

從參數檔案傳入值時，會套用相同的格式。 字元會以字面方式解讀。 與前述範本搭配使用時，下列參數檔案會傳回`[test value]`：

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

若要將屬性設定為 null，您可以使用**null**或 **[json （' null '）]**。 當您提供`null`做為參數時， [json 函數](template-functions-object.md#json)會傳回空的物件。 在這兩種情況下，Resource Manager 範本都會將其視為屬性不存在。

```json
"stringValue": null,
"objectValue": "[json('null')]"
```

## <a name="next-steps"></a>後續步驟

* 如需範本函數的完整清單，請參閱 [Azure 資源管理員範本函數](template-functions.md)。
* 如需範本檔案的詳細資訊，請參閱[瞭解 Azure Resource Manager 範本的結構和語法](template-syntax.md)。
