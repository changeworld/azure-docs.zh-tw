---
title: 範本中的變數
description: 描述如何在 Azure Resource Manager 範本中定義 (ARM 範本) 的變數。
ms.topic: conceptual
ms.date: 01/26/2021
ms.openlocfilehash: feecc4b5df77e6a3bf51294cb12aabf44899dde5
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98874429"
---
# <a name="variables-in-arm-template"></a>ARM 範本中的變數

本文說明如何在 Azure Resource Manager 範本中定義和使用變數 (ARM 範本) 。 您可以使用變數來簡化您的範本。 您可以定義包含複雜運算式的變數，而不是在整個範本中重複複雜的運算式。 然後，您可以在整個範本中視需要參考該變數。

Resource Manager 在開始部署作業之前解析變數。 只要在範本中使用變數，Resource Manager 就會以已解析的值來加以取代。

## <a name="define-variable"></a>定義變數

定義變數時，請提供可解析為 [資料類型](template-syntax.md#data-types)的值或範本運算式。 您可以在建立變數時，使用參數或其他變數中的值。

您可以在變數 [宣告中使用](template-functions.md) 樣板函式，但是不能使用 [參考](template-functions-resource.md#reference) 函式或任何 [清單](template-functions-resource.md#list) 函數。 這些函式會取得資源的執行時間狀態，而且在解析變數之前無法執行部署。

下列範例顯示變數定義。 它會建立儲存體帳戶名稱的字串值。 它會使用數個範本函式來取得參數值，並將它串連成唯一的字串。

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

## <a name="use-variable"></a>使用變數

在範本中，您可以使用 [variables](template-functions-deployment.md#variables) 函數來參考參數的值。 下列範例顯示如何將變數用於資源屬性。

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageName')]",
    ...
  }
]
```

## <a name="example-template"></a>範本範例

下列範本不會部署任何資源。 它只會顯示一些宣告變數的方式。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variables.json":::

## <a name="configuration-variables"></a>設定變數

您可以定義包含設定環境相關值的變數。 您可以將變數定義為具有值的物件。 下列範例顯示的物件包含兩個環境的值- **測試** 和 **生產** 環境。您會在部署期間傳入其中一個值。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variablesconfigurations.json":::

## <a name="next-steps"></a>後續步驟

* 若要瞭解變數的可用屬性，請參閱 [瞭解 ARM 範本的結構和語法](template-syntax.md)。
* 如需有關建立變數的建議，請參閱 [最佳做法-變數](template-best-practices.md#variables)。
* 如需將安全性規則指派給網路安全性群組的範例範本，請參閱 [網路安全性規則](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) 和 [參數](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json)檔案。
