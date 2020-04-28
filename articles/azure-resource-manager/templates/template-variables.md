---
title: 範本中的變數
description: 描述如何在 Azure Resource Manager 範本中定義變數。
ms.topic: conceptual
ms.date: 09/05/2019
ms.openlocfilehash: cf135959d30702ea58b7a1d4fdd82625a39245d2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75483813"
---
# <a name="variables-in-azure-resource-manager-template"></a>Azure Resource Manager 範本中的變數

本文說明如何在 Azure Resource Manager 範本中定義和使用變數。 您可以使用變數來簡化您的範本。 您不需要在整個範本中重複複雜的運算式，而是定義一個包含複雜運算式的變數。 然後，您可以視需要在整個範本中參考該變數。

Resource Manager 在啟動部署作業之前，先解析變數。 只要在範本中使用變數，Resource Manager 會以已解析的值來取代它。

## <a name="define-variable"></a>定義變數

下列範例顯示變數定義。 它會建立儲存體帳戶名稱的字串值。 它會使用數個範本函式來取得參數值，並將其串連為唯一字串。

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

您不能在 variables 區段中使用[reference](template-functions-resource.md#reference)函式或任何[list](template-functions-resource.md#list)函數。 這些函式會取得資源的執行時間狀態，而且當變數已解決時，就無法在部署之前執行。

## <a name="use-variable"></a>使用變數

在範本中，您可以使用[variables](template-functions-deployment.md#variables)函數來參考參數的值。 下列範例顯示如何使用資源屬性的變數。

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageName')]",
    ...
  }
]
```

## <a name="configuration-variables"></a>設定變數

您可以定義變數，以保存用於設定環境的相關值。 您可以將變數定義為具有值的物件。 下列範例顯示的物件會保留兩個環境的值：**測試**和**生產**。

```json
"variables": {
  "environmentSettings": {
    "test": {
      "instanceSize": "Small",
      "instanceCount": 1
    },
    "prod": {
      "instanceSize": "Large",
      "instanceCount": 4
    }
  }
},
```

在 parameters 中，您可建立一個值來表示所要使用的組態值。

```json
"parameters": {
  "environmentName": {
    "type": "string",
    "allowedValues": [
      "test",
      "prod"
    ]
  }
},
```

若要取得指定環境的設定，請同時使用變數和參數。

```json
"[variables('environmentSettings')[parameters('environmentName')].instanceSize]"
```

## <a name="example-templates"></a>範本的範例

下列範例示範使用變數的案例。

|[範本]  |描述  |
|---------|---------|
| [變數定義](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | 示範不同類型的變數。 範本不會部署任何資源。 它會建構變數值並傳回這些值。 |
| [組態變數](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | 示範如何使用可定義組態值的變數。 範本不會部署任何資源。 它會建構變數值並傳回這些值。 |
| [網路安全性規則](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json)和[參數檔案](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | 建構正確格式的陣列，以便將安全性規則指派給網路安全性群組。 |

## <a name="next-steps"></a>後續步驟

* 若要瞭解變數可用的屬性，請參閱[瞭解 Azure Resource Manager 範本的結構和語法](template-syntax.md)。
* 如需建立變數的建議，請參閱[最佳做法-變數](template-best-practices.md#variables)。
