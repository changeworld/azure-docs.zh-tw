---
title: 範本中使用者定義的函數
description: 介紹如何在 Azure 資源管理器範本中定義和使用使用者定義的函數。
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 2c09572a460aa028b23987033d2b77e2aad8a0cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943210"
---
# <a name="user-defined-functions-in-azure-resource-manager-template"></a>Azure 資源管理器範本中使用者定義的函數

在您的範本內，您可以建立自己的函式。 這些函式可供您在範本中使用。 使用者定義的函數與範本中自動可用的[標準範本函數](template-functions.md)是分開的。 當您有在範本中重複使用的複雜運算式時，請創建自己的函數。

本文介紹如何在 Azure 資源管理器範本中添加使用者定義的函數。

## <a name="define-the-function"></a>定義函數

您的函式需要命名空間值，以避免範本函式發生命名衝突。 下面的示例顯示了返回唯一名稱的函數：

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

下面的示例顯示了包含使用者定義的函數的範本。 它使用該函數獲取存儲帳戶的唯一名稱。 範本具有一個名為**storageNamePrefix 的**參數，它作為參數傳遞給函數。

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
* 此函式只能使用函式中定義的參數。 在使用者定義的函數中使用[參數](template-functions-deployment.md#parameters)函數時，將限制為該函數的參數。
* 此函式無法呼叫其他的使用者定義函式。
* 函數不能使用[引用](template-functions-resource.md#reference)函數或任何[清單](template-functions-resource.md#list)函數。
* 函式的參數不能有預設值。


## <a name="next-steps"></a>後續步驟

* 要瞭解使用者定義的函數的可用屬性，請參閱[瞭解 Azure 資源管理器範本的結構和語法](template-syntax.md)。
* 有關可用範本函數的清單，請參閱 Azure[資源管理器範本函數](template-functions.md)。
