---
title: 範本中的參數
description: 介紹如何在 Azure 資源管理器範本中定義參數。
ms.topic: conceptual
ms.date: 09/05/2019
ms.openlocfilehash: 89c6984c587e8dae59c1825a99d4f8da1c06dafb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122418"
---
# <a name="parameters-in-azure-resource-manager-templates"></a>Azure 資源管理器範本中的參數

本文介紹如何在 Azure 資源管理器範本中定義和使用參數。 通過為參數提供不同的值，可以為不同的環境重用範本。

資源管理器在開始部署操作之前解析參數值。 無論在範本中使用參數，資源管理器都會將其替換為解析的值。

## <a name="define-parameter"></a>定義參數

下列範例顯示簡單的參數定義。 它定義了一個名為**存儲SKU**的參數。 參數是字串值，僅接受對其預期用途有效的值。 當部署期間未提供任何值時，參數將使用預設值。

```json
"parameters": {
  "storageSKU": {
    "type": "string",
    "allowedValues": [
      "Standard_LRS",
      "Standard_ZRS",
      "Standard_GRS",
      "Standard_RAGRS",
      "Premium_LRS"
    ],
    "defaultValue": "Standard_LRS",
    "metadata": {
      "description": "The type of replication to use for the storage account."
    }
  }
}
```

## <a name="use-parameter"></a>使用參數

在範本中，使用[參數](template-functions-deployment.md#parameters)函數傳址參數的值。 在下面的示例中，參數值用於為存儲帳戶設置 SKU。

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "sku": {
      "name": "[parameters('storageSKU')]"
    },
    ...
  }
]
```

## <a name="template-functions"></a>範本函式

為參數指定預設值時，您可以使用大部分的範本函式。 您可以使用另一個參數以建立預設值。 以下範本演示了函數在預設值中的使用。 當沒有為網站提供名稱時，它將創建一個唯一的字串值並將其追加到**網站**。 當主機計畫未提供名稱時，它將獲取網站的值，並追加 **-計畫**。

```json
"parameters": {
  "siteName": {
    "type": "string",
    "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]",
    "metadata": {
      "description": "The site name. To use the default value, do not specify a new value."
    }
  },
  "hostingPlanName": {
    "type": "string",
    "defaultValue": "[concat(parameters('siteName'),'-plan')]",
    "metadata": {
      "description": "The host name. To use the default value, do not specify a new value."
    }
  }
}
```

不能使用參數部分中的[引用](template-functions-resource.md#reference)函數或任何[清單](template-functions-resource.md#list)函數。 這些函數獲取資源的運行時狀態，並且在解析參數時無法在部署之前執行。

## <a name="objects-as-parameters"></a>作為參數的物件

藉由將相關值以物件的方式傳遞，可以更輕鬆地進行組織。 此方法也會減少範本中的參數數目。

下面的示例顯示了作為物件的參數。 預設值顯示物件的預期屬性。

```json
"parameters": {
  "VNetSettings": {
    "type": "object",
    "defaultValue": {
      "name": "VNet1",
      "location": "eastus",
      "addressPrefixes": [
        {
          "name": "firstPrefix",
          "addressPrefix": "10.0.0.0/22"
        }
      ],
      "subnets": [
        {
          "name": "firstSubnet",
          "addressPrefix": "10.0.0.0/24"
        },
        {
          "name": "secondSubnet",
          "addressPrefix": "10.0.1.0/24"
        }
      ]
    }
  }
},
```

通過使用點運算子引用物件的屬性。

```json
"resources": [
  {
    "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2015-06-15",
    "name": "[parameters('VNetSettings').name]",
    "location": "[parameters('VNetSettings').location]",
    "properties": {
      "addressSpace":{
        "addressPrefixes": [
          "[parameters('VNetSettings').addressPrefixes[0].addressPrefix]"
        ]
      },
      "subnets":[
        {
          "name":"[parameters('VNetSettings').subnets[0].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[0].addressPrefix]"
          }
        },
        {
          "name":"[parameters('VNetSettings').subnets[1].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[1].addressPrefix]"
          }
        }
      ]
    }
  }
]
```

## <a name="example-templates"></a>範本的範例

以下示例演示了使用參數的方案。

|[範本]  |描述  |
|---------|---------|
|[具有預設值之帶有函式的參數](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | 示範定義參數的預設值時，如何使用範本函式。 範本不會部署任何資源。 它會建構參數值，並傳回這些值。 |
|[參數物件](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | 示範如何針對參數使用物件。 範本不會部署任何資源。 它會建構參數值，並傳回這些值。 |


## <a name="next-steps"></a>後續步驟

* 要瞭解參數的可用屬性，請參閱[瞭解 Azure 資源管理器範本的結構和語法](template-syntax.md)。
* 要瞭解如何將參數值作為檔傳入，請參閱[創建資源管理器參數檔](parameter-files.md)。
* 有關創建參數的建議，請參閱最佳實踐[- 參數](template-best-practices.md#parameters)。
