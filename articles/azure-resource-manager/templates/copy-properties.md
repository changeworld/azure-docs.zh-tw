---
title: 定義屬性的多個實例
description: 在 Azure 資源管理器範本中使用複製操作在資源上創建屬性時多次反覆運算。
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: e86d38b0e5d2e39d54b3c419b6eebdcda74022db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258102"
---
# <a name="property-iteration-in-arm-templates"></a>ARM 範本中的屬性反覆運算

本文介紹如何在 Azure 資源管理器 （ARM） 範本中創建多個屬性實例。 通過將**複製**元素添加到範本中資源的屬性部分，可以在部署期間動態設置屬性的項數。 您還避免重複範本語法。

您還可以使用複製與[資源](copy-resources.md)、[變數](copy-variables.md)和[輸出](copy-outputs.md)。

## <a name="property-iteration"></a>屬性反覆運算

複製元素具有以下常規格式：

```json
"copy": [
  {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "input": <values-for-the-property>
  }
]
```

對於**名稱**，請提供要創建的資源屬性的名稱。 **count**屬性指定所需的屬性的反覆運算數。

**輸入**屬性指定要重複的屬性。 創建從**輸入**屬性中的值構造的元素陣列。

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

請注意，在屬性反覆運算內使用 `copyIndex` 時，您必須提供反覆運算的名稱。

> [!NOTE]
> 屬性反覆運算還支援偏移參數。 偏移量必須以反覆運算的名稱（如 copyIndex（"資料磁片"1）之後提供。
>

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

計數不能為負數。 如果部署具有 Azure PowerShell 2.6 或更高版本、Azure CLI 2.0.74 或更高版本或 REST API 版本**2019-05-10**或更高版本的範本，則可以將計數設置為零。 早期版本的 PowerShell、CLI 和 REST API 不支援零計數。

## <a name="example-templates"></a>範本的範例

下面的示例顯示了為屬性創建多個值的常見方案。

|[範本]  |描述  |
|---------|---------|
|[以可變的資料磁碟數目部署 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |利用虛擬機器部署數個資料磁碟。 |

## <a name="next-steps"></a>後續步驟

* 要流覽教程，請參閱[教程：使用 ARM 範本創建多個資源實例](template-tutorial-create-multiple-instances.md)。
* 有關複製元素的其他用途，請參閱：
  * [ARM 範本中的資源反覆運算](copy-resources.md)
  * [ARM 範本中的可變反覆運算](copy-variables.md)
  * [ARM 範本中的輸出反覆運算](copy-outputs.md)
* 如果要瞭解範本的各個部分，請參閱[創作 ARM 範本](template-syntax.md)。
* 要瞭解如何部署範本，請參閱[使用 ARM 範本部署應用程式](deploy-powershell.md)。

