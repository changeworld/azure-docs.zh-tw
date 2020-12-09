---
title: 定義屬性的多個實例
description: 在 Azure Resource Manager 範本中使用複製作業 (ARM 範本) 在資源上建立屬性時反復查看多次。
ms.topic: conceptual
ms.date: 09/15/2020
ms.openlocfilehash: 446a303104e6b538129cd22d1f1fbbba6282b2ee
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905922"
---
# <a name="property-iteration-in-arm-templates"></a>ARM 範本中的屬性反復專案

本文說明如何在 Azure Resource Manager 範本中建立多個屬性的實例 (ARM 範本) 。 藉由將 `copy` 專案新增至範本中資源的屬性區段，您可以在部署期間動態設定屬性的專案數。 您也可以避免重複範本語法。

`copy`即使在套用至屬性時，您也只能搭配最上層資源使用 `copy` 。 若要瞭解如何將子資源變更為最上層資源，請參閱 [子資源的反復](copy-resources.md#iteration-for-a-child-resource)專案。

您也可以使用 [複製] 搭配 [資源](copy-resources.md)、 [變數](copy-variables.md)和 [輸出](copy-outputs.md)。

## <a name="syntax"></a>語法

Copy 元素具有下列一般格式：

```json
"copy": [
  {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "input": <values-for-the-property>
  }
]
```

針對 `name` ，提供您想要建立之資源屬性的名稱。

`count`屬性會指定您想要用於屬性的反覆運算次數。

`input`屬性會指定您想要重複的屬性。 您可以建立一個由 `input` 屬性中的值建構的元素陣列。

## <a name="copy-limits"></a>複製限制

計數不能超過800。

計數不可為負數。 如果您使用最新版本的 Azure CLI、PowerShell 或 REST API 來部署範本，則可以為零。 具體而言，您必須使用：

* Azure PowerShell **2.6** 或更新版本
* Azure CLI **2.0.74 版** 或更新版本
* REST API **2019-05-10** 版或更新版本
* [連結的部署](linked-templates.md) 必須使用 API **2019-05-10** 版或更新版本，才能進行部署資源類型

舊版 PowerShell、CLI 和 REST API 不支援 count 的零。

## <a name="property-iteration"></a>屬性反覆運算

下列範例將示範如何套用 `copy` 至 `dataDisks` 虛擬機器上的屬性：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numberOfDataDisks": {
      "type": "int",
      "minValue": 0,
      "maxValue": 16,
      "defaultValue": 16,
      "metadata": {
        "description": "The number of dataDisks to create."
      }
    },
    ...
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2017-03-30",
      ...
      "properties": {
        "storageProfile": {
          ...
          "copy": [
            {
              "name": "dataDisks",
              "count": "[parameters('numberOfDataDisks')]",
              "input": {
                "diskSizeGB": 1023,
                "lun": "[copyIndex('dataDisks')]",
                "createOption": "Empty"
              }
            }
          ]
        }
      }
    }
  ]
}
```

請注意，在屬性反覆運算內使用 `copyIndex` 時，您必須提供反覆運算的名稱。 屬性反復專案也支援 offset 引數。 位移必須在反覆運算的名稱之後，例如 `copyIndex('dataDisks', 1)` 。

Resource Manager 會在部署期間展開 `copy` 陣列。 陣列名稱會變成屬性名稱。 輸入值會變成物件屬性。 已部署的範本會變成：

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2017-03-30",
  "properties": {
    "storageProfile": {
      "dataDisks": [
        {
          "lun": 0,
          "createOption": "Empty",
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "createOption": "Empty",
          "diskSizeGB": 1023
        },
        {
          "lun": 2,
          "createOption": "Empty",
          "diskSizeGB": 1023
        }
      ],
      ...
```

使用陣列時，複製作業會有幫助，因為您可以逐一查看陣列中的每個項目。 使用陣列上的 `length` 函式指定反覆運算的計數，並使用 `copyIndex` 來擷取陣列中目前的索引。

下列範例範本會針對以陣列形式傳入的資料庫建立容錯移轉群組。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "primaryServerName": {
            "type": "string"
        },
        "secondaryServerName": {
            "type": "string"
        },
        "databaseNames": {
            "type": "array",
            "defaultValue": [
                "mydb1",
                "mydb2",
                "mydb3"
            ]
        }
    },
    "variables": {
        "failoverName": "[concat(parameters('primaryServerName'),'/', parameters('primaryServerName'),'failovergroups')]"
    },
    "resources": [
        {
            "type": "Microsoft.Sql/servers/failoverGroups",
            "apiVersion": "2015-05-01-preview",
            "name": "[variables('failoverName')]",
            "properties": {
                "readWriteEndpoint": {
                    "failoverPolicy": "Automatic",
                    "failoverWithDataLossGracePeriodMinutes": 60
                },
                "readOnlyEndpoint": {
                    "failoverPolicy": "Disabled"
                },
                "partnerServers": [
                    {
                        "id": "[resourceId('Microsoft.Sql/servers', parameters('secondaryServerName'))]"
                    }
                ],
                "copy": [
                    {
                        "name": "databases",
                        "count": "[length(parameters('databaseNames'))]",
                        "input": "[resourceId('Microsoft.Sql/servers/databases', parameters('primaryServerName'), parameters('databaseNames')[copyIndex('databases')])]"
                    }
                ]
            }
        }
    ],
    "outputs": {
    }
}
```

`copy`元素是陣列，因此您可以為資源指定一個以上的屬性。

```json
{
  "type": "Microsoft.Network/loadBalancers",
  "apiVersion": "2017-10-01",
  "name": "exampleLB",
  "properties": {
    "copy": [
      {
        "name": "loadBalancingRules",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      },
      {
        "name": "probes",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      }
    ]
  }
}
```

您可以一起使用資源和屬性反覆運算。 依名稱參考屬性反覆運算。

```json
{
  "type": "Microsoft.Network/virtualNetworks",
  "apiVersion": "2018-04-01",
  "name": "[concat(parameters('vnetname'), copyIndex())]",
  "copy":{
    "count": 2,
    "name": "vnetloop"
  },
  "location": "[resourceGroup().location]",
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "[parameters('addressPrefix')]"
      ]
    },
    "copy": [
      {
        "name": "subnets",
        "count": 2,
        "input": {
          "name": "[concat('subnet-', copyIndex('subnets'))]",
          "properties": {
            "addressPrefix": "[variables('subnetAddressPrefix')[copyIndex('subnets')]]"
          }
        }
      }
    ]
  }
}
```

## <a name="example-templates"></a>範本的範例

下列範例顯示為屬性建立多個值的常見案例。

|[範本]  |描述  |
|---------|---------|
|[以可變的資料磁碟數目部署 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |利用虛擬機器部署數個資料磁碟。 |

## <a name="next-steps"></a>後續步驟

* 若要進行教學課程，請參閱 [教學課程：使用 ARM 範本建立多個資源實例](template-tutorial-create-multiple-instances.md)。
* 如需複製元素的其他用途，請參閱：
  * [ARM 範本中的資源反復專案](copy-resources.md)
  * [ARM 範本中的變數反復專案](copy-variables.md)
  * [ARM 範本中的輸出反復專案](copy-outputs.md)
* 如果您想要瞭解範本的區段，請參閱 [瞭解 ARM 範本的結構和語法](template-syntax.md)。
* 若要瞭解如何部署您的範本，請參閱 [使用 ARM 範本部署資源和 Azure PowerShell](deploy-powershell.md)。
