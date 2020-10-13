---
title: 使用連結的範本建立範本規格
description: 瞭解如何使用連結的範本建立範本規格。
ms.topic: conceptual
ms.date: 08/31/2020
ms.openlocfilehash: adcce8194f380b90eb9a29f4da25763e112b9f12
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91728534"
---
# <a name="tutorial-create-a-template-spec-with-linked-templates-preview"></a>教學課程：使用連結的範本建立範本規格 (預覽) 

瞭解如何使用[連結的範本](linked-templates.md#linked-template)建立[範本規格](template-specs.md)。 您可以使用範本規格，與組織中的其他使用者共用 ARM 範本。 本文說明如何使用 `relativePath` [部署資源](/azure/templates/microsoft.resources/deployments)的屬性，建立範本規格來封裝主要範本及其連結的範本。

## <a name="prerequisites"></a>必要條件

具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

> [!NOTE]
> 範本規格目前為預覽狀態。 若要加以使用，您必須[註冊預覽版](https://aka.ms/templateSpecOnboarding)。

## <a name="create-linked-templates"></a>建立連結的範本

建立主要範本和連結的範本。

若要連結範本，請將 [部署資源](/azure/templates/microsoft.resources/deployments) 新增至您的主要範本。 在 `templateLink` 屬性中，根據父範本的路徑指定連結範本的相對路徑。

連結的範本會 ** 在上呼叫linkedTemplate.js**，並儲存在儲存主要範本的路徑中 **，稱為成品** 的子資料夾。  您可以使用下列其中一個 relativePath 值：

- `./artifacts/linkedTemplate.json`
- `/artifacts/linkedTemplate.json`
- `artifacts/linkedTemplate.json`

`relativePath`屬性一律相對於宣告的範本檔案 `relativePath` ，因此，如果在上有另一個從 linkedTemplate.js呼叫的 linkedTemplate2.js，而 linkedTemplate2.json 儲存在相同的成品子資料夾中，則在 linkedTemplate.js中指定的 relativePath 就是 `linkedTemplate2.json` 。

1. 使用下列 JSON 建立主要範本。 將主要範本儲存為本機電腦 ** 上的azuredeploy.js** 。 本教學課程假設您已儲存至 **c:\Templates\linkedTS\azuredeploy.js的** 路徑，但您可以使用任何路徑。

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

1. 在儲存主要範本的資料夾中，建立稱為 **構件** 的目錄。
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

1. 將範本儲存為 **linkedTemplate.js** **在 [成品** ] 資料夾中。

## <a name="create-template-spec"></a>建立範本規格

範本規格會儲存在資源群組中。  建立資源群組，然後使用下列腳本建立範本規格。 範本規格名稱是 **webSpec**。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name templateSpecRG `
  -Location westus2

New-AzTemplateSpec `
  -Name webSpec `
  -Version "1.0.0.0" `
  -ResourceGroupName templateSpecRG `
  -Location westus2 `
  -TemplateFile "c:\Templates\linkedTS\azuredeploy.json"
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az group create \
  --name templateSpecRG \
  --location westus2

az ts create \
  --name webSpec \
  --version "1.0.0.0" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "c:\Templates\linkedTS\azuredeploy.json"
```

---

當您完成時，您可以從 Azure 入口網站或使用下列 Cmdlet 來查看範本規格：

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Get-AzTemplateSpec -ResourceGroupName templatespecRG -Name webSpec
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az ts show --name webSpec --resource-group templateSpecRG --version "1.0.0.0"
```

---

## <a name="deploy-template-spec"></a>部署範本規格

您現在可以部署範本規格。部署範本規格就如同部署其包含的範本，不同的是您會傳入範本規格的資源識別碼。您可以使用相同的部署命令，並視需要傳入範本規格的參數值。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name webRG `
  -Location westus2

$id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name webSpec -Version "1.0.0.0").Versions.Id

New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName webRG
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az group create \
  --name webRG \
  --location westus2

id = $(az template-specs show --name webSpec --resource-group templateSpecRG --version "1.0.0.0" --query "id")

az deployment group create \
  --resource-group webRG \
  --template-spec $id
```

> [!NOTE]
> 取得範本規格識別碼，並將其指派給 Windows PowerShell 中的變數時，發生已知的問題。

---

## <a name="next-steps"></a>後續步驟

若要瞭解如何將範本規格部署為連結的範本，請參閱 [教學課程：將範本規格部署為連結的範本](template-specs-deploy-linked-template.md)。
