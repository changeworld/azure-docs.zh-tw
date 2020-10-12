---
title: 範本中的使用者定義函數
description: 描述如何在 Azure Resource Manager 範本中定義和使用使用者定義函數。
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 69f4e98d389cc8dbe5cd3f4b628189676c501106
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84672930"
---
# <a name="user-defined-functions-in-azure-resource-manager-template"></a>Azure Resource Manager 範本中的使用者定義函數

在您的範本內，您可以建立自己的函式。 這些函式可供您在範本中使用。 使用者定義函式與您範本中自動提供的 [標準範本](template-functions.md) 函式不同。 當您有在範本中重複使用的複雜運算式時，請建立您自己的函式。

本文說明如何在 Azure Resource Manager 範本中新增使用者自訂函數。

## <a name="define-the-function"></a>定義函數

您的函式需要命名空間值，以避免範本函式發生命名衝突。 下列範例顯示的函式會傳回唯一的名稱：

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

下列範例顯示包含使用者定義函數的範本。 它使用該函數來取得儲存體帳戶的唯一名稱。 範本有一個名為 **storageNamePrefix** 的參數，它會以參數的形式傳遞至函式。

```json
{
 "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
* 此函式只能使用函式中定義的參數。 當您在使用者自訂函數中使用 [parameters](template-functions-deployment.md#parameters) 函式時，您會被限制為該函數的參數。
* 此函式無法呼叫其他的使用者定義函式。
* 函數無法使用 [參考](template-functions-resource.md#reference) 函式或任何 [清單](template-functions-resource.md#list) 函數。
* 函式的參數不能有預設值。


## <a name="next-steps"></a>接下來的步驟

* 若要瞭解使用者定義函數的可用屬性，請參閱 [瞭解 Azure Resource Manager 範本的結構和語法](template-syntax.md)。
* 如需可用範本函式的清單，請參閱 [Azure Resource Manager 範本函數](template-functions.md)。
