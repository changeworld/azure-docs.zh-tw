---
title: 定義屬性的多個實體
description: 在 Azure 資源管理器樣本中使用複製操作在資源上創建屬性時多次反覆運算。
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 831ae1af202a1cdf52bdd2bdf0d9a042a97ba52f
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81391329"
---
# <a name="property-iteration-in-arm-templates"></a>ARM 樣本內容的屬性反覆運算

本文介紹如何在 Azure 資源管理員 (ARM) 範本中創建多個屬性實例。 通過將**複製**元素添加到範本中資源的屬性部分,可以在部署期間動態設置屬性的項數。 您還避免重複範本語法。

您可以使用複製與[資源](copy-resources.md)、[變數](copy-variables.md)與[輸出](copy-outputs.md)。

## <a name="property-iteration"></a>屬性反覆運算

複製元素具有以下一般格式:

```json
"copy": [
  {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "input": <values-for-the-property>
  }
]
```

對於**名稱**,請提供要建立的資源屬性的名稱。

**count**屬性指定所需的屬性的反覆運算數。

**輸入**屬性指定要重複的屬性。 創建從**輸入**屬性中的值建構的元素陣列。

下列範例示範如何將 `copy` 套用至虛擬機器的 dataDisks 屬性：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

請注意，在屬性反覆運算內使用 `copyIndex` 時，您必須提供反覆運算的名稱。 屬性反覆運算還支援偏移參數。 偏移量必須以反覆運算的名稱(如 copyIndex("資料磁碟"1)之後提供。

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

以下範例範本為作為陣列傳入的資料庫創建故障轉移組。

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

copy 元素為一個陣列，因此，您可以針對資源指定一個以上的屬性。

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

## <a name="copy-limits"></a>複製限制

計數不能超過 800。

計數不能為負數。 如果部署具有 Azure PowerShell 2.6 或更高版本、Azure CLI 2.0.74 或更高版本或 REST API 版本**2019-05-10**或更高版本的範本,則可以將計數設置為零。 早期版本的 PowerShell、CLI 和 REST API 不支援零計數。

## <a name="example-templates"></a>範本的範例

下面的範例顯示了為屬性創建多個值的常見方案。

|[範本]  |描述  |
|---------|---------|
|[以可變的資料磁碟數目部署 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |利用虛擬機器部署數個資料磁碟。 |

## <a name="next-steps"></a>後續步驟

* 要瀏覽教學,請參閱[教學:使用 ARM 樣本建立多個資源實例](template-tutorial-create-multiple-instances.md)。
* 有關複製元素的其他用途,請參閱:
  * [ARM 樣本資源反覆運算](copy-resources.md)
  * [ARM 樣本中的可變反覆運算](copy-variables.md)
  * [ARM 樣本輸出的影像](copy-outputs.md)
* 如果要瞭解範本的各個部分,請參閱[創作 ARM 範本](template-syntax.md)。
* 要瞭解如何部署樣本,請參閱[使用 ARM 範本部署應用程式](deploy-powershell.md)。

