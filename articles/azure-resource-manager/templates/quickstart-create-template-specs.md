---
title: 建立和部署範本規格
description: 了解如何從 ARM 範本建立範本規格。 然後，將範本規格部署至您訂用帳戶中的資源群組。
author: tfitzmac
ms.date: 12/14/2020
ms.topic: quickstart
ms.author: tomfitz
ms.openlocfilehash: 1d3e7c6ee6d19d4d2cd3828d5abf95ccb5457e76
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2020
ms.locfileid: "97511343"
---
# <a name="quickstart-create-and-deploy-template-spec-preview"></a>快速入門：建立和部署範本規格 (預覽)

本快速入門說明如何將 Azure Resource Manager 範本 (ARM 範本) 封裝成[範本規格](template-specs.md)。然後，您將部署該範本規格。您的範本規格包含會部署儲存體帳戶的 ARM 範本。

## <a name="prerequisites"></a>Prerequisites

具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

> [!NOTE]
> 範本規格目前為預覽狀態。 若要與 Azure PowerShell 搭配使用，您必須安裝 [5.0.0 版或更新版本](/powershell/azure/install-az-ps)。 若要與 Azure CLI 搭配使用，請使用 [2.14.2 版或更新版本](/cli/azure/install-azure-cli)。

## <a name="create-template"></a>建立範本

您可以從本機範本建立範本規格。 複製下列範本並將其儲存在名為 **azuredeploy.json** 的本機檔案。 本快速入門假設您已儲存至 **c:\Templates\azuredeploy.json** 的路徑，但是您可以使用任何路徑。

:::code language="json" source="~/quickstart-templates/101-storage-account-create/azuredeploy.json":::

## <a name="create-template-spec"></a>建立範本規格

範本規格是名為 `Microsoft.Resources/templateSpecs` 的資源類型。 若要建立範本規格，請使用 PowerShell、Azure CLI、入口網站或 ARM 範本。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 建立包含範本規格的新資源群組。

    ```azurepowershell
    New-AzResourceGroup `
      -Name templateSpecRG `
      -Location westus2
    ```

1. 在該資源群組中建立範本規格。 為新的範本規格指定 **storageSpec** 的名稱。

    ```azurepowershell
    New-AzTemplateSpec `
      -Name storageSpec `
      -Version "1.0" `
      -ResourceGroupName templateSpecRG `
      -Location westus2 `
      -TemplateFile "c:\Templates\azuredeploy.json"
    ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

1. 建立包含範本規格的新資源群組。

    ```azurecli
    az group create \
      --name templateSpecRG \
      --location westus2
    ```

1. 在該資源群組中建立範本規格。 為新的範本規格指定 **storageSpec** 的名稱。

    ```azurecli
    az ts create \
      --name storageSpec \
      --version "1.0" \
      --resource-group templateSpecRG \
      --location "westus2" \
      --template-file "c:\Templates\azuredeploy.json"
    ```

# <a name="portal"></a>[入口網站](#tab/azure-portal)

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 搜尋 **範本規格**。 從可用的選項中選取 [範本規格]。

    :::image type="content" source="./media/quickstart-create-template-specs/search-template-spec.png" alt-text="搜尋範本規格":::

1. 選取 [匯入範本]。

    :::image type="content" source="./media/quickstart-create-template-specs/import-template.png" alt-text="匯入範本":::

1. 選取資料夾圖示。

    :::image type="content" source="./media/quickstart-create-template-specs/open-folder.png" alt-text="開啟資料夾":::

1. 瀏覽至您儲存的本機範本，並加以選取。 選取 [開啟]。
1. 選取 [匯入]  。

    :::image type="content" source="./media/quickstart-create-template-specs/select-import.png" alt-text="選取匯入選項":::

1. 提供下列值：

    - **名稱**：輸入範本規格名稱。例如，**storageSpec**
    - **訂用帳戶**：選取用來建立範本規格的 Azure 訂用帳戶。
    - **資源群組**：選取 [新建] 並輸入新的資源群組名稱。  例如，**templateSpecRG**。
    - **位置**：選取資源群組的位置。 例如，**美國西部 2**。
    - **版本**：輸入範本規格的版本。使用 **1.0**。

1. 選取 [檢閱 + 建立]  。
1. 選取 [建立]。

# <a name="arm-template"></a>[ARM 範本](#tab/azure-resource-manager)

> [!NOTE]
> 建議您使用 PowerShell 或 CLI 來建立範本規格，而不是使用 ARM 範本。這些工具會自動將連結的範本轉換成連線到您主要範本的成品。 當您使用 ARM 範本來建立範本規格時，您必須手動將這些連結的範本新增為成品，而這可能會很複雜。

1. 當您使用 ARM 範本來建立範本規格時，此範本會內嵌於資源定義中。 您需要對本機範本進行一些變更。 複製下列範本並將其儲存在本機名為 **azuredeploy.json** 的檔案。

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

若要部署範本規格，請使用部署範本時要使用的相同部署命令。 傳入要部署的範本規格資源識別碼。

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
    id=$(az ts show --name storageSpec --resource-group templateSpecRG --version "1.0" --query "id")
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

# <a name="portal"></a>[入口網站](#tab/azure-portal)

1. 選取您建立的範本規格。

    :::image type="content" source="./media/quickstart-create-template-specs/select-template-spec.png" alt-text="選取範本規格":::

1. 選取 [部署]。

    :::image type="content" source="./media/quickstart-create-template-specs/deploy-template-spec.png" alt-text="部署範本規格":::

1. 提供下列值：

    - **訂用帳戶**：選取用來建立資源的 Azure 訂用帳戶。
    - **資源群組**：選取 [新建]，然後輸入 **storageRG**。
    - **儲存體帳戶類型**：選取 [Standard_GRS]。

1. 選取 [檢閱 + 建立]。
1. 選取 [建立]。

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

## <a name="update-template"></a>更新範本

假設您在範本規格中發現想要執行的範本變更。下列範本類似於您先前的範本，不同之處在於其針對儲存體帳戶名稱新增了前置詞。 複製下列範本，並更新您的 azuredeploy.json 檔案。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/templatespecs/azuredeploy2.json":::

## <a name="update-template-spec-version"></a>更新範本規格版本

將名為 `2.0` 的新版本新增至現有範本規格，而不是針對修改過的範本建立新範本規格。使用者可以選擇任一版本來進行部署。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 為範本規格建立新版本。

   ```powershell
   New-AzTemplateSpec `
     -Name storageSpec `
     -Version "2.0" `
     -ResourceGroupName templateSpecRG `
     -Location westus2 `
     -TemplateFile "c:\Templates\azuredeploy.json"
   ```

1. 若要部署新版本，請取得 `2.0` 版本的資源識別碼。

   ```powershell
   $id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name storageSpec -Version "2.0").Versions.Id
   ```

1. 部署該版本。 提供儲存體帳戶名稱的前置詞。

   ```powershell
   New-AzResourceGroupDeployment `
     -TemplateSpecId $id `
     -ResourceGroupName storageRG `
     -namePrefix "demoaccount"
   ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

1. 為範本規格建立新版本。

   ```azurecli
   az ts create \
     --name storageSpec \
     --version "2.0" \
     --resource-group templateSpecRG \
     --location "westus2" \
     --template-file "c:\Templates\azuredeploy.json"
   ```

1. 若要部署新版本，請取得 `2.0` 版本的資源識別碼。

   ```azurecli
   id=$(az ts show --name storageSpec --resource-group templateSpecRG --version "2.0" --query "id")
   ```

1. 部署該版本。 提供儲存體帳戶名稱的前置詞。

   ```azurecli
    az deployment group create \
      --resource-group storageRG \
      --template-spec $id \
      --parameters namePrefix='demoaccount'
    ```

# <a name="portal"></a>[入口網站](#tab/azure-portal)

1. 在您的範本規格中，選取 [建立新版本]。

   :::image type="content" source="./media/quickstart-create-template-specs/select-versions.png" alt-text="建立新版本":::

1. 將新版本命名為 `2.0` 並選擇性地新增附註。 選取 [編輯範本]。

   :::image type="content" source="./media/quickstart-create-template-specs/add-version-name.png" alt-text="命名新版本":::

1. 以您更新的範本取代範本內容。 選取 [檢閱並儲存]。
1. 選取 [儲存變更]。

1. 若要部署新版本，請選取 [版本]

   :::image type="content" source="./media/quickstart-create-template-specs/see-versions.png" alt-text="列出版本":::

1. 針對您想要部署的版本，選取三個點及 [部署]。

   :::image type="content" source="./media/quickstart-create-template-specs/deploy-version.png" alt-text="選取要部署的版本":::

1. 填寫欄位 (如同部署舊版時所執行的動作)。
1. 選取 [檢閱 + 建立]。
1. 選取 [建立]。

# <a name="arm-template"></a>[ARM 範本](#tab/azure-resource-manager)

1. 同樣地，您必須對本機範本進行一些變更，使其能夠使用範本規格。 複製下列範本並將其儲存在本機名為 azuredeploy.json 的檔案。

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
              "name": "2.0",
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
                    },
                    "namePrefix": {
                      "type": "string",
                      "maxLength": 11,
                      "defaultValue": "store",
                      "metadata": {
                        "description": "Prefix for storage account name"
                      }
                    }
                  },
                  "variables": {
                    "storageAccountName": "[[concat(parameters('namePrefix'), uniquestring(resourceGroup().id))]"
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

1. 若要將新版本新增至範本規格，請使用 Azure CLI 或 PowerShell 來部署您的範本。

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
              "id": "[resourceId('templateSpecRG', 'Microsoft.Resources/templateSpecs/versions', 'storageSpec', '2.0')]"
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

## <a name="clean-up-resources"></a>清除資源

若要清除您在本快速入門中部署的資源，請刪除您所建立的兩個資源群組。

1. 在 Azure 入口網站中，選取左側功能表中的 [資源群組]。

1. 在 [依名稱篩選] 欄位中輸入資源群組名稱 (templateSpecRG 和 storageRG)。

1. 選取資源群組名稱。

1. 從頂端功能表中選取 [刪除資源群組]。

## <a name="next-steps"></a>後續步驟

若要了解如何建立包含連結範本的範本規格，請參閱[建立連結範本的範本規格](template-specs-create-linked.md)。
