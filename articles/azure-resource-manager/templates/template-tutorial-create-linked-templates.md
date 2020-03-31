---
title: 建立連結的範本
description: 了解如何建立連結的 Azure Resource Manager 範本以用於建立虛擬機器
author: mumian
ms.date: 12/03/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: e1cce566fb7aab286c57f32d9348e51dd0a7c1ee
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/25/2020
ms.locfileid: "80239327"
---
# <a name="tutorial-create-linked-arm-templates"></a>教學課程：建立連結的 ARM 範本

了解如何建立連結的 Azure Resource Manager (ARM) 範本。 使用連結的範本，您可以讓一個範本呼叫另一個範本。 此功能非常適合用來將範本模組化時。 在此教學課程中，您會使用與在[教學課程：建立具有相依資源的 ARM 範本](./template-tutorial-create-templates-with-dependent-resources.md)中使用的相同範本，以建立虛擬機器、虛擬網路與其他相依資源，包括儲存體帳戶。 您會將儲存體帳戶資源建立分散到連結的範本。

呼叫連結的範本就像進行函式呼叫一樣。  您也會了解如何將參數值傳遞給連結的範本，以及如何從連結的範本取得「傳回值」。

本教學課程涵蓋下列工作：

> [!div class="checklist"]
> * 開啟快速入門範本
> * 建立連結的範本
> * 上傳連結的範本
> * 連結到連結的範本
> * 設定相依性
> * 部署範本
> * 其他做法

如需詳細資訊，請參閱[在部署 Azure 資源時使用連結與巢狀的範本](./linked-templates.md)。

如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisites

若要完成本文，您需要：

* Visual Studio Code 搭配 Resource Manager Tools 擴充功能。 請參閱[使用 Visual Studio Code 建立 ARM 範本](use-vs-code-to-create-template.md)。
* 為了提高安全性，請使用為虛擬機器系統管理員帳戶產生的密碼。 以下是用於產生密碼的範例：

    ```console
    openssl rand -base64 32
    ```

    Azure Key Vault 的設計訴求是保護加密金鑰和其他祕密。 如需詳細資訊，請參閱[教學課程：在 ARM 範本部署中整合 Azure Key Vault](./template-tutorial-use-key-vault.md)。 我們也建議您每三個月更新一次密碼。

## <a name="open-a-quickstart-template"></a>開啟快速入門範本

Azure 快速入門範本是 ARM 範本的存放庫。 您可以尋找範例範本並加以自訂，而不要從頭建立範本。 本教學課程中使用的範本名為[部署簡單的 Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/)。 這是[教學課程：建立具有相依資源的 ARM 範本](./template-tutorial-create-templates-with-dependent-resources.md)。 您儲存相同範本的兩個複本以做為：

* **主範本**：建立所有資源，儲存體帳戶除外。
* **連結的範本**：建立儲存體帳戶。

1. 在 Visual Studio Code 中，選取 [檔案]  >[開啟檔案]  。
1. 在 [檔案名稱]  中，貼上下列 URL：

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. 選取 [開啟]  以開啟檔案。
1. 範本中定義了六項資源：

   * [`Microsoft.Storage/storageAccounts`](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts)
   * [`Microsoft.Network/publicIPAddresses`](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses)
   * [`Microsoft.Network/networkSecurityGroups`](https://docs.microsoft.com/azure/templates/microsoft.network/networksecuritygroups)
   * [`Microsoft.Network/virtualNetworks`](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks)
   * [`Microsoft.Network/networkInterfaces`](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces)
   * [`Microsoft.Compute/virtualMachines`](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines)

     在自訂範本之前，先對範本結構描述有一些基本了解會相當有幫助。
1. 選取 [檔案]  >[另存新檔]  ，以名稱 **azuredeploy.json** 將檔案的複本儲存至您的本機電腦。
1. 選取 [檔案]  >[另存新檔]  以使用名稱 **linkedTemplate.json** 建立該檔案的複本。

## <a name="create-the-linked-template"></a>建立連結的範本

連結的範本會建立儲存體帳戶。 連結的範本可用來作為獨立範本以建立儲存體帳戶。 在本教學課程中，連結的範本會接受兩個參數，然後將一個值傳回給主要範本。 這個「傳回」值是在 `outputs` 元素中定義的。

1. 在 Visual Studio Code 中開啟 **linkedTemplate.json** (如果尚未開啟此檔案)。
1. 進行下列變更：

    * 移除 **location** 以外的所有參數。
    * 新增稱為 **storageAccountName** 的參數。

      ```json
      "storageAccountName":{
        "type": "string",
        "metadata": {
            "description": "Azure Storage account name."
        }
      },
      ```

      儲存體帳戶名稱和位置會以參數形式從主要範本傳遞給連結的範本。

    * 移除 **variables** 元素及所有的變數定義。
    * 移除儲存體帳戶以外的所有資源。 您必須移除總共四個資源。
    * 將儲存體帳戶資源的 **name** 元素值更新為：

        ```json
          "name": "[parameters('storageAccountName')]",
        ```

    * 更新 **outputs** 元素，讓它看起來像這樣：

        ```json
        "outputs": {
          "storageUri": {
              "type": "string",
              "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
            }
        }
        ```

       主範本中的虛擬機器資源定義需要 **storageUri**。  您必須將該值傳遞回主範本做為輸出值。

        當您完成時，範本看起來應該像這樣：

        ```json
        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "storageAccountName": {
              "type": "string",
              "metadata": {
                "description": "Azure Storage account name."
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
              "apiVersion": "2018-11-01",
              "name": "[parameters('storageAccountName')]",
              "location": "[parameters('location')]",
              "sku": {
                "name": "Standard_LRS"
              },
              "kind": "Storage",
              "properties": {}
            }
          ],
          "outputs": {
            "storageUri": {
              "type": "string",
              "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
            }
          }
        }
        ```

1. 儲存變更。

## <a name="upload-the-linked-template"></a>上傳連結的範本

主要範本和連結的範本必須可從您執行部署的位置存取。 在此教學課程中，您使用如同您在[教學課程：建立具有相依資源的 ARM 範本](./template-tutorial-create-templates-with-dependent-resources.md)。 主範本 (azuredeploy.json) 已上傳到殼層。 連結的範本 (linkedTemplate.json) 必須在某處安全地共用。 下列 PowerShell 指令碼會建立 Azure 儲存體帳戶、將範本上傳至儲存體帳戶，接著產生 SAS 權杖以授與範本檔案的有限存取權。 為了簡化教學課程，此指令碼會從 GitHub 存放庫下載完整的連結範本。 如果您想要使用您所建立的連結範本，可以使用 [Cloud Shell](https://shell.azure.com) 來上傳連結的範本，然後將指令碼修改為使用連結的範本。

> [!NOTE]
> 指令碼會限制在 8 小時內使用 SAS 權杖。 如果您需要更多時間才能完成本教學課程，請增加到期時間。

```azurepowershell-interactive
$projectNamePrefix = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"

$resourceGroupName = $projectNamePrefix + "rg"
$storageAccountName = $projectNamePrefix + "store"
$containerName = "linkedtemplates" # The name of the Blob container to be created.

$linkedTemplateURL = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-linked-templates/linkedStorageAccount.json" # A completed linked template used in this tutorial.
$fileName = "linkedStorageAccount.json" # A file name used for downloading and uploading the linked template.

# Download the tutorial linked template
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
New-AzStorageContainer -Name $containerName -Context $context

# Upload the linked template
Set-AzStorageBlobContent `
    -Container $containerName `
    -File "$home/$fileName" `
    -Blob $fileName `
    -Context $context

# Generate a SAS token
$templateURI = New-AzStorageBlobSASToken `
    -Context $context `
    -Container $containerName `
    -Blob $fileName `
    -Permission r `
    -ExpiryTime (Get-Date).AddHours(8.0) `
    -FullUri

Write-Host "You need the following values later in the tutorial:"
Write-Host "Resource Group Name: $resourceGroupName"
Write-Host "Linked template URI with SAS token: $templateURI"
Write-Host "Press [ENTER] to continue ..."
```

1. 選取 [試試看]  綠色按鈕，以開啟 Azure Cloud Shell 窗格。
2. 選取 [複製]  來複製 PowerShell 指令碼。
3. 以滑鼠右鍵按一下 Shell 窗格內的任何位置 (深藍色的部分)，然後選取 [貼上]  。
4. 請記下 Shell 窗格結尾的兩個值 (資源群組名稱和連結的範本 URI)。 在本教學課程後續的內容中，您會需要這些值。
5. 選取 [結束焦點模式]  以關閉 Shell 窗格。

實際上，您會在部署主要範本時產生 SAS 權杖，並提供 SAS 權杖較小的到期時間範圍，讓它更安全。 如需詳細資訊，請參閱[在部署期間提供 SAS 權杖](./secure-template-with-sas-token.md#provide-sas-token-during-deployment)。

## <a name="call-the-linked-template"></a>呼叫連結的範本

主範本的名稱是 azuredeploy.json。

1. 在 Visual Studio Code 中開啟 **azuredeploy.json** (若尚未開啟)。
1. 將儲存體帳戶資源定義取代為下列 json 程式碼片段：

    ```json
    {
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "properties": {
          "mode": "Incremental",
          "templateLink": {
              "uri":""
          },
          "parameters": {
              "storageAccountName":{"value": "[variables('storageAccountName')]"},
              "location":{"value": "[parameters('location')]"}
          }
      }
    },
    ```

    請注意這些詳細資料：

    * 主範本中的 `Microsoft.Resources/deployments` 資源會用來連結到另一個範本。
    * `deployments` 資源的名稱是 `linkedTemplate`。 此名稱會用於[設定相依性](#configure-dependency)。
    * 呼叫連結的範本時，您只能使用[增量](./deployment-modes.md)部署模式。
    * `templateLink/uri` 包含連結的範本 URI。 將此值更新為您上傳連結的範本 (具有 SAS 權杖的範本) 時取得的 URI。
    * 使用 `parameters` 將值從主範本傳遞到連結的範本。
1. 確定您已將 `uri` 元素的值更新為您上傳連結的範本 (具有 SAS 權杖的範本) 時取得的值。 實際上，您想要提供具有參數的 URI。
1. 儲存修改過的範本

## <a name="configure-dependency"></a>設定相依性

回想一下[教學課程：建立具有相依資源的 ARM 範本](./template-tutorial-create-templates-with-dependent-resources.md)，虛擬機器資源相依於儲存體帳戶：

![Azure Resource Manager 範本相依性圖表](./media/template-tutorial-create-linked-templates/resource-manager-template-visual-studio-code-dependency-diagram.png)

因為現在儲存體帳戶是在連結的範本中所定義，您必須更新下列 `Microsoft.Compute/virtualMachines` 資源的兩個元素。

* 重新設定 `dependsOn` 元素。 儲存體帳戶定義已從連結的範本移除。
* 重新設定 `properties/diagnosticsProfile/bootDiagnostics/storageUri` 元素。 在 [建立連結的範本](#create-the-linked-template) 中，您已新增輸出值：

    ```json
    "outputs": {
        "storageUri": {
            "type": "string",
            "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
            }
    }
    ```

    主範本需要此值。

1. 在 Visual Studio Code 中開啟 azuredeploy.json (若尚未開啟)。
2. 展開虛擬機器資源定義並更新**dependsOn**，如下列螢幕擷取化驗所示：

    ![Azure Resource Manager 連結的範本設定相依性](./media/template-tutorial-create-linked-templates/resource-manager-template-linked-templates-configure-dependency.png)

    linkedTemplate  是部署資源的名稱。
3. 更新 **properties/diagnosticsProfile/bootDiagnostics/storageUri**，如上一個螢幕擷取畫面所示。
4. 儲存修改過的範本。

## <a name="deploy-the-template"></a>部署範本

請參閱[部署範本](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template)一節，以了解部署程序。 使用與儲存體帳戶相同的資源群組名稱來儲存連結的範本。 它可讓您更輕鬆地清除下一節中的資源。 為了提高安全性，請使用為虛擬機器系統管理員帳戶產生的密碼。 請參閱[必要條件](#prerequisites)。

## <a name="clean-up-resources"></a>清除資源

不再需要 Azure 資源時，可藉由刪除資源群組來清除您所部署的資源。

1. 在 Azure 入口網站中，選取左側功能表中的 [資源群組]  。
2. 在 [依名稱篩選]  欄位中輸入資源群組名稱。
3. 選取資源群組名稱。  您在資源群組中應該會看到共計六個資源。
4. 從頂端功能表中選取 [刪除資源群組]  。

## <a name="additional-practice"></a>其他做法

若要改善專案，請對完成的專案進行下列其他變更：

1. 修改主要範本 (azuredeploy.json)，使其透過參數取得連結的範本 URI 值。
2. 您不會在上傳連結的範本時產生 SAS 權杖，而是在部署主要範本產生該權杖。 如需詳細資訊，請參閱[在部署期間提供 SAS 權杖](./secure-template-with-sas-token.md#provide-sas-token-during-deployment)。

## <a name="next-steps"></a>後續步驟

在本教學課程，您已將某個範本模組化成為主要範本和連結的範本。 若要了解如何使用虛擬機器擴充功能來執行部署後工作，請參閱：

> [!div class="nextstepaction"]
> [部署虛擬機器延伸模組](./template-tutorial-deploy-vm-extensions.md)
