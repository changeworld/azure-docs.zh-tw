---
title: 定義輸出值的多個實例
description: 在 Azure Resource Manager 範本中使用複製作業，以在從部署傳回值時反復執行多次。
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: db5c548c7bd4c60357d3656b1273b0192c497459
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2020
ms.locfileid: "77624770"
---
# <a name="output-iteration-in-azure-resource-manager-templates"></a>Azure Resource Manager 範本中的輸出反復專案

本文說明如何為 Azure Resource Manager 範本中的輸出建立一個以上的值。 藉由將**copy**元素新增至範本的 [輸出] 區段，您可以在部署期間動態傳回數個專案。

您也可以使用 [複製[資源](copy-resources.md)]、[資源中的屬性](copy-properties.md)和[變數](copy-variables.md)。

## <a name="outputs-iteration"></a>輸出反復專案

Copy 元素具有下列一般格式：

```json
"copy": [
  {
    "count": <number-of-iterations>,
    "input": <values-for-the-variable>
  }
]
```

**Count**屬性會指定您想要的輸出值反覆運算次數。

**輸入**屬性會指定您想要重複的屬性。 您會建立從**輸入**屬性中的值所構造的元素陣列。 它可以是單一屬性（例如字串）或具有數個屬性的物件。

下列範例會建立變數數目的儲存體帳戶，並傳回每個儲存體帳戶的端點：

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

上述範本會傳回具有下列值的陣列：

```json
[
    "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
    "https://1storagecfrbqnnmpeudi.blob.core.windows.net/"
]
```

下一個範例會從新的儲存體帳戶傳回三個屬性。

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

上述範例會傳回具有下列值的陣列：

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

* 如須逐步瀏覽教學課程，請參閱[教學課程：使用 Resource Manager 範本建立多個資源執行個體](template-tutorial-create-multiple-instances.md)。
* 如需 copy 元素的其他用法，請參閱：
  * [Azure Resource Manager 範本中的資源反復專案](copy-resources.md)
  * [Azure Resource Manager 範本中的屬性反復專案](copy-properties.md)
  * [Azure Resource Manager 範本中的變數反復專案](copy-variables.md)
* 若要了解範本區段的相關資訊，請參閱[編寫 Azure Resource Manager 範本](template-syntax.md)。
* 若要了解如何部署範本，請參閱 [使用 Azure 資源管理員範本部署應用程式](deploy-powershell.md)。

