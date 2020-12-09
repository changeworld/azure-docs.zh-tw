---
title: 定義輸出值的多個實例
description: 使用 Azure Resource Manager 範本中的複製作業 (ARM 範本) ，以便在從部署傳回值時反復查看多次。
ms.topic: conceptual
ms.date: 04/17/2020
ms.openlocfilehash: 5ae20ed9ec3fdb3b76adbd370f5ba22f9386d613
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905939"
---
# <a name="output-iteration-in-arm-templates"></a>ARM 範本中的輸出反復專案

本文說明如何在 Azure Resource Manager 範本中為輸出建立一個以上的值 (ARM 範本) 。 藉由將 `copy` 專案新增至範本的輸出區段，您可以在部署期間以動態方式傳回數個專案。

您也可以搭配 [資源](copy-resources.md)、 [資源中的屬性](copy-properties.md)和 [變數](copy-variables.md)來使用複製。

## <a name="syntax"></a>語法

Copy 元素具有下列一般格式：

```json
"copy": {
  "count": <number-of-iterations>,
  "input": <values-for-the-output>
}
```

`count`屬性會指定您想要用於輸出值的反覆運算次數。

`input`屬性會指定您想要重複的屬性。 您可以建立一個由 `input` 屬性中的值建構的元素陣列。 它可以是單一屬性 (例如字串) ，或是具有數個屬性的物件。

## <a name="copy-limits"></a>複製限制

計數不能超過800。

計數不可為負數。 如果您使用最新版本的 Azure CLI、PowerShell 或 REST API 來部署範本，則可以為零。 具體而言，您必須使用：

* Azure PowerShell **2.6** 或更新版本
* Azure CLI **2.0.74 版** 或更新版本
* REST API **2019-05-10** 版或更新版本
* [連結的部署](linked-templates.md) 必須使用 API **2019-05-10** 版或更新版本，才能進行部署資源類型

舊版 PowerShell、CLI 和 REST API 不支援 count 的零。

## <a name="outputs-iteration"></a>輸出反復專案

下列範例會建立數量不定的儲存體帳戶，並傳回每個儲存體帳戶的端點：

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

* 若要進行教學課程，請參閱 [教學課程：使用 ARM 範本建立多個資源實例](template-tutorial-create-multiple-instances.md)。
* 如需複製元素的其他用途，請參閱：
  * [ARM 範本中的資源反復專案](copy-resources.md)
  * [ARM 範本中的屬性反復專案](copy-properties.md)
  * [ARM 範本中的變數反復專案](copy-variables.md)
* 如果您想要瞭解範本的區段，請參閱 [瞭解 ARM 範本的結構和語法](template-syntax.md)。
* 若要瞭解如何部署您的範本，請參閱 [使用 ARM 範本部署資源和 Azure PowerShell](deploy-powershell.md)。
