---
title: 將範本規格部署為連結的範本
description: 瞭解如何在連結的部署中部署現有的範本規格。
ms.topic: conceptual
ms.date: 07/20/2020
ms.openlocfilehash: 5d4824ea432d804418fda2cdc90d49154d496722
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87096144"
---
# <a name="tutorial-deploy-a-template-spec-as-a-linked-template-preview"></a>教學課程：將範本規格部署為連結的範本（預覽）

瞭解如何使用[連結的部署](linked-templates.md#linked-template)來部署現有的[範本規格](template-specs.md)。 您可以使用範本規格，與組織中的其他使用者共用 ARM 範本。 建立範本規格之後，您可以使用 Azure PowerShell 來部署範本規格。 您也可以使用連結的範本，將範本規格部署為方案的一部分。

## <a name="prerequisites"></a>必要條件

具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

> [!NOTE]
> 範本規格目前為預覽狀態。 若要加以使用，您必須[註冊預覽版](https://aka.ms/templateSpecOnboarding)。

## <a name="create-a-template-spec"></a>建立範本規格

遵循[快速入門：建立及部署範本規格](quickstart-create-template-specs.md)，以建立用於部署儲存體帳戶的範本規格。 在下一節中，您需要範本規格、範本規格名稱和範本規格版本的資源組名。

## <a name="create-the-main-template"></a>建立主要範本

若要在 ARM 範本中部署範本規格，請將[部署資源](/azure/templates/microsoft.resources/deployments)新增至您的主要範本。 在 `templateLink` 屬性中，指定範本規格的資源識別碼。建立具有下列 JSON 的範本，其**在上稱為azuredeploy.js**。 本教學課程假設您已儲存至的路徑**c:\Templates\deployTS\azuredeploy.js** ，但您可以使用任何路徑。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    },
    "tsResourceGroup":{
      "type": "string",
      "metadata": {
        "Description": "Specifies the resource group name of the template spec."
      }
    },
    "tsName": {
      "type": "string",
      "metadata": {
        "Description": "Specifies the name of the template spec."
      }
    },
    "tsVersion": {
      "type": "string",
      "defaultValue": "1.0.0.0",
      "metadata": {
        "Description": "Specifies the version the template spec."
      }
    },
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "metadata": {
        "Description": "Specifies the storage account type required by the template spec."
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
          "id": "[resourceId(parameters('tsResourceGroup'), 'Microsoft.Resources/templateSpecs/versions', parameters('tsName'), parameters('tsVersion'))]"
        },
        "parameters": {
          "storageAccountType": {
            "value": "[parameters('storageAccountType')]"
          }
        }
      }
    }
  ],
  "outputs": {
    "templateSpecId": {
      "type": "string",
      "value": "[resourceId(parameters('tsResourceGroup'), 'Microsoft.Resources/templateSpecs/versions', parameters('tsName'), parameters('tsVersion'))]"
    }
  }
}
```

範本規格識別碼是使用函數產生的 [`resourceID()`](template-functions-resource.md#resourceid) 。 如果 templateSpec 位於目前部署的相同資源群組中，則 resourceID （）函數中的資源群組引數是選擇性的。  您也可以直接傳入資源識別碼做為參數。 若要取得識別碼，請使用：

```azurepowershell-interactive
$id = (Get-AzTemplateSpec -ResourceGroupName $resourceGroupName -Name $templateSpecName -Version $templateSpecVersion).Version.Id
```

將參數傳遞給範本規格的語法如下：

```json
"parameters": {
  "storageAccountType": {
    "value": "[parameters('storageAccountType')]"
  }
}
```

> [!NOTE]
> 的 apiVersion `Microsoft.Resources/deployments` 必須是2020-06-01 或更新版本。

## <a name="deploy-the-template"></a>部署範本

當您部署連結的範本時，它會同時部署 web 應用程式和儲存體帳戶。 部署與部署其他 ARM 範本相同。

```azurepowershell
New-AzResourceGroup `
  -Name webRG `
  -Location westus2

New-AzResourceGroupDeployment `
  -ResourceGroupName webRG `
  -TemplateFile "c:\Templates\deployTS\azuredeploy.json"
```

## <a name="next-steps"></a>後續步驟

若要瞭解如何建立包含連結範本的範本規格，請參閱[建立連結範本的範本規格](template-specs-create-linked.md)。
