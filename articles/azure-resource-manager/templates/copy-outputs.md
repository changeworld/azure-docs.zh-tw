---
title: 定義輸出值的多個實例
description: 在 Azure 資源管理器範本中使用複製操作在從部署傳回值時多次反覆運算。
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: 3889260d02f438274c80e99e99136515499443e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153381"
---
# <a name="output-iteration-in-arm-templates"></a>ARM 範本中的輸出反覆運算

本文介紹如何在 Azure 資源管理器 （ARM） 範本中為輸出創建多個值。 通過將**複製**元素添加到範本的輸出部分，可以在部署期間動態返回多個項。

還可以將 copy 與[資源](copy-resources.md)、[資源中的屬性](copy-properties.md)和[變數](copy-variables.md)一起使用。

## <a name="outputs-iteration"></a>輸出反覆運算

複製元素具有以下常規格式：

```json
"copy": [
  {
    "count": <number-of-iterations>,
    "input": <values-for-the-variable>
  }
]
```

**count**屬性指定所需的輸出值的反覆運算次數。

**輸入**屬性指定要重複的屬性。 創建從**輸入**屬性中的值構造的元素陣列。 它可以是單個屬性（如字串），也可以是具有多個屬性的物件。

以下示例創建可變數量的存儲帳戶，並為每個存儲帳戶返回終結點：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageCount": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "variables": {
        "baseName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(copyIndex(), variables('baseName'))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": "[parameters('storageCount')]"
            }
        }
    ],
    "outputs": {
        "storageEndpoints": {
            "type": "array",
            "copy": {
                "count": "[parameters('storageCount')]",
                "input": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]"
            }
        }
    }
}
```

前面的範本返回具有以下值的陣列：

```json
[
    "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
    "https://1storagecfrbqnnmpeudi.blob.core.windows.net/"
]
```

下一個示例從新的存儲帳戶返回三個屬性。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageCount": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "variables": {
        "baseName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(copyIndex(), variables('baseName'))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": "[parameters('storageCount')]"
            }
        }
    ],
    "outputs": {
        "storageInfo": {
            "type": "array",
            "copy": {
                "count": "[parameters('storageCount')]",
                "input": {
                    "id": "[reference(concat(copyIndex(), variables('baseName')), '2019-04-01', 'Full').resourceId]",
                    "blobEndpoint": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]",
                    "status": "[reference(concat(copyIndex(), variables('baseName'))).statusOfPrimary]"
                }
            }
        }
    }
}
```

前面的示例返回具有以下值的陣列：

```json
[
    {
        "id": "Microsoft.Storage/storageAccounts/0storagecfrbqnnmpeudi",
        "blobEndpoint": "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
        "status": "available"
    },
    {
        "id": "Microsoft.Storage/storageAccounts/1storagecfrbqnnmpeudi",
        "blobEndpoint": "https://1storagecfrbqnnmpeudi.blob.core.windows.net/",
        "status": "available"
    }
]
```

## <a name="next-steps"></a>後續步驟

* 要流覽教程，請參閱[教程：使用 ARM 範本創建多個資源實例](template-tutorial-create-multiple-instances.md)。
* 有關複製元素的其他用途，請參閱：
  * [ARM 範本中的資源反覆運算](copy-resources.md)
  * [ARM 範本中的屬性反覆運算](copy-properties.md)
  * [ARM 範本中的可變反覆運算](copy-variables.md)
* 如果要瞭解範本的各個部分，請參閱[創作 ARM 範本](template-syntax.md)。
* 要瞭解如何部署範本，請參閱[使用 ARM 範本部署應用程式](deploy-powershell.md)。

