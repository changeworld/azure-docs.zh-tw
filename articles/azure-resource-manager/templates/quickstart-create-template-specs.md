---
title: 建立和部署範本規格
description: 了解如何從 ARM 範本建立範本規格。 然後，將範本規格部署至您訂用帳戶中的資源群組。
author: tfitzmac
ms.date: 11/17/2020
ms.topic: quickstart
ms.author: tomfitz
ms.openlocfilehash: 8439b1de5a69b3e5bfc22e10f089938da921c1cb
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2020
ms.locfileid: "94747497"
---
# <a name="quickstart-create-and-deploy-template-spec-preview"></a>快速入門：建立和部署範本規格 (預覽)

本快速入門說明如何將 Azure Resource Manager 範本 (ARM 範本) 封裝成[範本規格](template-specs.md)，然後部署該範本規格。您的範本規格包含會部署儲存體帳戶的 ARM 範本。

## <a name="prerequisites"></a>Prerequisites

具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

> [!NOTE]
> 範本規格目前為預覽狀態。 若要加以使用，您必須安裝最新版的 PowerShell 或 Azure CLI。 針對 Azure PowerShell，請使用 [5.0.0 版或更新版本](/powershell/azure/install-az-ps)。 針對 Azure CLI，請使用 [2.14.2 版或更新版本](/cli/azure/install-azure-cli)。

## <a name="create-template-spec"></a>建立範本規格

範本規格是名為 **Microsoft.Resources/templateSpecs** 的資源類型。 若要建立您的範本規格，您可以使用 Azure PowerShell、Azure CLI 或 ARM 範本。 在所有選項中，您都需要封裝在範本規格內的 ARM 範本。

使用 PowerShell 和 CLI 時，ARM 範本會當做參數傳入命令。 使用 ARM 範本時，要封裝至範本規格內的 ARM 範本會內嵌在範本規格定義中。

下面會說明這些選項。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 當您使用 PowerShell 建立範本規格時，可以傳入本機範本。 複製下列範本並將其儲存在名為 **azuredeploy.json** 的本機檔案。 本快速入門假設您已儲存至 **c:\Templates\azuredeploy.json** 的路徑，但是您可以使用任何路徑。

    :::code language="json" source="~/quickstart-templates/101-storage-account-create/azuredeploy.json":::

1. 建立包含範本規格的新資源群組。

    ```azurepowershell
    New-AzResourceGroup `
      -Name templateSpecRG `
      -Location westus2
    ```

1. 然後，在該資源群組中建立範本規格。 您可以為新的範本規格指定 **storageSpec** 的名稱。

    ```azurepowershell
    New-AzTemplateSpec `
      -Name storageSpec `
      -Version "1.0" `
      -ResourceGroupName templateSpecRG `
      -Location westus2 `
      -TemplateFile "c:\Templates\azuredeploy.json"
    ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

1. 當您使用 CLI 建立範本規格時，可以傳入本機範本。 複製下列範本並將其儲存在名為 **azuredeploy.json** 的本機檔案。 本快速入門假設您已儲存至 **c:\Templates\azuredeploy.json** 的路徑，但是您可以使用任何路徑。

    :::code language="json" source="~/quickstart-templates/101-storage-account-create/azuredeploy.json":::

1. 建立包含範本規格的新資源群組。

    ```azurecli
    az group create \
      --name templateSpecRG \
      --location westus2
    ```

1. 然後，在該資源群組中建立範本規格。 您可以為新的範本規格指定 **storageSpec** 的名稱。

    ```azurecli
    az ts create \
      --name storageSpec \
      --version "1.0" \
      --resource-group templateSpecRG \
      --location "westus2" \
      --template-file "c:\Templates\azuredeploy.json"
    ```

# <a name="arm-template"></a>[ARM 範本](#tab/azure-resource-manager)

1. 當您使用 ARM 範本來建立範本規格時，此範本會內嵌於資源定義中。 複製下列範本並將其儲存在本機名為 **azuredeploy.json** 的檔案。 本快速入門假設您已儲存至 **c:\Templates\azuredeploy.json** 的路徑，但是您可以使用任何路徑。

    > [!NOTE]
    > 在內嵌的範本中，所有[範本運算式](template-expressions.md)都必須以第二個左括弧進行逸出。 使用 `"[[`，而不是 `"[`。 JSON 陣列仍會使用單一左括弧。

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "functions": [],
      "variables": {},
      "resources": [
        {
          "type": "Microsoft.Resources/templateSpecs",
          "apiVersion": "2019-06-01-preview",
          "name": "storageSpec",
          "location": "westus2",
          "properties": {
            "displayName": "Storage template spec"
          },
          "tags": {},
          "resources": [
            {
              "type": "versions",
              "apiVersion": "2019-06-01-preview",
              "name": "1.0",
              "location": "westus2",
              "dependsOn": [ "storageSpec" ],
              "properties": {
                "template": {
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
                      "defaultValue": "[[resourceGroup().location]",
                      "metadata": {
                        "description": "Location for all resources."
                      }
                    }
                  },
                  "variables": {
                    "storageAccountName": "[[concat('store', uniquestring(resourceGroup().id))]"
                  },
                  "resources": [
                    {
                      "type": "Microsoft.Storage/storageAccounts",
                      "apiVersion": "2019-04-01",
                      "name": "[[variables('storageAccountName')]",
                      "location": "[[parameters('location')]",
                      "sku": {
                        "name": "[[parameters('storageAccountType')]"
                      },
                      "kind": "StorageV2",
                      "properties": {}
                    }
                  ],
                  "outputs": {
                    "storageAccountName": {
                      "type": "string",
                      "value": "[[variables('storageAccountName')]"
                    }
                  }
                }
              },
              "tags": {}
            }
          ]
        }
      ],
      "outputs": {}
    }
    ```

1. 使用 Azure CLI 或 PowerShell 來建立新的資源群組。

    ```azurepowershell
    New-AzResourceGroup `
      -Name templateSpecRG `
      -Location westus2
    ```

    ```azurecli
    az group create \
      --name templateSpecRG \
      --location westus2
    ```

1. 使用 Azure CLI 或 PowerShell 部署您的範本。

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -ResourceGroupName templateSpecRG `
      -TemplateFile "c:\Templates\azuredeploy.json"
    ```

    ```azurecli
    az deployment group create \
      --name templateSpecRG \
      --template-file "c:\Templates\azuredeploy.json"
    ```

---

## <a name="deploy-template-spec"></a>部署範本規格

您現在可以部署範本規格。部署範本規格就如同部署其包含的範本，不同的是您會傳入範本規格的資源識別碼。您可以使用相同的部署命令，並視需要傳入範本規格的參數值。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 建立資源群組以包含新的儲存體帳戶。

    ```azurepowershell
    New-AzResourceGroup `
      -Name storageRG `
      -Location westus2
    ```

1. 取得範本規格的資源識別碼。

    ```azurepowershell
    $id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name storageSpec -Version "1.0").Versions.Id
    ```

1. 部署範本規格。

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -TemplateSpecId $id `
      -ResourceGroupName storageRG
    ```

1. 您可以提供與 ARM 範本所用參數完全相同的參數。 使用儲存體帳戶類型的參數重新部署範本規格。

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -TemplateSpecId $id `
      -ResourceGroupName storageRG `
      -storageAccountType Standard_GRS
    ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

1. 建立資源群組以包含新的儲存體帳戶。

    ```azurecli
    az group create \
      --name storageRG \
      --location westus2
    ```

1. 取得範本規格的資源識別碼。

    ```azurecli
    id = $(az ts show --name storageSpec --resource-group templateSpecRG --version "1.0" --query "id")
    ```

    > [!NOTE]
    > 取得範本規格識別碼，並將其指派給 Windows PowerShell 中的變數時，發生已知的問題。

1. 部署範本規格。

    ```azurecli
    az deployment group create \
      --resource-group storageRG \
      --template-spec $id
    ```

1. 您可以提供與 ARM 範本所用參數完全相同的參數。 使用儲存體帳戶類型的參數重新部署範本規格。

    ```azurecli
    az deployment group create \
      --resource-group storageRG \
      --template-spec $id \
      --parameters storageAccountType='Standard_GRS'
    ```

# <a name="arm-template"></a>[ARM 範本](#tab/azure-resource-manager)

1. 複製下列範本並將其儲存在名為 **storage.json** 的本機檔案。

    ```json
       {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "functions": [],
      "variables": {},
      "resources": [
        {
          "type": "Microsoft.Resources/deployments",
          "apiVersion": "2020-06-01",
          "name": "demo",
          "properties": {
            "templateLink": {
              "id": "[resourceId('templateSpecRG', 'Microsoft.Resources/templateSpecs/versions', 'storageSpec', '1.0')]"
            },
            "parameters": {
            },
            "mode": "Incremental"
          }
        }
      ],
      "outputs": {}
    }
    ```

1. 使用 Azure CLI 或 PowerShell 為儲存體帳戶建立新的資源群組。

    ```azurepowershell
    New-AzResourceGroup `
      -Name storageRG `
      -Location westus2
    ```

    ```azurecli
    az group create \
      --name storageRG \
      --location westus2
    ```

1. 使用 Azure CLI 或 PowerShell 部署您的範本。

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -ResourceGroupName storageRG `
      -TemplateFile "c:\Templates\storage.json"
    ```

    ```azurecli
    az deployment group create \
      --name storageRG \
      --template-file "c:\Templates\storage.json"
    ```

---

## <a name="grant-access"></a>授與存取權

如果您想要讓貴組織中的其他使用者部署您的範本規格，您必須授與他們讀取權限。 您可以將「讀者」角色指派給資源群組的 Azure AD 群組，其中包含您想要共用的範本規格。 如需詳細資訊，請參閱[教學課程：使用 Azure PowerShell 為群組授與 Azure 資源的存取權](../../role-based-access-control/tutorial-role-assignments-group-powershell.md)。

## <a name="clean-up-resources"></a>清除資源

若要清除您在本快速入門中部署的資源，請刪除您所建立的兩個資源群組。

1. 在 Azure 入口網站中，選取左側功能表中的 [資源群組]。

1. 在 [依名稱篩選] 欄位中輸入資源群組名稱 (templateSpecRG 和 storageRG)。

1. 選取資源群組名稱。

1. 從頂端功能表中選取 [刪除資源群組]。

## <a name="next-steps"></a>後續步驟

若要了解如何建立包含連結範本的範本規格，請參閱[建立連結範本的範本規格](template-specs-create-linked.md)。
