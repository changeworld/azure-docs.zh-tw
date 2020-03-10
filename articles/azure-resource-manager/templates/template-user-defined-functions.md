---
title: 範本中的使用者定義函數
description: 描述如何定義及使用 Azure Resource Manager 範本中的使用者定義函數。
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 2c09572a460aa028b23987033d2b77e2aad8a0cd
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2020
ms.locfileid: "78943210"
---
# <a name="user-defined-functions-in-azure-resource-manager-template"></a>Azure Resource Manager 範本中的使用者定義函數

在您的範本內，您可以建立自己的函式。 這些函式可供您在範本中使用。 使用者定義函式與範本內自動提供的[標準範本](template-functions.md)函式不同。 當您的範本中重複使用複雜的運算式時，請建立您自己的函式。

本文說明如何在 Azure Resource Manager 範本中新增使用者定義的函數。

## <a name="define-the-function"></a>定義函數

您的函式需要命名空間值，以避免範本函式發生命名衝突。 下列範例顯示傳回唯一名稱的函式：

```json
"functions": [
  {
    "namespace": "contoso",
    "members": {
      "uniqueName": {
        "parameters": [
          {
            "name": "namePrefix",
            "type": "string"
          }
        ],
        "output": {
          "type": "string",
          "value": "[concat(toLower(parameters('namePrefix')), uniqueString(resourceGroup().id))]"
        }
      }
    }
  }
],
```

## <a name="use-the-function"></a>使用函數

下列範例會顯示包含使用者定義函數的範本。 它會使用該函數來取得儲存體帳戶的唯一名稱。 範本具有名為**storageNamePrefix**的參數，它會將它當做參數傳遞給函式。

```json
{
 "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
 "contentVersion": "1.0.0.0",
 "parameters": {
   "storageNamePrefix": {
     "type": "string",
     "maxLength": 11
   }
 },
 "functions": [
  {
    "namespace": "contoso",
    "members": {
      "uniqueName": {
        "parameters": [
          {
            "name": "namePrefix",
            "type": "string"
          }
        ],
        "output": {
          "type": "string",
          "value": "[concat(toLower(parameters('namePrefix')), uniqueString(resourceGroup().id))]"
        }
      }
    }
  }
],
 "resources": [
   {
     "type": "Microsoft.Storage/storageAccounts",
     "apiVersion": "2019-04-01",
     "name": "[contoso.uniqueName(parameters('storageNamePrefix'))]",
     "location": "South Central US",
     "sku": {
       "name": "Standard_LRS"
     },
     "kind": "StorageV2",
     "properties": {
       "supportsHttpsTrafficOnly": true
     }
   }
 ]
}
```

## <a name="limitations"></a>限制

在定義使用者函式時，有一些限制：

* 此函式無法存取變數。
* 此函式只能使用函式中定義的參數。 當您在使用者定義函數中使用[parameters](template-functions-deployment.md#parameters)函式時，會限制為該函數的參數。
* 此函式無法呼叫其他的使用者定義函式。
* 函數不能使用[reference](template-functions-resource.md#reference)函式或任何[清單](template-functions-resource.md#list)函數。
* 函式的參數不能有預設值。


## <a name="next-steps"></a>後續步驟

* 若要瞭解使用者定義函數可用的屬性，請參閱[瞭解 Azure Resource Manager 範本的結構和語法](template-syntax.md)。
* 如需可用範本函式的清單，請參閱[Azure Resource Manager 範本](template-functions.md)函式。
