---
title: 定義輸出值的多個實例
description: 在 Azure Resource Manager 範本中使用複製作業，以在從部署傳回值時反復執行多次。
ms.topic: conceptual
ms.date: 04/17/2020
ms.openlocfilehash: 50c4b4b8f301ad88d3dfde98ace1aed4431693db
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82583420"
---
# <a name="output-iteration-in-arm-templates"></a>ARM 範本中的輸出反復專案

本文說明如何在您的 Azure Resource Manager （ARM）範本中為輸出建立一個以上的值。 藉由將**copy**元素新增至範本的 [輸出] 區段，您可以在部署期間動態傳回數個專案。

您也可以使用 [複製[資源](copy-resources.md)]、[資源中的屬性](copy-properties.md)和[變數](copy-variables.md)。

## <a name="syntax"></a>語法

Copy 元素具有下列一般格式：

```json
"copy": {
  "count": <number-of-iterations>,
  "input": <values-for-the-output>
}
```

**Count**屬性會指定您想要的輸出值反覆運算次數。

**輸入**屬性會指定您想要重複的屬性。 您會建立從**輸入**屬性中的值所構造的元素陣列。 它可以是單一屬性（例如字串）或具有數個屬性的物件。

## <a name="copy-limits"></a>複製限制

計數不能超過800。

計數不可為負數。 如果您使用最新版本的 Azure CLI、PowerShell 或 REST API 來部署範本，則可以是零。 具體而言，您必須使用：

* Azure PowerShell **2.6**或更新版本
* Azure CLI **2.0.74**或更新版本
* REST API **2019-05-10**版或更新版本
* [連結的部署](linked-templates.md)必須使用 API **2019-05-10**版或更新版本作為部署資源類型

舊版的 PowerShell、CLI 和 REST API 不支援 count 的零。

## <a name="outputs-iteration"></a>輸出反復專案

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

* 若要進行教學課程，請參閱[教學課程：使用 ARM 範本建立多個資源實例](template-tutorial-create-multiple-instances.md)。
* 如需 copy 元素的其他用法，請參閱：
  * [ARM 範本中的資源反復專案](copy-resources.md)
  * [ARM 範本中的屬性反復專案](copy-properties.md)
  * [ARM 範本中的變數反復專案](copy-variables.md)
* 如果您想要瞭解範本的各區段，請參閱[編寫 ARM 範本](template-syntax.md)。
* 若要瞭解如何部署您的範本，請參閱[使用 ARM 範本部署應用程式](deploy-powershell.md)。

