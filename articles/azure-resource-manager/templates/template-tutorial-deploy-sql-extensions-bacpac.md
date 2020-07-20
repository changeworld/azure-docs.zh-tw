---
title: 使用範本匯入 SQL BACPAC 檔案
description: 了解如何使用 Azure SQL Database 擴充功能透過 Azure Resource Manager 範本匯入 SQL BACPAC 檔案。
author: mumian
ms.date: 12/09/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 6a56602ad5217af07d9e35872a26ddb478146d0e
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86101880"
---
# <a name="tutorial-import-sql-bacpac-files-with-arm-templates"></a>教學課程：使用 ARM 範本匯入 SQL BACPAC 檔案

了解如何使用 Azure SQL Database 擴充功能透過 Azure Resource Manager (ARM) 範本匯入 BACPAC 檔案。 除了完成部署所需的主要範本檔案以外，部署成品可以是任何檔案。 BACPAC 檔案是成品。

在本教學課程中，您將建立一個範本來部署[邏輯 SQL 伺服器](../../azure-sql/database/logical-servers.md)和單一資料庫並匯入 BACPAC 檔案。 如需如何使用 ARM 範本部署 Azure 虛擬機器擴充功能的相關資訊，請參閱[教學課程：使用 ARM 範本部署虛擬機器擴充功能](./template-tutorial-deploy-vm-extensions.md)。

本教學課程涵蓋下列工作：

> [!div class="checklist"]
>
> * 準備 BACPAC 檔案。
> * 開啟快速入門範本。
> * 編輯範本。
> * 部署範本。
> * 驗證部署。

如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

若要完成本文，您需要：

* Visual Studio Cod 搭配 Resource Manager Tools 擴充功能。 請參閱[快速入門：使用 Visual Studio Code 建立 Azure Resource Manager 範本](./quickstart-create-templates-use-visual-studio-code.md)。
* 為了提高安全性，請使用為伺服器系統管理員帳戶產生的密碼。 以下是您可以用來產生密碼的範例：

    ```console
    openssl rand -base64 32
    ```

    Azure Key Vault 的設計訴求是保護加密金鑰和其他祕密。 如需詳細資訊，請參閱[教學課程：在 ARM 範本部署中整合 Azure Key Vault](./template-tutorial-use-key-vault.md)。 我們也建議您每三個月更新一次密碼。

## <a name="prepare-a-bacpac-file"></a>準備 BACPAC 檔案

已在 [GitHub](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac) 中分享 BACPAC 檔案。 若要自行建立，請參閱[將 Azure SQL Database 中的資料庫匯出到 BACPAC 檔案](../../azure-sql/database/database-export.md)。 如果您選擇將檔案發佈到自己的位置，您稍後必須在本教學課程中更新範本。

BACPAC 檔案必須先儲存在 Azure 儲存體帳戶中，才能使用 ARM 範本進行匯入。 下列 PowerShell 指令碼會使用這些步驟來準備 BACPAC 檔案：

* 下載 BACPAC 檔案。
* 建立 Azure 儲存體帳戶。
* 建立儲存體帳戶 Blob 容器。
* 將 BACPAC 檔案上傳至容器。
* 顯示儲存體帳戶金鑰和 Blob URL。

1. 選取 [試試看] 以開啟 Cloud Shell。 然後將以下 PowerShell 指令碼貼到 Shell 視窗中。

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

    $resourceGroupName = "${projectName}rg"
    $storageAccountName = "${projectName}store"
    $containerName = "bacpacfiles"
    $bacpacFileName = "SQLDatabaseExtension.bacpac"
    $bacpacUrl = "https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac"

    # Download the bacpac file
    Invoke-WebRequest -Uri $bacpacUrl -OutFile "$HOME/$bacpacFileName"

    # Create a resource group
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a storage account
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
                                           -Name $storageAccountName `
                                           -SkuName Standard_LRS `
                                           -Location $location
    $storageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName `
                                                  -Name $storageAccountName).Value[0]

    # Create a container
    New-AzStorageContainer -Name $containerName -Context $storageAccount.Context

    # Upload the BACPAC file to the container
    Set-AzStorageBlobContent -File $HOME/$bacpacFileName `
                             -Container $containerName `
                             -Blob $bacpacFileName `
                             -Context $storageAccount.Context

    Write-Host "The storage account key is $storageAccountKey"
    Write-Host "The BACPAC file URL is https://$storageAccountName.blob.core.windows.net/$containerName/$bacpacFileName"
    Write-Host "Press [ENTER] to continue ..."
    ```

1. 記下儲存體帳戶金鑰和 BACPAC 檔案 URL。 您在部署範本時需要這些值。

## <a name="open-a-quickstart-template"></a>開啟快速入門範本

此教學課程中使用的範本會儲存在 [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json) 中。

1. 在 Visual Studio Code 中，選取 [檔案] > [開啟檔案]。
1. 在 [檔案名稱] 中，貼上下列 URL：

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json
    ```

1. 選取 [開啟] 以開啟檔案。

    範本中定義了兩項資源：

   * 第 1 課：建立 Windows Azure 儲存體物件`Microsoft.Sql/servers`。 請參閱[範本參考](/azure/templates/microsoft.sql/servers)。
   * 第 1 課：建立 Windows Azure 儲存體物件`Microsoft.SQL.servers/databases`。 請參閱[範本參考](/azure/templates/microsoft.sql/servers/databases)。

        自訂範本之前，最好先對範本有初步了解。
1. 選取 [檔案] > [另存新檔]，以名稱 *azuredeploy.json* 將檔案的複本儲存至您的本機電腦。

## <a name="edit-the-template"></a>編輯範本

1. 在 **parameters** 區段的結尾新增兩個參數，以設定儲存體帳戶金鑰和 BACPAC URL。

    ```json
        "storageAccountKey": {
          "type":"string",
          "metadata":{
            "description": "Specifies the key of the storage account where the BACPAC file is stored."
          }
        },
        "bacpacUrl": {
          "type":"string",
          "metadata":{
            "description": "Specifies the URL of the BACPAC file."
          }
        }
    ```

    在 **adminPassword** 之後新增逗號。 若要從 Visual Studio Code 將 JSON 檔案格式化，請選取 Shift + Alt + F。

    若要取得這兩個值，請參閱[準備 BACPAC 檔案](#prepare-a-bacpac-file)。

1. 將兩個額外的資源新增至範本。

    * 若要讓 SQL Database 擴充功能匯入 BACPAC 檔案，您必須允許來自 Azure 服務的流量。 在伺服器定義底下新增下列防火牆規則定義：

        ```json
        "resources": [
          {
            "type": "firewallrules",
            "apiVersion": "2015-05-01-preview",
            "name": "AllowAllAzureIps",
            "location": "[parameters('location')]",
            "dependsOn": [
              "[parameters('databaseServerName')]"
            ],
            "properties": {
              "startIpAddress": "0.0.0.0",
              "endIpAddress": "0.0.0.0"
            }
          }
        ]
        ```

        範本外觀如下：

        ![具有防火牆規則定義的範本](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-firewall.png)

    * 使用下列 JSON，將 SQL Database 擴充功能資源新增至資料庫定義：

        ```json
        "resources": [
          {
            "type": "extensions",
            "apiVersion": "2014-04-01",
            "name": "Import",
            "dependsOn": [
              "[resourceId('Microsoft.Sql/servers/databases', parameters('databaseServerName'), parameters('databaseName'))]"
            ],
            "properties": {
              "storageKeyType": "StorageAccessKey",
              "storageKey": "[parameters('storageAccountKey')]",
              "storageUri": "[parameters('bacpacUrl')]",
              "administratorLogin": "[parameters('adminUser')]",
              "administratorLoginPassword": "[parameters('adminPassword')]",
              "operationMode": "Import"
            }
          }
        ]
        ```

        範本外觀如下：

        ![具有 SQL Database 擴充功能的範本](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac.png)

        若要了解資源定義，請參閱 [SQL Database 擴充功能參考](/azure/templates/microsoft.sql/servers/databases/extensions)。 以下是部分重要元素：

        * **dependsOn**：在資料庫建立後，才可建立擴充功能資源。
        * **storageKeyType**：指定要使用的儲存體金鑰類型。 這個值可以是 `StorageAccessKey` 或 `SharedAccessKey`。 在本教學課程中使用 `StorageAccessKey`。
        * **storageKey**：為 BACPAC 檔案儲存所在的儲存體帳戶指定金鑰。 如果儲存體金鑰類型為 `SharedAccessKey`，則前面必須加上 "?"。
        * **storageUri**：指定儲存體帳戶中所儲存 BACPAC 檔案的 URL。
        * **administratorLoginPassword**：SQL 系統管理員的密碼。 使用所產生的密碼。 請參閱[必要條件](#prerequisites)。

完成的範本看起來像這樣：

[!code-json[](~/resourcemanager-templates/tutorial-sql-extension/azuredeploy2.json?range=1-106&highlight=38-49,62-76,86-103)]

## <a name="deploy-the-template"></a>部署範本

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

請參閱[部署範本](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template)一節，以了解部署程序。 改用下列 PowerShell 部署指令碼：

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that is used earlier"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the SQL admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$storageAccountKey = Read-Host -Prompt "Enter the storage account key"
$bacpacUrl = Read-Host -Prompt "Enter the URL of the BACPAC file"
$resourceGroupName = "${projectName}rg"

#New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -TemplateFile "$HOME/azuredeploy.json" `
    -storageAccountKey $storageAccountKey `
    -bacpacUrl $bacpacUrl

Write-Host "Press [ENTER] to continue ..."
```

請考慮使用您在準備 BACPAC 檔案時所用的相同專案名稱，讓所有資源都儲存在相同的資源群組中。 如此一來，就能更輕鬆地管理資源工作，例如清除資源。 如果您使用相同的專案名稱，則可從指令碼中移除 `New-AzResourceGroup` 命令，或在系統詢問您是否要更新現有資源群組時回答是 (y) 或否 (n)。

使用所產生的密碼。 請參閱[必要條件](#prerequisites)。

## <a name="verify-the-deployment"></a>驗證部署

若要從您的用戶端電腦存取伺服器，您需要新增額外的防火牆規則。 如需詳細資訊，請參閱[建立和管理 IP 防火牆規則](../../azure-sql/database/firewall-configure.md#create-and-manage-ip-firewall-rules)。

在 Azure 入口網站中，請從新部署的資源群組中選取資料庫。 選取 [查詢編輯器 (預覽)]，然後輸入系統管理員認證。 您應該會看到兩個已匯入資料庫中的資料表。

![查詢編輯器 (預覽)](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>清除資源

不再需要 Azure 資源時，可藉由刪除資源群組來清除您所部署的資源。

1. 在 Azure 入口網站中，選取左側功能表中的 [資源群組]。
1. 在 [依名稱篩選] 欄位中輸入資源群組名稱。
1. 選取資源群組名稱。 您在資源群組中應該會看到共計六個資源。
1. 從頂端功能表中選取 [刪除資源群組]。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已部署伺服器和資料庫並匯入 BACPAC 檔案。 若要了解如何針對範本部署進行疑難排解，請參閱：

> [!div class="nextstepaction"]
> [對 ARM 範本部署進行疑難排解](./template-tutorial-troubleshoot.md)
