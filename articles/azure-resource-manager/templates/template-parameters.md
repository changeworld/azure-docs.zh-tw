---
title: 範本中的參數
description: 描述如何在 Azure Resource Manager 範本中定義參數。
ms.topic: conceptual
ms.date: 09/05/2019
ms.openlocfilehash: 89c6984c587e8dae59c1825a99d4f8da1c06dafb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "76122418"
---
# <a name="parameters-in-azure-resource-manager-templates"></a>Azure Resource Manager 範本中的參數

本文說明如何定義及使用 Azure Resource Manager 範本中的參數。 藉由提供不同的參數值，您可以針對不同的環境重複使用範本。

Resource Manager 會在開始部署作業之前，先解析參數值。 在範本中使用參數的任何地方，Resource Manager 都會以已解析的值來取代它。

## <a name="define-parameter"></a>定義參數

下列範例顯示簡單的參數定義。 它會定義名為**storageSKU**的參數。 參數是字串值，且只接受適用于其預期用途的值。 當部署期間未提供任何值時，參數會使用預設值。

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

在範本中，您可以使用[parameters](template-functions-deployment.md#parameters)函數來參考參數的值。 在下列範例中，會使用參數值來設定儲存體帳戶的 SKU。

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

為參數指定預設值時，您可以使用大部分的範本函式。 您可以使用另一個參數以建立預設值。 下列範本示範如何使用預設值中的函數。 未提供網站的名稱時，它會建立唯一的字串值，並將它附加至**網站**。 當沒有提供主機方案的名稱時，它會採用網站的值和附加**方案**。

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

您不能在 parameters 區段中使用[reference](template-functions-resource.md#reference)函式或任何[清單](template-functions-resource.md#list)函數。 這些函式會取得資源的執行時間狀態，而且在解析參數時，無法在部署之前執行。

## <a name="objects-as-parameters"></a>作為參數的物件

藉由將相關值以物件的方式傳遞，可以更輕鬆地進行組織。 此方法也會減少範本中的參數數目。

下列範例會顯示屬於物件的參數。 預設值會顯示物件的預期屬性。

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

您可以使用點運算子來參考物件的屬性。

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

下列範例示範使用參數的案例。

|[範本]  |描述  |
|---------|---------|
|[具有預設值之帶有函式的參數](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | 示範定義參數的預設值時，如何使用範本函式。 範本不會部署任何資源。 它會建構參數值，並傳回這些值。 |
|[parameter 物件](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | 示範如何針對參數使用物件。 範本不會部署任何資源。 它會建構參數值，並傳回這些值。 |


## <a name="next-steps"></a>後續步驟

* 若要瞭解參數可用的屬性，請參閱[瞭解 Azure Resource Manager 範本的結構和語法](template-syntax.md)。
* 若要瞭解如何以檔案的形式傳入參數值，請參閱[建立 Resource Manager 參數](parameter-files.md)檔案。
* 如需有關建立參數的建議，請參閱[最佳做法-參數](template-best-practices.md#parameters)。
