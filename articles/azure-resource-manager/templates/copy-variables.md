---
title: 定義變數的多個實例
description: 在 Azure 資源管理器範本中使用複製操作在創建變數時多次反覆運算。
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: ed0c2d87c48a18b0a065f6c76e1e69142a9df048
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153296"
---
# <a name="variable-iteration-in-arm-templates"></a>ARM 範本中的可變反覆運算

本文介紹如何在 Azure 資源管理器 （ARM） 範本中為變數創建多個值。 通過將**複製**元素添加到範本的變數部分，可以在部署期間動態設置變數的項數。 您還避免重複範本語法。

還可以將 copy 與[資源](copy-resources.md)、[資源中的屬性](copy-properties.md)和[輸出](copy-outputs.md)一起使用 。

## <a name="variable-iteration"></a>變數反覆項目

複製元素具有以下常規格式：

```json
"copy": [
  {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "input": <values-for-the-variable>
  }
]
```

**名稱**屬性是標識迴圈的任何值。 **count**屬性指定變數所需的反覆運算次數。

**輸入**屬性指定要重複的屬性。 創建從**輸入**屬性中的值構造的元素陣列。 它可以是單個屬性（如字串），也可以是具有多個屬性的物件。

下面的示例演示如何創建字串值陣列：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "itemCount": {
            "type": "int",
            "defaultValue": 5
        }
     },
    "variables": {
        "copy": [
            {
                "name": "stringArray",
                "count": "[parameters('itemCount')]",
                "input": "[concat('item', copyIndex('stringArray', 1))]"
            }
        ]
    },
    "resources": [],
    "outputs": {
        "arrayResult": {
            "type": "array",
            "value": "[variables('stringArray')]"
        }
    }
}
```

前面的範本返回具有以下值的陣列：

```json
[
    "item1",
    "item2",
    "item3",
    "item4",
    "item5"
]
```

下一個示例演示如何創建具有三個屬性的物件陣列 - 名稱、磁片大小GB 和 diskIndex。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "itemCount": {
            "type": "int",
            "defaultValue": 5
        }
    },
    "variables": {
        "copy": [
            {
                "name": "objectArray",
                "count": "[parameters('itemCount')]",
                "input": {
                    "name": "[concat('myDataDisk', copyIndex('objectArray', 1))]",
                    "diskSizeGB": "1",
                    "diskIndex": "[copyIndex('objectArray')]"
                }
            }
        ]
    },
    "resources": [],
    "outputs": {
        "arrayResult": {
            "type": "array",
            "value": "[variables('objectArray')]"
        }
    }
}
```

前面的示例返回具有以下值的陣列：

```json
[
    {
        "name": "myDataDisk1",
        "diskSizeGB": "1",
        "diskIndex": 0
    },
    {
        "name": "myDataDisk2",
        "diskSizeGB": "1",
        "diskIndex": 1
    },
    {
        "name": "myDataDisk3",
        "diskSizeGB": "1",
        "diskIndex": 2
    },
    {
        "name": "myDataDisk4",
        "diskSizeGB": "1",
        "diskIndex": 3
    },
    {
        "name": "myDataDisk5",
        "diskSizeGB": "1",
        "diskIndex": 4
    }
]
```

> [!NOTE]
> 變數反覆運算支援偏移參數。 偏移量必須以反覆運算的名稱（如 copyIndex（"磁片名稱"1）之後提供。 如果不提供偏移值，則第一個實例預設為 0。
>

您還可以在變數中使用複製元素。 下面的示例創建一個物件，該物件具有陣列作為其值之一。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "itemCount": {
            "type": "int",
            "defaultValue": 5
        }
    },
    "variables": {
        "topLevelObject": {
            "sampleProperty": "sampleValue",
            "copy": [
                {
                    "name": "disks",
                    "count": "[parameters('itemCount')]",
                    "input": {
                        "name": "[concat('myDataDisk', copyIndex('disks', 1))]",
                        "diskSizeGB": "1",
                        "diskIndex": "[copyIndex('disks')]"
                    }
                }
            ]
        }
    },
    "resources": [],
    "outputs": {
        "objectResult": {
            "type": "object",
            "value": "[variables('topLevelObject')]"
        }
    }
}
```

前面的示例返回具有以下值的物件：

```json
{
    "sampleProperty": "sampleValue",
    "disks": [
        {
            "name": "myDataDisk1",
            "diskSizeGB": "1",
            "diskIndex": 0
        },
        {
            "name": "myDataDisk2",
            "diskSizeGB": "1",
            "diskIndex": 1
        },
        {
            "name": "myDataDisk3",
            "diskSizeGB": "1",
            "diskIndex": 2
        },
        {
            "name": "myDataDisk4",
            "diskSizeGB": "1",
            "diskIndex": 3
        },
        {
            "name": "myDataDisk5",
            "diskSizeGB": "1",
            "diskIndex": 4
        }
    ]
}
```

下一個示例顯示了將複製與變數一起使用的不同方法。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {
    "disk-array-on-object": {
      "copy": [
        {
          "name": "disks",
          "count": 5,
          "input": {
            "name": "[concat('myDataDisk', copyIndex('disks', 1))]",
            "diskSizeGB": "1",
            "diskIndex": "[copyIndex('disks')]"
          }
        },
        {
          "name": "diskNames",
          "count": 5,
          "input": "[concat('myDataDisk', copyIndex('diskNames', 1))]"
        }
      ]
    },
    "copy": [
      {
        "name": "top-level-object-array",
        "count": 5,
        "input": {
          "name": "[concat('myDataDisk', copyIndex('top-level-object-array', 1))]",
          "diskSizeGB": "1",
          "diskIndex": "[copyIndex('top-level-object-array')]"
        }
      },
      {
        "name": "top-level-string-array",
        "count": 5,
        "input": "[concat('myDataDisk', copyIndex('top-level-string-array', 1))]"
      },
      {
        "name": "top-level-integer-array",
        "count": 5,
        "input": "[copyIndex('top-level-integer-array')]"
      }
    ]
  },
  "resources": [],
  "outputs": {
    "exampleObject": {
      "value": "[variables('disk-array-on-object')]",
      "type": "object"
    },
    "exampleArrayOnObject": {
      "value": "[variables('disk-array-on-object').disks]",
      "type" : "array"
    },
    "exampleObjectArray": {
      "value": "[variables('top-level-object-array')]",
      "type" : "array"
    },
    "exampleStringArray": {
      "value": "[variables('top-level-string-array')]",
      "type" : "array"
    },
    "exampleIntegerArray": {
      "value": "[variables('top-level-integer-array')]",
      "type" : "array"
    }
  }
}
```

## <a name="copy-limits"></a>複製限制

計數不能超過 800。

計數不能為負數。 如果部署具有 Azure PowerShell 2.6 或更高版本、Azure CLI 2.0.74 或更高版本或 REST API 版本**2019-05-10**或更高版本的範本，則可以將計數設置為零。 早期版本的 PowerShell、CLI 和 REST API 不支援零計數。

## <a name="example-templates"></a>範本的範例

以下示例顯示了為變數創建多個值的常見方案。

|[範本]  |描述  |
|---------|---------|
|[複製變數](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) |示範逐一查看變數的不同方式。 |
|[多個安全性規則](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |將數個安全性規則部署至網路安全性群組。 從參數建構安全性規則。 如需參數，請參閱[多個 NSG 參數檔案](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json)。 |

## <a name="next-steps"></a>後續步驟

* 要流覽教程，請參閱[教程：使用 ARM 範本創建多個資源實例](template-tutorial-create-multiple-instances.md)。
* 有關複製元素的其他用途，請參閱：
  * [ARM 範本中的資源反覆運算](copy-resources.md)
  * [ARM 範本中的屬性反覆運算](copy-properties.md)
  * [ARM 範本中的輸出反覆運算](copy-outputs.md)
* 如果要瞭解範本的各個部分，請參閱[創作 ARM 範本](template-syntax.md)。
* 要瞭解如何部署範本，請參閱[使用 ARM 範本部署應用程式](deploy-powershell.md)。

