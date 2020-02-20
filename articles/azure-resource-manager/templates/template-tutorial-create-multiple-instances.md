---
title: 建立多個資源執行個體
description: 了解如何建立 Azure Resource Manager 範本以建立多個 Azure 資源執行個體。
author: mumian
ms.date: 03/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 91a37178f8dc8ecc3c61ca16f193e2e52c309d46
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209472"
---
# <a name="tutorial-create-multiple-resource-instances-with-resource-manager-templates"></a>教學課程：使用 Resource Manager 範本建立多個資源執行個體

了解如何在您的 Azure Resource Manager 範本中逐一查看以建立 Azure 資源的多個執行個體。 在本教學課程中，您將修改範本以建立三個儲存體帳戶執行個體。

![Azure Resource Manager 建立多個執行個體圖表](./media/template-tutorial-create-multiple-instances/resource-manager-template-create-multiple-instances-diagram.png)

本教學課程涵蓋下列工作：

> [!div class="checklist"]
> * 開啟快速入門範本
> * 編輯範本
> * 部署範本

如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>Prerequisites

若要完成本文，您需要：

* Visual Studio Code 搭配 Resource Manager Tools 擴充功能。 請參閱[使用 Visual Studio Code 建立 Azure Resource Manager 範本](use-vs-code-to-create-template.md)。

## <a name="open-a-quickstart-template"></a>開啟快速入門範本

[Azure 快速入門範本](https://azure.microsoft.com/resources/templates/)是 Resource Manager 範本的存放庫。 您可以尋找範例範本並加以自訂，而不要從頭建立範本。 本快速入門中使用的範本名為[建立標準儲存體帳戶](https://azure.microsoft.com/resources/templates/101-storage-account-create/)。 此範本會定義 Azure 儲存體帳戶資源。

1. 在 Visual Studio Code 中，選取 [檔案]  >[開啟檔案]  。
2. 在 [檔案名稱]  中，貼上下列 URL：

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. 選取 [開啟]  以開啟檔案。
4. 範本中有已定義的 'Microsoft.Storage/storageAccounts' 資源。 將範本與[範本參考](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts)相比較。 自訂範本之前，最好能初步了解範本。
5. 選取 [檔案]  >[另存新檔]  ，在您的本機電腦上將檔案另存為 **azuredeploy.json**。

## <a name="edit-the-template"></a>編輯範本

現有的範本會建立一個儲存體帳戶。 您會自訂範本以建立三個儲存體帳戶。

從 Visual Studio Code 進行下列四個變更：

![Azure Resource Manager 建立多個執行個體](./media/template-tutorial-create-multiple-instances/resource-manager-template-create-multiple-instances.png)

1. 新增 `copy` 元素到儲存體帳戶資源定義。 在複製元素中，您可以指定反覆運算次數以及此迴圈的變數。 計數值必須為不超過 800 的正整數。
2. `copyIndex()` 函式會傳回迴圈中的目前反覆項目。 您可以使用索引作為名稱前置詞。 `copyIndex()`是以零為基礎。 若要位移索引值，您可以傳遞 copyIndex() 函式中的值。 例如，*copyIndex(1)* 。
3. 刪除 **variables** 元素，因為已不再使用它。
4. 刪除 **outputs** 元素。 已不再需要此項目。

完成的範本看起來像這樣：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "apiVersion": "2018-02-01",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": 3
      }
    }
  ]
}
```

如需有關建立多個執行個體的詳細資訊，請參閱[在 Azure Resource Manager 範本中部署資源或屬性的多個執行個體](./copy-resources.md)

## <a name="deploy-the-template"></a>部署範本

請參閱 Visual Studio Code 快速入門中的[部署範本](quickstart-create-templates-use-visual-studio-code.md#deploy-the-template)一節，以取得部署程序。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

若要列出全部三個儲存體帳戶，請省略 --name 參數：

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az storage account list --resource-group $resourceGroupName
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Get-AzStorageAccount -ResourceGroupName $resourceGroupName
```

---

比較儲存體帳戶名稱與範本中的名稱定義。

## <a name="clean-up-resources"></a>清除資源

不再需要 Azure 資源時，可藉由刪除資源群組來清除您所部署的資源。

1. 在 Azure 入口網站中，選取左側功能表中的 [資源群組]  。
2. 在 [依名稱篩選]  欄位中輸入資源群組名稱。
3. 選取資源群組名稱。  您在資源群組中應該會看到共計六個資源。
4. 從頂端功能表中選取 [刪除資源群組]  。

## <a name="next-steps"></a>後續步驟

在此教學課程中，您已學到如何建立多個儲存體帳戶執行個體。  在下一個教學課程中，您會開發具有多個資源與多個資源類型的的範本。 某些資源有相依的資源。

> [!div class="nextstepaction"]
> [建立相依資源](./template-tutorial-create-templates-with-dependent-resources.md)
