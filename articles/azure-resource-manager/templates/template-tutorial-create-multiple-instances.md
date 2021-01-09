---
title: 建立多個資源執行個體
description: 了解如何建立 Azure Resource Manager 範本 (ARM 範本) 以建立多個 Azure 資源執行個體。
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: e669e27547633639a88674ffee499fb1d84facdf
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2020
ms.locfileid: "97673948"
---
# <a name="tutorial-create-multiple-resource-instances-with-arm-templates"></a>教學課程：使用 ARM 範本建立多個資源執行個體

了解如何在您的 Azure Resource Manager 範本 (ARM 範本) 中逐一查看以建立 Azure 資源的多個執行個體。 在本教學課程中，您將修改範本以建立三個儲存體帳戶執行個體。

![Azure Resource Manager 建立多個執行個體圖表](./media/template-tutorial-create-multiple-instances/resource-manager-template-create-multiple-instances-diagram.png)

本教學課程涵蓋下列工作：

> [!div class="checklist"]
> * 開啟快速入門範本
> * 編輯範本
> * 部署範本

如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

對於涵蓋資源複本的 Microsoft Learn 模組，請參閱[使用進階 ARM 範本功能管理複雜的雲端部署](/learn/modules/manage-deployments-advanced-arm-template-features/)。

## <a name="prerequisites"></a>必要條件

若要完成本文，您需要：

* Visual Studio Code 搭配 Resource Manager Tools 擴充功能。 請參閱[快速入門：使用 Visual Studio Code 建立 ARM 範本](quickstart-create-templates-use-visual-studio-code.md)。

## <a name="open-a-quickstart-template"></a>開啟快速入門範本

[Azure 快速入門範本](https://azure.microsoft.com/resources/templates/)是 ARM 範本的存放庫。 您可以尋找範例範本並加以自訂，而不要從頭建立範本。 本快速入門中使用的範本名為[建立標準儲存體帳戶](https://azure.microsoft.com/resources/templates/101-storage-account-create/)。 此範本會定義 Azure 儲存體帳戶資源。

1. 在 Visual Studio Code 中，選取 [檔案] > [開啟檔案]。
1. 在 [檔案名稱] 中，貼上下列 URL：

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

1. 選取 [開啟] 以開啟檔案。
1. 範本中定義的資源為 `Microsoft.Storage/storageAccounts`。 將範本與[範本參考](/azure/templates/Microsoft.Storage/storageAccounts)相比較。 自訂範本之前，最好能初步了解範本。
1. 選取 [檔案] > [另存新檔]，在您的本機電腦上將檔案另存為 _azuredeploy.json_。

## <a name="edit-the-template"></a>編輯範本

現有的範本會建立一個儲存體帳戶。 您會自訂範本以建立三個儲存體帳戶。

從 Visual Studio Code 進行下列四個變更：

![Azure Resource Manager 建立多個執行個體](./media/template-tutorial-create-multiple-instances/resource-manager-template-create-multiple-instances.png)

1. 新增 `copy` 元素到儲存體帳戶資源定義。 在 `copy` 元素中，您可以指定反覆運算次數以及此迴圈的變數。 計數值必須為不超過 800 的正整數。
2. `copyIndex()` 函式會傳回迴圈中的目前反覆項目。 您可以使用索引作為名稱前置詞。 `copyIndex()`是以零為基礎。 若要位移索引值，您可以傳遞 `copyIndex()` 函式中的值。 例如： `copyIndex(1)` 。
3. 刪除 `variables` 元素，因為已不再使用它。
4. 刪除 `outputs` 元素。 已不再需要此項目。

完成的範本看起來像這樣：

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
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "copy": {
        "name": "storagecopy",
        "count": 3
      },
      "properties": {}
    }
  ]
}
```

如需建立多個執行個體的詳細資訊，請參閱 [ARM 範本中的資源反覆項目](./copy-resources.md)

## <a name="deploy-the-template"></a>部署範本

1. 登入 [Azure Cloud Shell](https://shell.azure.com)

1. 藉由選取左上角的 **PowerShell** 或 **Bash** (適用於 CLI) 來選擇您慣用的環境。 切換時必須重新啟動殼層。

    ![Azure 入口網站的 Cloud Shell 上傳檔案](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. 選取 [上傳/下載檔案]，然後選取 [上傳]。 請參閱上一個螢幕擷取畫面。 選取您在前一節中儲存的檔案。 上傳檔案之後，您可以使用 `ls` 命令和 `cat` 命令來確認檔案是否已成功上傳。

1. 從 Cloud Shell 執行下列命令。 選取要顯示 PowerShell 程式碼或 CLI 程式碼的索引標籤。

    # <a name="cli"></a>[CLI](#tab/CLI)

    ```azurecli
    echo "Enter a project name that is used to generate resource group name:" &&
    read projectName &&
    echo "Enter the location (i.e. centralus):" &&
    read location &&
    resourceGroupName="${projectName}rg" &&
    az group create --name $resourceGroupName --location "$location" &&
    az deployment group create --resource-group $resourceGroupName --template-file "$HOME/azuredeploy.json"
    ```

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json"
    ```

    ---

成功部署範本之後，您可以顯示在指定資源群組中建立的三個儲存體帳戶。 比較儲存體帳戶名稱與範本中的名稱定義。

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
echo "Enter a project name that is used to generate resource group name:" &&
read projectName &&
resourceGroupName="${projectName}rg" &&
az storage account list --resource-group $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
$resourceGroupName = "${projectName}rg"

Get-AzStorageAccount -ResourceGroupName $resourceGroupName
Write-Host "Press [ENTER] to continue ..."
```

---

## <a name="clean-up-resources"></a>清除資源

不再需要 Azure 資源時，可藉由刪除資源群組來清除您所部署的資源。

1. 在 Azure 入口網站中，選取左側功能表中的 [資源群組]  。
2. 在 [依名稱篩選]  欄位中輸入資源群組名稱。
3. 選取資源群組名稱。  您在資源群組中應該會看到共計三個資源。
4. 從頂端功能表中選取 [刪除資源群組]  。

## <a name="next-steps"></a>後續步驟

在此教學課程中，您已學到如何建立多個儲存體帳戶執行個體。 在下一個教學課程中，您會開發具有多個資源與多個資源類型的範本。 某些資源有相依的資源。

> [!div class="nextstepaction"]
> [建立相依資源](./template-tutorial-create-templates-with-dependent-resources.md)
