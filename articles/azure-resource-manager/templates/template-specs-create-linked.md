---
title: 使用連結的範本建立範本規格
description: 瞭解如何使用連結的範本來建立範本規格。
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: b952baa465092fef19ad2feb11a43328a6177d1c
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87387858"
---
# <a name="tutorial-create-a-template-spec-with-linked-templates-preview"></a>教學課程：使用連結的範本建立範本規格（預覽）

瞭解如何使用[連結的範本](linked-templates.md#linked-template)來建立[範本規格](template-specs.md)。 您可以使用範本規格，與組織中的其他使用者共用 ARM 範本。 本文說明如何使用 `relativePath` [部署資源](/azure/templates/microsoft.resources/deployments)的新屬性，建立範本規格來封裝主要範本及其連結的範本。

## <a name="prerequisites"></a>必要條件

具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

> [!NOTE]
> 範本規格目前為預覽狀態。 若要加以使用，您必須[註冊預覽版](https://aka.ms/templateSpecOnboarding)。

## <a name="create-linked-templates"></a>建立連結的範本

建立主要範本和連結的範本。

若要連結範本，請將[部署資源](/azure/templates/microsoft.resources/deployments)新增至您的主要範本。 在 `templateLink` 屬性中，根據父範本的路徑指定連結範本的相對路徑。

連結的範本會**在上linkedTemplate.js**呼叫，並儲存在主要範本儲存所在路徑中名為成品的子**資料夾中。**  您可以使用下列其中一個 relativePath 值：

- `./artifacts/linkedTemplate.json`
- `/artifacts/linkedTemplate.json`
- `artifacts/linkedTemplate.json`

`relativePath`屬性一律相對於宣告的範本檔案 `relativePath` ，因此，如果從上的 linkedTemplate.js呼叫了另一個 linkedTemplate2.js，而 linkedTemplate2.js上的則儲存在相同的成品子資料夾中，則在 linkedTemplate.js中指定的 relativePath 就是 `linkedTemplate2.json` 。

1. 使用下列 JSON 建立主要範本。 將azuredeploy.js的主要範本儲存到本機電腦**上**。 本教學課程假設您已儲存至的路徑**c:\Templates\linkedTS\azuredeploy.js** ，但您可以使用任何路徑。

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "location": {
          "type": "string",
          "defaultValue": "westus2",
          "metadata":{
            "description": "Specify the location for the resources."
          }
        },
        "storageAccountType": {
          "type": "string",
          "defaultValue": "Standard_LRS",
          "metadata":{
            "description": "Specify the storage account type."
          }
        }
      },
      "variables": {
        "appServicePlanName": "[concat('plan', uniquestring(resourceGroup().id))]"
      },
      "resources": [
        {
          "type": "Microsoft.Web/serverfarms",
          "apiVersion": "2016-09-01",
          "name": "[variables('appServicePlanName')]",
          "location": "[parameters('location')]",
          "sku": {
            "name": "B1",
            "tier": "Basic",
            "size": "B1",
            "family": "B",
            "capacity": 1
          },
          "kind": "linux",
          "properties": {
            "perSiteScaling": false,
            "reserved": true,
            "targetWorkerCount": 0,
            "targetWorkerSizeId": 0
          }
        },
        {
          "type": "Microsoft.Resources/deployments",
          "apiVersion": "2020-06-01",
          "name": "createStorage",
          "properties": {
            "mode": "Incremental",
            "templateLink": {
              "relativePath": "artifacts/linkedTemplate.json"
            },
            "parameters": {
              "storageAccountType": {
                "value": "[parameters('storageAccountType')]"
              }
            }
          }
        }
      ]
    }
    ```

    > [!NOTE]
    > 的 apiVersion `Microsoft.Resources/deployments` 必須是2020-06-01 或更新版本。

1. 在儲存主要範本的資料夾中，**建立名為成品的目錄**。
1. 使用下列 JSON 建立連結的範本：

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "storageAccountType": {
          "type": "string",
          "defaultValue": "Standard_LRS",
          "allowedValues": [
            "Standard_LRS",
            "Standard_GRS",
            "Standard_ZRS",
            "Premium_LRS"
          ],
          "metadata": {
            "description": "Storage Account type"
          }
        },
        "location": {
          "type": "string",
          "defaultValue": "[resourceGroup().location]",
          "metadata": {
            "description": "Location for all resources."
          }
        }
      },
      "variables": {
        "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
      },
      "resources": [
        {
          "type": "Microsoft.Storage/storageAccounts",
          "apiVersion": "2019-04-01",
          "name": "[variables('storageAccountName')]",
          "location": "[parameters('location')]",
          "sku": {
            "name": "[parameters('storageAccountType')]"
          },
          "kind": "StorageV2",
          "properties": {}
        }
      ],
      "outputs": {
        "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
        }
      }
    }
    ```

1. 在 [成品 **] 資料夾中，將**範本儲存為**linkedTemplate.js** 。

## <a name="create-template-spec"></a>建立範本規格

範本規格會儲存在資源群組中。  建立資源群組，然後使用下列腳本建立範本規格。 範本規格名稱是**webSpec**。

```azurepowershell
New-AzResourceGroup `
  -Name templateSpecRG `
  -Location westus2

New-AzTemplateSpec `
  -ResourceGroupName templateSpecRG `
  -Name webSpec `
  -Version "1.0.0.0" `
  -Location westus2 `
  -TemplateJsonFile "c:\Templates\linkedTS\azuredeploy.json"
```

當您完成時，您可以從 Azure 入口網站或使用下列 Cmdlet 來查看範本規格：

```azurepowershell-interactive
Get-AzTemplateSpec -ResourceGroupName templatespecRG -Name webSpec
```

## <a name="deploy-template-spec"></a>部署範本規格

您現在可以部署範本規格。部署範本規格就如同部署其包含的範本，不同的是您會傳入範本規格的資源識別碼。您可以使用相同的部署命令，並視需要傳入範本規格的參數值。

```azurepowershell
New-AzResourceGroup `
  -Name webRG `
  -Location westus2

$id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name webSpec -Version "1.0.0.0").Version.Id

New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName webRG
```

## <a name="next-steps"></a>後續步驟

若要瞭解如何將範本規格部署為連結的範本，請參閱[教學課程：將範本規格部署為連結的範本](template-specs-deploy-linked-template.md)。
