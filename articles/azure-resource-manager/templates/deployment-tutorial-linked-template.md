---
title: 教學課程 - 部署連結的範本
description: 了解如何部署連結的範本
ms.date: 03/13/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: a90bb90c90206ffe00f8b4f2d035c0ea844b5c47
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/01/2020
ms.locfileid: "91611668"
---
# <a name="tutorial-deploy-a-linked-template"></a>教學課程：部署連結的範本

在[前面的教學課程](./deployment-tutorial-local-template.md)中，您已了解如何部署儲存在本機電腦中的範本。 若要部署複雜的解決方案，您可以將一個範本拆解成許多範本，然後透過主要範本來部署這些拆解出來的範本。 在本教學課程中，您將了解如何部署包含連結範本參考的主要範本。 主要範本在進行部署時會觸發連結範本的部署。 您也會了解如何使用 SAS 權杖來儲存及保護連結的範本。 完成此教學課程大約需要 **12 分鐘**。

## <a name="prerequisites"></a>Prerequisites

建議您完成上一個教學課程，但這並非必要條件。

## <a name="review-template"></a>檢閱範本

在前面的教學課程中，您部署了範本來建立儲存體帳戶、App Service 方案和 Web 應用程式。 所使用的範本是：

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/local-template/azuredeploy.json":::

## <a name="create-a-linked-template"></a>建立連結的範本

您可以將儲存體帳戶資源分散到連結的範本中：

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/linkedStorageAccount.json":::

下列範本是主要範本。  醒目提示的 **Microsoft.Resources/deployments** 物件會顯示如何呼叫連結的範本。 連結的範本無法儲存為本機檔案，也無法儲存為只能在區域網路上使用的檔案。 您可以只提供 URI 值，其中包含 *http* 或 *https*。 Resource Manager 必須能夠存取範本。 有一個選項是將連結的範本放在儲存體帳戶中，並將 URI 用於該項目。 URI 會透過參數傳遞至範本。 請參閱醒目提示的參數定義。

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/azuredeploy.json" highlight="27-32,40-58":::

以副檔名. json 將主要範本的複本儲存到本機電腦，例如 azuredeploy.json。 您不需要儲存連結範本的複本。  連結的範本會從 GitHub 存放庫複製到儲存體帳戶。

## <a name="store-the-linked-template"></a>儲存連結的範本

下列 PowerShell 指令碼會建立儲存體帳戶、建立容器，並將連結的範本從 GitHub 存放庫複製到容器。 [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json) 中會儲存連結範本的複本。

選取 [試試看]  以開啟 Cloud Shell，選取 [複製]  以複製 PowerShell 指令碼，然後以滑鼠右鍵按一下殼層窗格以貼上指令碼：

> [!IMPORTANT]
> 儲存體帳戶名稱必須介於 3 到 24 個字元的長度，而且只能使用數字和小寫字母。 名稱必須是唯一的。 在範本中，儲存體帳戶名稱是附加了 "store" 的專案名稱，且專案名稱的長度必須介於 3 到 11 個字元之間。 因此，專案名稱必須符合儲存體帳戶名稱需求，且少於 11 個字元。

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"

$resourceGroupName = $projectName + "rg"
$storageAccountName = $projectName + "store"
$containerName = "templates" # The name of the Blob container to be created.

$linkedTemplateURL = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json" # A completed linked template used in this tutorial.
$fileName = "linkedStorageAccount.json" # A file name used for downloading and uploading the linked template.

# Download the template
Invoke-WebRequest -Uri $linkedTemplateURL -OutFile "$home/$fileName"

# Create a resource group
New-AzResourceGroup -Name $resourceGroupName -Location $location

# Create a storage account
$storageAccount = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -Location $location `
    -SkuName "Standard_LRS"

$context = $storageAccount.Context

# Create a container
New-AzStorageContainer -Name $containerName -Context $context -Permission Container

# Upload the template
Set-AzStorageBlobContent `
    -Container $containerName `
    -File "$home/$fileName" `
    -Blob $fileName `
    -Context $context

Write-Host "Press [ENTER] to continue ..."
```

## <a name="deploy-template"></a>部署範本

若要在儲存體帳戶中部署私人範本，請產生 SAS Token 並將它包含在範本的 URI 中。 設定到期時間，以允許足夠的時間來完成部署。 僅有帳戶擁有者可以存取包含範本的 Blob。 不過，當您建立 Blob 的 SAS Token 時，具備該 URI 的任何人都可以存取該 Blob。 如果另一位使用者攔截了 URI，該使用者也能存取範本。 SAS Token 是限制存取範本的好方法，但您不應該將機密資料 (如密碼) 直接包含在範本中。

如果您尚未建立資源群組，請參閱[建立資源群組](./deployment-tutorial-local-template.md#create-resource-group)。

> [!NOTE]
> 在下列 Azure CLI 程式碼中，資料參數 -d 會是 macOS 中無效的引數。 因此若 macOS 的使用者，要在 macOS 上的終端機新增 2 小時到目前的時間，應該使用 -v+2H。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell

$projectName = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$templateFile = Read-Host -Prompt "Enter the main template file and path"

$resourceGroupName="${projectName}rg"
$storageAccountName="${projectName}store"
$containerName = "templates"
$fileName = "linkedStorageAccount.json" # A file name used for downloading and uploading the linked template.

$key = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName).Value[0]
$context = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $key

# Generate a SAS token
$linkedTemplateUri = New-AzStorageBlobSASToken `
    -Context $context `
    -Container $containerName `
    -Blob $fileName `
    -Permission r `
    -ExpiryTime (Get-Date).AddHours(2.0) `
    -FullUri

# Deploy the template
New-AzResourceGroupDeployment `
  -Name DeployLinkedTemplate `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile $templateFile `
  -projectName $projectName `
  -linkedTemplateUri $linkedTemplateUri `
  -verbose
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli

echo "Enter a project name that is used to generate resource names:"
read projectName
echo "Enter the main template file:"
read templateFile

resourceGroupName="${projectName}rg"
storageAccountName="${projectName}store"
containerName="templates"
fileName="linkedStorageAccount.json"

key=$(az storage account keys list -g $resourceGroupName -n $storageAccountName --query [0].value -o tsv)

linkedTemplateUri=$(az storage blob generate-sas \
  --account-name $storageAccountName \
  --account-key $key \
  --container-name $containerName \
  --name $fileName \
  --permissions r \
  --expiry `date -u -d "120 minutes" '+%Y-%m-%dT%H:%MZ'` \
  --full-uri)

linkedTemplateUri=$(echo $linkedTemplateUri | sed 's/"//g')
az deployment group create \
  --name DeployLinkedTemplate \
  --resource-group $resourceGroupName \
  --template-file $templateFile \
  --parameters projectName=$projectName linkedTemplateUri=$linkedTemplateUri \
  --verbose
```

---

## <a name="clean-up-resources"></a>清除資源

您可以藉由刪除資源群組來清除您所部署的資源。

1. 在 Azure 入口網站中，選取左側功能表中的 [資源群組]  。
2. 在 [依名稱篩選]  欄位中輸入資源群組名稱。
3. 選取資源群組名稱。
4. 從頂端功能表中選取 [刪除資源群組]  。

## <a name="next-steps"></a>後續步驟

您已了解如何部署連結的範本。 在下一個教學課程中，您將了解如何建立 DevOp 管線來部署範本。

> [!div class="nextstepaction"]
> [建立管線](./deployment-tutorial-pipeline.md)
