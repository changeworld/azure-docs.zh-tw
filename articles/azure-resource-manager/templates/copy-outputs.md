---
title: 定義輸出值的多個實體
description: 在 Azure 資源管理器範本中使用複製操作在從部署返回值時多次反覆運算。
ms.topic: conceptual
ms.date: 04/17/2020
ms.openlocfilehash: 0315af2f083285c4704b08fec608341b6f0b2231
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617833"
---
# <a name="output-iteration-in-arm-templates"></a>ARM 樣本輸出的影像

本文介紹如何在 Azure 資源管理員 (ARM) 範本中為輸出創建多個值。 通過將**複製**元素添加到範本的輸出部分,可以在部署期間動態返回多個項。

還可以將 copy 與[資源](copy-resources.md)、[資源中的屬性](copy-properties.md)和[變數](copy-variables.md)一起使用。

## <a name="outputs-iteration"></a>輸出反覆運算

複製元素具有以下一般格式:

```json
"copy": {
  "count": <number-of-iterations>,
  "input": <values-for-the-output>
}
```

**count**屬性指定所需的輸出值的反覆運算次數。

**輸入**屬性指定要重複的屬性。 創建從**輸入**屬性中的值建構的元素陣列。 它可以是單個屬性(如字串),也可以是具有多個屬性的物件。

以下範例建立可變數量的儲存帳戶,並為每個儲存帳戶傳回終結點:

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

前面的樣本傳回有以下值的陣列:

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

前面的範例傳回有以下值的陣列:

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

* 要瀏覽教學,請參閱[教學:使用 ARM 樣本建立多個資源實例](template-tutorial-create-multiple-instances.md)。
* 有關複製元素的其他用途,請參閱:
  * [ARM 樣本資源反覆運算](copy-resources.md)
  * [ARM 樣本內容的屬性反覆運算](copy-properties.md)
  * [ARM 樣本中的可變反覆運算](copy-variables.md)
* 如果要瞭解範本的各個部分,請參閱[創作 ARM 範本](template-syntax.md)。
* 要瞭解如何部署樣本,請參閱[使用 ARM 範本部署應用程式](deploy-powershell.md)。

