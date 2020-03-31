---
title: 保護範本中的成品
description: 了解如何保護 Azure Resource Manager 範本中使用的成品。
author: mumian
ms.date: 12/09/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: ad6ea3c68ed6f48ac48bbbdafed7f8660df23937
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/25/2020
ms.locfileid: "80239218"
---
# <a name="tutorial-secure-artifacts-in-arm-template-deployments"></a>教學課程：保護 ARM 範本部署中的成品

了解如何使用 Azure 儲存體帳戶搭配共用存取簽章 (SAS)，保護 Azure Resource Manager (ARM) 範本使用的成品。 除了完成部署所需的主要範本檔案以外，部署成品可以是任何檔案。 例如，在[教學課程：使用 ARM 範本匯入 SQL BACPAC 檔案](./template-tutorial-deploy-sql-extensions-bacpac.md)中，主要範本會建立 Azure SQL Database 執行個體。 也會呼叫 BACPAC 檔案以建立資料表及插入資料。 BACPAC 檔案是儲存在 Azure 儲存體帳戶中的成品。 您可以使用儲存體帳戶金鑰來存取該成品。

在本教學課程中，您會使用 SAS 在您自己的 Azure 儲存體帳戶中授與 BACPAC 檔案的有限存取權。 如需關於 SAS 的詳細資訊，請參閱[使用共用存取簽章 (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md)。

若要了解如何保護連結的範本，請參閱[教學課程：建立連結的 ARM 範本](./template-tutorial-create-linked-templates.md)。

本教學課程涵蓋下列工作：

> [!div class="checklist"]
> * 準備 BACPAC 檔案。
> * 開啟現有範本。
> * 編輯範本。
> * 部署範本。
> * 驗證部署。

如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>Prerequisites

若要完成本文，您需要：

* Visual Studio Code 搭配 Resource Manager Tools 擴充功能。 請參閱[使用 Visual Studio Code 建立 ARM 範本](./use-vs-code-to-create-template.md)。
* 檢閱[本教學課程：使用 ARM 範本匯入 SQL BACPAC 檔案](./template-tutorial-deploy-sql-extensions-bacpac.md)。 本教學課程中使用的範本就是該教學課程中開發的範本。 本文會提供完整範本的下載連結。
* 為了提高安全性，請使用為 SQL Server 系統管理員帳戶產生的密碼。 以下是您可以用來產生密碼的範例：

    ```console
    openssl rand -base64 32
    ```

    Azure Key Vault 的設計訴求是保護加密金鑰和其他祕密。 如需詳細資訊，請參閱[教學課程：在 ARM 範本部署中整合 Azure Key Vault](./template-tutorial-use-key-vault.md)。 我們也建議您每三個月更新一次密碼。

## <a name="prepare-a-bacpac-file"></a>準備 BACPAC 檔案

在本節中，您會準備 BACPAC 檔案，以便在部署 ARM 範本時安全地存取該檔案。 本節有五個程序︰

* 下載 BACPAC 檔案。
* 建立 Azure 儲存體帳戶。
* 建立儲存體帳戶 Blob 容器。
* 將 BACPAC 檔案上傳至容器。
* 擷取 BACPAC 檔案的 SAS 權杖。

1. 選取 [試試看]  以開啟 Cloud Shell。 然後將以下 PowerShell 指令碼貼到 Shell 視窗中。

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name"   # This name is used to generate names for Azure resources, such as storage account name.
    $location = Read-Host -Prompt "Enter a location (i.e. centralus)"

    $resourceGroupName = $projectName + "rg"
    $storageAccountName = $projectName + "store"
    $containerName = "bacpacfile" # The name of the Blob container to be created.

    $bacpacURL = "https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac"
    $bacpacFileName = "SQLDatabaseExtension.bacpac" # A file name used for downloading and uploading the BACPAC file.

    # Download the bacpac file
    Invoke-WebRequest -Uri $bacpacURL -OutFile "$home/$bacpacFileName"

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
    New-AzStorageContainer -Name $containerName -Context $context

    # Upload the bacpac file
    Set-AzStorageBlobContent `
        -Container $containerName `
        -File "$home/$bacpacFileName" `
        -Blob $bacpacFileName `
        -Context $context

    # Generate a SAS token
    $bacpacURI = New-AzStorageBlobSASToken `
        -Context $context `
        -Container $containerName `
        -Blob $bacpacFileName `
        -Permission r `
        -ExpiryTime (Get-Date).AddHours(8.0) `
        -FullUri

    $str = $bacpacURI.split("?")

    Write-Host "You need the blob url and the SAS token later in the tutorial:"
    Write-Host $str[0]
    Write-Host (-join ("?", $str[1]))

    Write-Host "Press [ENTER] to continue ..."
    ```

1. 記下 BACPAC 檔案 URL 和 SAS 權杖。 您在部署範本時需要這些值。

## <a name="open-an-existing-template"></a>開啟現有範本

在這個課程中，您會修改在[教學課程：使用 ARM 範本匯入 SQL BACPAC 檔案](./template-tutorial-deploy-sql-extensions-bacpac.md)中建立的範本，以呼叫具有 SAS 權杖的 BACPAC 檔案。 在 SQL 擴充功能教學課程中開發的範本會在 [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json) 中共用。

1. 在 Visual Studio Code 中，選取 [檔案]   > [開啟檔案]  。
1. 在 [檔案名稱]  中，貼上下列 URL：

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy2.json
    ```

1. 選取 [開啟]  以開啟檔案。

    範本中定義了四個資源：

   * 第 1 課：建立 Windows Azure 儲存體物件`Microsoft.Sql/servers`。 請參閱[範本參考](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers)。
   * 第 1 課：建立 Windows Azure 儲存體物件`Microsoft.SQL/servers/firewallRules`。 請參閱[範本參考](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers/firewallrules)。
   * 第 1 課：建立 Windows Azure 儲存體物件`Microsoft.SQL/servers/databases`。 請參閱[範本參考](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases)。
   * 第 1 課：建立 Windows Azure 儲存體物件`Microsoft.SQL/server/databases/extensions`。 請參閱[範本參考](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions)。

        自訂範本之前，最好先對範本有初步了解。
1. 選取 [檔案]   > [另存新檔]  ，以名稱 *azuredeploy.json* 將檔案的複本儲存至您的本機電腦。

## <a name="edit-the-template"></a>編輯範本

1. 將 storageAccountKey 參數定義取代為下列參數定義：

    ```json
        "_artifactsLocationSasToken": {
          "type": "securestring",
          "metadata": {
            "description": "Specifies the SAS token required to access the artifact location."
          }
        },
    ```

    ![Resource Manager 教學課程的安全成品參數](./media/template-tutorial-secure-artifacts/resource-manager-tutorial-secure-artifacts-parameters.png)

1. 更新 SQL 擴充功能資源的下列三個元素值：

    ```json
    "storageKeyType": "SharedAccessKey",
    "storageKey": "[parameters('_artifactsLocationSasToken')]",
    "storageUri": "[parameters('bacpacUrl')]",
    ```

完成的範本看起來像這樣：

[!code-json[](~/resourcemanager-templates/tutorial-sql-extension/azuredeploy3.json?range=1-106&highlight=38-43,95-97)]

## <a name="deploy-the-template"></a>部署範本

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

請參閱[部署範本](./template-tutorial-create-multiple-instances.md#deploy-the-template)一節，以了解部署程序。 改用下列 PowerShell 部署指令碼。

```azurepowershell
$projectName = Read-Host -Prompt "Enter the project name that is used earlier"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the sql database admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$bacpacUrl = Read-Host -Prompt "Enter the BACPAC url"
$artifactsLocationSasToken = Read-Host -Prompt "Enter the artifacts location SAS token" -AsSecureString

$resourceGroupName = $projectName + "rg"

#New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -_artifactsLocationSasToken $artifactsLocationSasToken `
    -bacpacUrl $bacpacUrl `
    -TemplateFile "$HOME/azuredeploy.json"

Write-Host "Press [ENTER] to continue ..."
```

使用所產生的密碼。 請參閱[必要條件](#prerequisites)。
如需 _artifactsLocation、_artifactsLocationSasToken 和 bacpacFileName 的值，請參閱[準備 BACPAC 檔案](#prepare-a-bacpac-file)。

## <a name="verify-the-deployment"></a>驗證部署

在入口網站中，請從新部署的資源群組中選取 SQL 資料庫。 選取 [查詢編輯器 (預覽)]  ，然後輸入系統管理員認證。 您應該會看到兩個已匯入資料庫中的資料表。

![查詢編輯器 (預覽)](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>清除資源

不再需要 Azure 資源時，可藉由刪除資源群組來清除您所部署的資源。

1. 在 Azure 入口網站中，選取左側功能表中的 [資源群組]  。
2. 在 [依名稱篩選]  欄位中輸入資源群組名稱。
3. 選取資源群組名稱。 您在資源群組中應該會看到共計六個資源。
4. 從頂端功能表中選取 [刪除資源群組]  。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已使用 SAS 權杖部署 SQL 伺服器和 SQL 資料庫並匯入 BACPAC 檔案。 若要了解如何跨多個區域部署 Azure 資源，以及如何使用安全的部署實務，請參閱

> [!div class="nextstepaction"]
> [使用安全部署做法](./deployment-manager-tutorial.md)
