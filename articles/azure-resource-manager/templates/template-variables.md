---
title: 範本中的變數
description: 介紹如何在 Azure 資源管理器範本中定義變數。
ms.topic: conceptual
ms.date: 09/05/2019
ms.openlocfilehash: cf135959d30702ea58b7a1d4fdd82625a39245d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75483813"
---
# <a name="variables-in-azure-resource-manager-template"></a>Azure 資源管理器範本中的變數

本文介紹如何在 Azure 資源管理器範本中定義和使用變數。 您可以使用變數來簡化範本。 您定義一個包含複雜運算式的變數，而不是在整個範本中重複複雜的運算式。 然後，在整個範本中根據需要引用該變數。

資源管理器在開始部署操作之前解析變數。 無論變數在範本中使用何處，資源管理器都會將其替換為已解析的值。

## <a name="define-variable"></a>定義變數

下列範例顯示變數定義。 它會建立儲存體帳戶名稱的字串值。 它使用多個範本函數來獲取參數值，並將其串聯到唯一字串中。

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

不能使用變數部分中的[引用](template-functions-resource.md#reference)函數或任何[清單](template-functions-resource.md#list)函數。 這些函數獲取資源的運行時狀態，並且在解析變數時無法在部署之前執行。

## <a name="use-variable"></a>使用變數

在範本中，使用[變數](template-functions-deployment.md#variables)函數傳址參數的值。 下面的示例演示如何將變數用於資源屬性。

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

您可以定義保存相關值以配置環境的變數。 將變數定義為具有值的物件。 下面的示例顯示了一個物件，該物件包含兩個環境的值 -**測試**和**prod**。

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

若要檢索指定環境的設置，請使用變數和參數。

```json
"[variables('environmentSettings')[parameters('environmentName')].instanceSize]"
```

## <a name="example-templates"></a>範本的範例

以下示例演示了使用變數的方案。

|[範本]  |描述  |
|---------|---------|
| [變數定義](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | 示範不同類型的變數。 範本不會部署任何資源。 它會建構變數值並傳回這些值。 |
| [組態變數](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | 示範如何使用可定義組態值的變數。 範本不會部署任何資源。 它會建構變數值並傳回這些值。 |
| [網路安全性規則](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json)和[參數檔案](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | 建構正確格式的陣列，以便將安全性規則指派給網路安全性群組。 |

## <a name="next-steps"></a>後續步驟

* 要瞭解變數的可用屬性，請參閱[瞭解 Azure 資源管理器範本的結構和語法](template-syntax.md)。
* 有關創建變數的建議，請參閱最佳實踐[- 變數](template-best-practices.md#variables)。
