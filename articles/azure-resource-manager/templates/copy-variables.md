---
title: 定義變數的多個實例
description: 在 Azure Resource Manager 範本中使用複製作業，在建立變數時反復執行多次。
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: aca69dd858c7a940592e74123b97b8d364d9e11c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84678438"
---
# <a name="variable-iteration-in-arm-templates"></a>ARM 範本中的變數反復專案

本文說明如何在您的 Azure Resource Manager （ARM）範本中為變數建立一個以上的值。 藉由將**copy**元素新增至範本的 variables 區段，您可以在部署期間，動態設定變數的專案數。 您也可以避免重複範本語法。

您也可以使用 [複製[資源](copy-resources.md)]、[資源中的屬性](copy-properties.md)和 [[輸出](copy-outputs.md)]。

## <a name="syntax"></a>Syntax

Copy 元素具有下列一般格式：

```json
"copy": [
  {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "input": <values-for-the-variable>
  }
]
```

**Name**屬性是識別迴圈的任何值。 **Count**屬性會指定您想要用於變數的反覆運算次數。

**輸入**屬性會指定您想要重複的屬性。 您會建立從**輸入**屬性中的值所構造的元素陣列。 它可以是單一屬性（例如字串）或具有數個屬性的物件。

## <a name="copy-limits"></a>複製限制

計數不能超過800。

計數不可為負數。 如果您使用最新版本的 Azure CLI、PowerShell 或 REST API 來部署範本，則可以是零。 具體而言，您必須使用：

* Azure PowerShell **2.6**或更新版本
* Azure CLI **2.0.74**或更新版本
* REST API **2019-05-10**版或更新版本
* [連結的部署](linked-templates.md)必須使用 API **2019-05-10**版或更新版本作為部署資源類型

舊版的 PowerShell、CLI 和 REST API 不支援 count 的零。

## <a name="variable-iteration"></a>變數反覆項目

下列範例顯示如何建立字串值的陣列：

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

上述範本會傳回具有下列值的陣列：

```json
[
    "item1",
    "item2",
    "item3",
    "item4",
    "item5"
]
```

下一個範例顯示如何建立具有三個屬性的物件陣列-name、diskSizeGB 和 diskIndex。

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

上述範例會傳回具有下列值的陣列：

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
> 變數反復專案支援 offset 引數。 位移必須位於反復專案的名稱之後，例如 copyIndex （' diskNames '，1）。 如果您未提供位移值，第一個實例的預設值會是0。
>

您也可以在變數內使用 copy 元素。 下列範例會建立一個物件，其具有陣列做為其中一個值。

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

上述範例會傳回具有下列值的物件：

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

下一個範例顯示您可以使用 copy 搭配變數的不同方式。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

## <a name="example-templates"></a>範本的範例

下列範例顯示為變數建立一個以上值的常見案例。

|[範本]  |描述  |
|---------|---------|
|[複製變數](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) |示範逐一查看變數的不同方式。 |
|[多個安全性規則](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |將數個安全性規則部署至網路安全性群組。 從參數建構安全性規則。 如需參數，請參閱[多個 NSG 參數檔案](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json)。 |

## <a name="next-steps"></a>後續步驟

* 若要進行教學課程，請參閱[教學課程：使用 ARM 範本建立多個資源實例](template-tutorial-create-multiple-instances.md)。
* 如需 copy 元素的其他用法，請參閱：
  * [ARM 範本中的資源反復專案](copy-resources.md)
  * [ARM 範本中的屬性反復專案](copy-properties.md)
  * [ARM 範本中的輸出反復專案](copy-outputs.md)
* 如果您想要瞭解範本的各區段，請參閱[編寫 ARM 範本](template-syntax.md)。
* 若要瞭解如何部署您的範本，請參閱[使用 ARM 範本部署應用程式](deploy-powershell.md)。

